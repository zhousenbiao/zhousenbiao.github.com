layout: post.md
title: redis环境搭建
categories: tech
tags: redis

redis是一个key-value存储系统。和Memcached类似，它支持存储的value类型相对更多，包括string(字符串)、list(链表)、set(集合)和zset(有序集合)。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作，而且这些操作都是原子性的。在此基础上，redis支持各种不同方式的排序。与memcached一样，为了保证效率，数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。
Redis 是一个高性能的key-value数据库。 redis的出现，很大程度补偿了memcached这类key/value存储的不足，在部 分场合可以对关系数据库起到很好的补充作用。它提供了Python，Ruby，Erlang，PHP客户端，使用很方便。

Redis是一个开源，先进的key-value存储，并用于构建高性能，可扩展的Web应用程序的完美解决方案。

Redis从它的许多竞争继承来的三个主要特点：

Redis数据库完全在内存中，使用磁盘仅用于持久性。

相比许多键值数据存储，Redis拥有一套较为丰富的数据类型。

Redis可以将数据复制到任意数量的从服务器。

###Redis的优势
异常快速：Redis的速度非常快，每秒能执行约11万集合，每秒约81000+条记录。

支持丰富的数据类型：Redis支持最大多数开发人员已经知道像列表，集合，有序集合，散列数据类型。这使得它非常容易解决各种各样的问题，因为我们知道哪些问题是可以处理通过它的数据类型更好。

操作都是原子性：所有Redis操作是原子的，这保证了如果两个客户端同时访问的Redis服务器将获得更新后的值。

多功能实用工具：Redis是一个多实用的工具，可以在多个用例如缓存，消息，队列使用(Redis原生支持发布/订阅)，任何短暂的数据，应用程序，如Web应用程序会话，网页命中计数等。
###一、环境安装
#####1.windows
  下载windows版本，解压出来，目前最新版本为2.4.5   
	下载地址：[https://github.com/dmajkic/redis/downloads](https://github.com/dmajkic/redis/downloads)
  根据操作系统 进入相应的目录 

   启动redis服务端  redis-server.exe redis.conf

启动服务端成功后  可以使用客户端redis-cli.exe测试

  redis-cli.exe -h 127.0.0.1 -p 6379

  set keytest valuestest

  get keytest

###二、Linux
Ubuntu上安装Redis，打开终端，然后键入以下命令：

    $sudo apt-get update
    $sudo apt-get install redis-server

这将在您的计算机上安装Redis。

启动 Redis

    $redis-server

检查Redis是否在工作？

    $redis-cli

这将打开一个Redis提示，如下图所示：

    redis 127.0.0.1:6379>

上面的提示127.0.0.1是本机的IP地址，6379为Redis服务器运行的端口。现在输入PING命令，如下图所示。

    redis 127.0.0.1:6379> ping
    PONG

这说明你已经成功地安装Redis在您的机器上。

在Ubuntu上安装Redis的桌面管理器
在Ubuntu上安装Redis的桌面管理器，只需从 http://redisdesktop.com/download 打开下载软件包并安装它。

Redis桌面管理器会给你用户界面来管理Redis的Key和数据。

###三、Jedis 客户端实现
http://blog.csdn.net/java2000_wl/article/details/8531702

Maven pom文件 加入依赖
    
	<dependencies>  
	  <dependency>  
	    <groupId>redis.clients</groupId>
    	<artifactId>jedis</artifactId>
    	<version>2.6.1</version>  
	  </dependency>  
	    
	  <dependency>  
	    <groupId>junit</groupId>  
	    <artifactId>junit</artifactId>  
	    <version>4.8.2</version>  
	    <scope>test</scope>  
	  </dependency>  
	</dependencies>  

http://blog.sina.com.cn/s/blog_67196ddc0101dl5g.html

如果只是简单使用Jedis，以下这么几行代码足够：
 
Jedis jedis = new Jedis("127.0.0.1");
String keys = "name";

// 删数据
jedis.del(keys);
// 存数据
jedis.set(keys, "snowolf");
// 取数据
String value = jedis.get(keys);

System.out.println(value);

参考：redis内存使用优化http://www.infoq.com/cn/articles/tq-redis-memory-usage-optimization-storage

http://www.yiibai.com/redis/redis_quick_guide.html

http://try.redis.io/