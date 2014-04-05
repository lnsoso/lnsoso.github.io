---
layout: post
title: What's New in Python 2.5
author: gavinkwoe
date: !binary |-
  MjAwNi0wOS0wMSAxODozMzoyNSArMDgwMA==
date_gmt: !binary |-
  MjAwNi0wOS0wMSAxMDozMzoyNSArMDgwMA==
---
1.1. 新的,改进的及不再赞成使用的模块
象以前一样, Python 标准库变得更强更健壮. 这里列出了大部分值得注意的变化, 按模块名字母序排列. 请参考源代码树中的 Misc/NEWS 文件以了解更完整的变化列表, 或者通过 SVN 日志了解所有的细节. 
gc 模块新增了一个 get_count() 函数. 它返回一个 3-元素 tuple: 内容是当前三个 GC 生成器的垃圾收集数目. 这是垃圾收集器的统计信息,当这个数值到达一个指定值, 就会执行一个清扫动作.原有的 gc.collect() 函数现在接受一个可选的 生成器 参数 ( 0, 1, 或 2 )以指定由哪个 生成器 进行收集. 
heapq 模块中的 nsmallest() 和 nlargest() 函数现在支持一个新的关键字参数以提供类似 min()/max() 和 sort() 的功能. 这里有一个例子: 
      >>> import heapq
      >>> L = ["short", 'medium', 'longest', 'longer still']
      >>> heapq.nsmallest(2, L)  # Return two lowest elements, lexicographically
      ['longer still', 'longest']
      >>> heapq.nsmallest(2, L, key=len)   # Return two shortest elements
      ['short', 'medium']
      (贡献者: Raymond Hettinger .)
