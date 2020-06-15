# 版本说明



## 产品版本

主备版Redis支持Redis3.0、Redis3.2、Redis4.0和Redis5.0.

分布式版Redis基于Redis2.8，分布式版Redis（高性能版）基于Redis3.2。

## Redis5.0版本新功能介绍

Redis5.0版本是Redis的重大版本发布，新增数据结构Stream，是一个新的强大的支持多播的可持久化的消息队列，在消息队列方面提供了新的选择，具备丰富的应用场景和想象空间。

Redis5.0最新特点具体有：

1.新的Stream数据类型(Stream data type)

2.RDB 增加存储LFU和LRU信息

3.新的有序集合(sorted set)命令：ZPOPMIN/MAX 和阻塞变量(blocking variants)

4.升级 Active defragmentation 至 v2

5.增强 HyperLogLog 的实现

6.更好的内存统计报告

7.许多包含子命令的命令现在都有一个 HELP 子命令

8.对于客户端频繁连接和断开，连接时性能更好

9.一些错误修复和其他方面的改进

10.升级 Jemalloc 至 5.1 版本

11.增加 CLIENT UNBLOCK 和 CLIENT ID

12.增加命令LOLWUT

13.对于不存在需要保持向后兼容性的地方，不再使用 "slave" 术语

14.网络层中的差异进行了优化

15.Lua改进

16.增加动态的 HZ(Dynamic HZ) 来平衡空闲 CPU 使用率和响应性

17.对 Redis 核心代码进行了重构，并在多方面进行了优化

## Redis4.0版本新功能介绍

主备版Redis 4.0基于社区Redis 4.0引擎，与Redis3.x版本相比，带来了以下这些新的功能特性，主要涉及以下更新：

Lazyfree机制，避免del、flushdb、flushall、rename等命令引起的redis-server阻塞，提高服务稳定性；新增命令，如MEMORY、SWAPDB；内存性能优化，即主动碎片整理。

### Lazyfree机制

Redis 4.0的Lazyfree机制，延迟删除大key，降低删除操作对系统资源的占用影响。具体如下：

**unlink**

在Redis
4.0之前，redis执行del命令，只有在释放掉key的所有内存以后才会返回OK。如果key比较大（比如说一个hash里有1000万条数据），其他连接需要等待较长时间。为了兼容已有的del语义，Redis
4.0引入unlink命令，效果以及用法和del完全一样，但内存释放动作放到后台线程中执行。 
``` 
UNLINK key [key...]
``` 

**flushdb/flushall**

flushdb/flushall在 Redis 4.0中引入了新选项，可以指定是否使用Lazyfree的方式来清空整个内存。 
```
FLUSHALL [ASYNC] 
FLUSHDB [ASYNC] 
``` 

**rename**

执行rename oldkey
newkey时，如果newkey已经存在，redis会先删除已经存在的newkey，这也会引发上面提到的删除大key问题。

### 新增命令

swapdb：交换两个db的数据，swapdb执行之后用户连接db无需再执行select，即可看到新的数据。

zlexcount：用于sorted
set中，和zrangebylex类似，不同的是zrangebylex返回member，而zlexcount是返回符合条件的member个数。

memory：Redis 4.0用户使用该命令可以全面了解Redis的内存状态，之前版本则只能通过info
memory来了解Redis内部有限的内存信息。

memory usage：usage子命令可以查看某个key在redis内部实际占用多少内存。

memory stats：当前Redis实例内存使用细节。

memory doctor：主要用于给一些诊断建议，提前发现潜在问题。

malloc stats & malloc purge：这两个命令用于操作jemalloc，只在使用jemalloc的时候才有效。
