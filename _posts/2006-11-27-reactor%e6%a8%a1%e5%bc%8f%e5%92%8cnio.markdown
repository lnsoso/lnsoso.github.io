---
layout: post
title: Reactor模式和NIO
author: Alvin
date: !binary |-
  MjAwNi0xMS0yNyAxNjo1MTo1OSArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMS0yNyAwODo1MTo1OSArMDgwMA==
---
转至 www.jdon.com
注: 理解ACE中的Reactor模式,  高效的响应IO等事件.
当前分布式计算　Web Services盛行天下，这些网络服务的底层都离不开对socket的操作。他们都有一个共同的结构：
1. Read request
2. Decode request
3. Process service
4. Encode reply
5. Send reply
经典的网络服务的设计如下图，在每个线程中完成对数据的处理：
 
但这种模式在用户负载增加时，性能将下降非常的快。我们需要重新寻找一个新的方案，保持数据处理的流畅，很显然，事件触发机制是最好的解决办法，当有事件发生时，会触动handler,然后开始数据的处理。
Reactor模式类似于AWT中的Event处理：
 
<strong>Reactor模式参与者</strong>
1.Reactor 负责响应IO事件，一旦发生，广播发送给相应的Handler去处理,这类似于AWT的thread
2.Handler 是负责非堵塞行为，类似于AWT ActionListeners；同时负责将handlers与event事件绑定，类似于AWT                      addActionListener
如图：
<img width="400" height="174" src="http://www.jdon.com/concurrent/images/reactor.jpg" alt="" /> 
Java的NIO为reactor模式提供了实现的基础机制，它的Selector当发现某个channel有数据时，会通过SlectorKey来告知我们，在此我们实现事件和handler的绑定。
我们来看看Reactor模式代码:
<table width="100%" cellspacing="0" cellpadding="0" border="0" bgcolor="#cccccc">
<tbody>
<tr>
<td>
            public class Reactor implements Runnable{
 　　final Selector selector;
            final ServerSocketChannel serverSocket;
            
　　Reactor(int port) throws IOException {
            selector = Selector.open();
            serverSocket = ServerSocketChannel.open();
            InetSocketAddress address = new InetSocketAddress(InetAddress.getLocalHost(),port);
            serverSocket.socket().bind(address);
            
            serverSocket.configureBlocking(false);
            //向selector注册该channel
            SelectionKey sk =serverSocket.register(selector,SelectionKey.OP_ACCEPT);
            
            logger.debug("-->Start serverSocket.register!");
            
            //利用sk的attache功能绑定Acceptor 如果有事情，触发Acceptor
            sk.attach(new Acceptor());
            logger.debug("-->attach(new Acceptor()!");
            }
            public void run() { // normally in a new Thread
            try {
            while (!Thread.interrupted())
            {
            selector.select();
            Set selected = selector.selectedKeys();
            Iterator it = selected.iterator();
            //Selector如果发现channel有OP_ACCEPT或READ事件发生，下列遍历就会进行。
            while (it.hasNext())
            //来一个事件 第一次触发一个accepter线程
            //以后触发SocketReadHandler
            dispatch((SelectionKey)(it.next()));
            selected.clear();
            }
            }catch (IOException ex) {
            logger.debug("reactor stop!"+ex);
            }
            }
 　　//运行Acceptor或SocketReadHandler
            void dispatch(SelectionKey k) {
            Runnable r = (Runnable)(k.attachment());
            if (r != null){
            // r.run();
 　　　　}
            } 
 　　class Acceptor implements Runnable { // inner
            public void run() {
            try {
            logger.debug("-->ready for accept!");
            SocketChannel c = serverSocket.accept();
            if (c != null)
            //调用Handler来处理channel
            new SocketReadHandler(selector, c);
            }
            catch(IOException ex) {
            logger.debug("accept stop!"+ex);
            }
            }
            }
            }
                        </td>        </tr>    </tbody></table>
以上代码中巧妙使用了SocketChannel的attach功能，将Hanlder和可能会发生事件的channel链接在一起，当发生事件时，可以立即触发相应链接的Handler。
再看看Handler代码:
<table width="100%" cellspacing="0" cellpadding="0" border="0" bgcolor="#cccccc">
<tbody>
<tr>
<td>public class SocketReadHandler implements Runnable {
 　　public static Logger logger = Logger.getLogger(SocketReadHandler.class);
 　　private Test test=new Test();
 　　final SocketChannel socket;
            final SelectionKey sk;
            
            static final int READING = 0, SENDING = 1;
            int state = READING;
 　　public SocketReadHandler(Selector sel, SocketChannel                            c)
            throws IOException {
 　　　　socket = c;
 　　　　socket.configureBlocking(false);
            sk = socket.register(sel, 0);
 　　　　//将SelectionKey绑定为本Handler 下一步有事件触发时，将调用本类的run方法。
            //参看dispatch(SelectionKey k)
            sk.attach(this);
            
            //同时将SelectionKey标记为可读，以便读取。
            sk.interestOps(SelectionKey.OP_READ);
            sel.wakeup();
            }
 　　public void run() {
            try{
            // test.read(socket,input);
            readRequest() ;
            }catch(Exception ex){
            logger.debug("readRequest error"+ex);
            }
            }
            /**
            * 处理读取data
            * @param key
            * @throws Exception
            */
            private void readRequest() throws Exception {
 　　ByteBuffer input = ByteBuffer.allocate(1024);
            input.clear();
            try{
 　　　　int bytesRead = socket.read(input);
　　　　...... 
　　　　//激活线程池 处理这些request
            requestHandle(new Request(socket,btt));
　　　　.....
            }catch(Exception e) {
            }
            
            }            </td>        </tr>    </tbody></table>
注意在Handler里面又执行了一次attach，这样，覆盖前面的Acceptor，下次该Handler又有READ事件发生时，将直接触发Handler.从而开始了数据的读　处理　写　发出等流程处理。
将数据读出后，可以将这些数据处理线程做成一个线程池，这样，数据读出后，立即扔到线程池中，这样加速处理速度：
<img width="500" height="332" src="http://www.jdon.com/concurrent/images/pool.jpg" alt="" />
更进一步，我们可以使用多个Selector分别处理连接和读事件。
一个高性能的Java网络服务机制就要形成，激动人心的集群并行计算即将实现。
