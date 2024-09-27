## Redis6.0版本新功能介绍

#### 1.ACL

在Redis 5版本之前，Redis安全规则只有密码控制，以及通过RENAME重命名高危命令如flushdb，flushall等。Redis 6可以通过ACL对用户进行权限控制 。

#### 2.RESP3
	
Redis 6 开始在兼容 RESP2 的基础上，开始支持 RESP3。

#### 3.客户端缓存
	
基于 RESP3 协议实现的客户端缓存功能。为了进一步提升缓存的性能，将客户端经常访问的数据cache到客户端。减少TCP网络交互。

#### 4.多线程IO
	
IO多线程是指客户端交互部分的网络IO交互处理模块多线程，执行命令仍然是单线程。
        
在开启IO多线程的情况下，性能提升50%左右。

#### 5. Modules API
	
提供了众多的新模块

#### 6. Redis-benchmark新增了Redis集群模式。


### 标注：

URedis 6.0 禁用ACL

URedis 6.0 不支持模版

URedis 6.0 不支持SSL

URedis 6.0 不支持多线程