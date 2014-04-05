---
layout: post
title: 使用memcached进行内存缓存
author: gavinkwoe
date: !binary |-
  MjAwNi0xMS0xNSAxNToyNDo1NCArMDgwMA==
date_gmt: !binary |-
  MjAwNi0xMS0xNSAwNzoyNDo1NCArMDgwMA==
---
通常的网页缓存方式有动态缓存和静态缓存等几种，在ASP.NET中已经可以实现对页面局部进行缓存，而使用memcached的缓存比ASP.NET的局部缓存更加灵活，可以缓存任意的对象，不管是否在页面上输出。而memcached最大的优点是可以分布式的部署，这对于大规模应用来说也是必不可少的要求。
LiveJournal.com使用了memcached在前端进行缓存，取得了良好的效果，而像wikipedia,sourceforge等也采用了或即将采用memcached作为缓存工具。memcached可以大规模网站应用发挥巨大的作用。
<div id="a000041more">
<div id="more">
<strong>Memcached是什么?</strong>
Memcached是高性能的，分布式的内存对象缓存系统，用于在动态应用中减少数据库负载，提升访问速度。
Memcached由Danga Interactive开发，用于提升LiveJournal.com访问速度的。LJ每秒动态页面访问量几千次，用户700万。Memcached将数据库负载大幅度降低，更好的分配资源，更快速访问。
<strong>如何使用memcached-Server端?</strong>
在服务端运行：
# ./memcached -d -m 2048 -l 10.0.0.40 -p 11211
这将会启动一个占用2G内存的进程，并打开11211端口用于接收请求。由于32位系统只能处理4G内存的寻址，所以在大于4G内存使用PAE的32位服务器上可以运行2-3个进程，并在不同端口进行监听。
<strong>如何使用memcached-Client端?</strong>
在应用端包含一个用于描述Client的Class后，就可以直接使用，非常简单。
PHP Example:
$options["servers"] = array("192.168.1.41:11211", "192.168.1.42:11212");
$options["debug"] = false;
$memc = new MemCachedClient($options);
$myarr = array("one","two", 3);
$memc->set("key_one", $myarr);
$val = $memc->get("key_one");
print $val[0]."\n"; // prints 'one&lsquo;
print $val[1]."\n"; // prints 'two&lsquo;
print $val[2]."\n"; // prints 3
<strong>为什么不使用数据库做这些？</strong>
暂且不考虑使用什么样的数据库(MS-SQL, Oracle, Postgres, MysQL-InnoDB, etc..), 实现事务(ACID，Atomicity, Consistency, Isolation, and Durability )需要大量开销，特别当使用到硬盘的时候，这就意味着查询可能会阻塞。当使用不包含事务的数据库（例如Mysql-MyISAM），上面的开销不存在，但读线程又可能会被写线程阻塞。
Memcached从不阻塞，速度非常快。
<strong>为什么不使用共享内存?</strong>
最初的缓存做法是在线程内对对象进行缓存，但这样进程间就无法共享缓存，命中率非常低，导致缓存效率极低。后来出现了共享内存的缓存，多个进程或者线程共享同一块缓存，但毕竟还是只能局限在一台机器上，多台机器做相同的缓存同样是一种资源的浪费，而且命中率也比较低。
Memcached Server和Clients共同工作，实现跨服务器分布式的全局的缓存。并且可以与Web Server共同工作，Web Server对CPU要求高，对内存要求低，Memcached Server对CPU要求低，对内存要求高，所以可以搭配使用。
<strong>Mysql 4.x的缓存怎么样?</strong>
Mysql查询缓存不是很理想，因为以下几点：
当指定的表发生更新后，查询缓存会被清空。在一个大负载的系统上这样的事情发生的非常频繁，导致查询缓存效率非常低，有的情况下甚至还不如不开，因为它对cache的管理还是会有开销。
在32位机器上，Mysql对内存的操作还是被限制在4G以内，但memcached可以分布开，内存规模理论上不受限制。
Mysql上的是查询缓存，而不是对象缓存，如果在查询后还需要大量其它操作，查询缓存就帮不上忙了。
如果要缓存的数据不大，并且查询的不是非常频繁，这样的情况下可以用Mysql 查询缓存，不然的话memcached更好。
<strong>数据库同步怎么样？</strong>
这里的数据库同步是指的类似Mysql Master-Slave模式的靠日志同步实现数据库同步的机制。
你可以分布读操作，但无法分布写操作，但写操作的同步需要消耗大量的资源，而且这个开销是随着slave服务器的增长而不断增长的。
下一步是要对数据库进行水平切分，从而让不同的数据分布到不同的数据库服务器组上，从而实现分布的读写，这需要在应用中实现根据不同的数据连接不同的数据库。
当这一模式工作后（我们也推荐这样做），更多的数据库导致更多的让人头疼的硬件错误。
Memcached可以有效的降低对数据库的访问，让数据库用主要的精力来做不频繁的写操作，而这是数据库自己控制的，很少会自己阻塞 自己。
<strong>Memcached快吗？</strong>
非常快，它使用libevent，可以应付任意数量打开的连接（使用epoll，而非poll），使用非阻塞网络IO，分布式散列对象到不同的服务器，查询复杂度是O(1)。
参考资料：
<a href="http://www.linuxjournal.com/article/7451">Distributed Caching with Memcached | Linux Journal</a>
<a href="http://www.danga.com/">http://www.danga.com/</a>
<a href="http://www.linuxjournal.com/article/7451">http://www.linuxjournal.com/article/7451</a>
转载自:http://www.example.net.cn/archives/2006/01/eoamemcachedoea.html</div></div>