itertools.islice() 函数现在接受 None 作为 start 和 step 参数. 这使得它与 slice 对象更兼容. 你现在能够象下面这样写代码了.: 
s = slice(5) # 创建一个 slice 对象 itertools.islice(iterable, s.start, s.stop, s.step) (贡献者: Raymond Hettinger .) 
operator 模块的 itemgetter() 和 attrgetter() 函数现在支持多个域了. 类似 operator.attrgetter('a', 'b') 会返回一个拥有 a 和 b 属性的函数经.结合这个新特性及 sort() 方法的 key 参数,你能够很容易的使用多个域对一个列表进行排序. 
os 模块有了很多变化. stat_float_times 变量的默认值变成了 true, 这意味着 os.stat() 将以浮点数的格式返回时间值.(这并不是说 os.stat() 就一定返回带有小数点的时间数:因为不是所有的平台支持这样的精确度.) 
增加了os.SEEK_SET, os.SEEK_CUR, 和 os.SEEK_END 常量; 这些都是 os.lseek() 函数的参数. 用于 locking 的两个新的常量是 os.O_SHLOCK 和 os.O_EXLOCK. 
增加了两个新函数: wait3() 和 wait4(). 它们的行为类似 waitpid() 函数: 等待一个子进程退出并返回一个元素为进程的 ID 及 退出状态的 tuple.不同之处在于 wait3() 和 wait4() 返回更多的信息. wait3() 不接受进程 ID 参数, 它等待任何子进程退出并返回一个 3-元素(进程id,退出状态,资源使用)的tuple. 类似 resource.getrusage() 函数的返回值. wait4(pid) 接受一个进程 ID 作为参数. (XXX 贡献.) 在FreeBSD上, os.stat() 函数现在返回精确到十亿分之一秒的时间, 并且返回的对象现在拥有 st_gen 和 st_birthtime 属性. 只要平台支持,st_flags 成员也是可用的. 
自 Python 2.0 以来就不再推荐使用的陈旧的 regex 和 regsub 模块, 终于从标准库中删除了. 其它删除的模块还有: statcache, tzparse, whrandom. 
以前用来包含那些类似 dircmp 和 ni 等古老模块lib-old 目录,也被删除. 除非你的代码显式的添加了这个目录到 sys.path, lib-old 不再位于默认 sys.path 内.这应该不支影响到你的代码. 
socket 模块现在在 Linux 上 支持 AF_NETLINK 了, 感谢 Philippe Biondi 的patch. Netlink sockets 是 Linux平台上的用于用户空间进程与内核的通信的专有机制. <a href="http://www.linuxjournal.com/article/7356">http://www.linuxjournal.com/article/7356</a> 是一篇它的介绍文章. 在 Python 代码中, netlink addresses 被表示一个由两个整数组成的 tuple(pid, group_mask). 
getfamily(), gettype(), 和 getproto() 方法用来得到 Socket对象的 family, type, 和 protocol 值. 
新模块: spwd 提供了访问 shadow 口令库的一系列函数(在支持的平台上). 
tarfile 模块中的 TarFile 类现在有了一个 extractall() 方法以释放出 tar 包中所有的文件到当前目录.当然你也可以指定一个不同的目标目录, 及指定解包哪些特定文件. 
一个 tarfile 的压缩格式可以通过使用模式 'r|*' 自动检测. (Lars Gust&auml;bel 贡献.) 
unicodedata 模块做了更新,现在使用 4.1.0 版本的 Unicode 字符数据库了. 某些平台需要版本 3.2.0, 因此可以用 unicodedata.db_3_2_0 使用这个老的版本. 
xmlrpclib 模块现在支持XML-RPC 日期类型返回datetime对象. 需要提供 use_datetime=True 参数给loads()函数或Unmarshaller类以允许这个特性. 
1.1.1. 13.1 ctypes 包
ctypes 包的作者是 Thomas Heller, 这个包被加入到标准库中. ctypes 使你能够调用共享库或DLL中的任意函数. 
将来发行 Python 2.5(正式版或BETA版?)时 , 我会添加一个简短的关于如何使用这个模块的介绍. 
1.1.2. 13.2 ElementTree 包
用于处理 XML的 ElementTree 库(作者:Fredrik Lundh )的子集被添加到标准库中,名字为 xml.etree. 可用的模块有 ElementTree, ElementPath, 和 ElementInclude (ElementTree版本 1.2.6). 
将来发行 Python 2.5(正式版或BETA版?)时 , 我会添加一个简短的关于如何使用这个模块的介绍(约一页长). 完整的 ElementTree 文档在<a href="http://effbot.org/zone/element-index.htm">http://effbot.org/zone/element-index.htm</a>. 
1.1.3. 13.3 hashlib 包
添加了一个新的 hashlib 模块以替换掉 md5 和 sha 模块. hashlib 添加了更多的安全 hashes (SHA-224, SHA-256, SHA-384, and SHA-512)支持. 只要可能,这个模块就会使用 OpenSSL 进行快速的平台优化的算法实现. 
旧的 md5 和 sha 模块仍然以 hashlib 封装器的形式存在,以提供向后兼容性.新模块的接口非常接近旧模块,但不是一模一样.最大的不同在于创建新哈希对象的构造函数 的命名不同. 
# Old versions h = md5.md5() h = md5.new() 
# New version h = hashlib.md5() 
# Old versions h = sha.sha() h = sha.new() 
# New version h = hashlib.sha1() 
# Hash that weren't previously available h = hashlib.sha224() h = hashlib.sha256() h = hashlib.sha384() h = hashlib.sha512() 
# Alternative form h = hashlib.new('md5') # Provide algorithm as a string 
一旦创建了一个哈希对象, 它的方法就和以前相同: update(string) hashes指定的字符串到当前的 digest 状态, digest() 和 hexdigest() 以一个二进制字符串或16进制字符串的形式返回 digest 值, copy() 返回一个同样digest 状态的新的哈希对象. 
贡献者: Gregory P. SmithThis 
1.1.4. 13.4 sqlite3 包
pysqlite 模块(<a href="http://www.pysqlite.org">http://www.pysqlite.org</a>), 一个嵌入式数据库 SQLite 的封装器, 被加入到标准库中, 包的名字是 sqlite3. SQLite 是一个 C 库,在不需要一个独立服务器进程的情况下实现了一个支持 SQL的全功能数据库. 它将数据保存为一个单一的磁盘文件.pysqlite 由 Gerhard H&auml;ring 完成, 提供了一个 兼容 DB-API 2.0 的SQL 接口.这意味着你可以用 SQLite 来书写你的应用程序的第一个版本, 在必要时将数据库切换到一个类似postgreSQL或Oracle的大的数据库, 这种切换将是相当的轻松. 
如果你是自己编译 Python , 要注意源码树中并不包含 SQLite 代码, 而只有封装模块. 你必须在编译 Python 之前安装 SQLite 库和头文件, 之后 build 进程才会编译这个模块. 
将来发行 Python 2.5(正式版或BETA版?)时 , 我会添加一个简短的关于如何使用这个模块的介绍. 
转至<a href="http://www.woodpecker.org.cn">http://www.woodpecker.org.cn</a>
