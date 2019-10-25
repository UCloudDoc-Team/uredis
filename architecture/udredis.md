# 分布式版Redis架构



分布式版Redis采用Redis分片+Proxy架构，Redis分片基于主备版Redis资源池，轻松突破Redis自身单线程瓶颈，支持在线扩容可极大满足对于Redis大容量或高性能的业务需求。

#### 1.分布式Redis架构-Redis分片

![image](/images/uredis002.png)

分布式版Redis默认提供一个访问ip，用户访问该ip进行正常的Redis访问及数据操作。

Redis分片：每个分片服务器均是主备版redis高可用架构，主节点故障之后，系统会自动进行主备切换保证服务高可用。

#### 2.分布式Redis架构–代理

![image](/images/uredis003.png)

Proxy代理：双主节点配置，分布式版架构中会有多个Proxy组成，系统会自动对其实现负载均衡及故障转移。

