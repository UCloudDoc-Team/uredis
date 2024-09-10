# 分布式版Redis架构



分布式版Redis采用Proxy代理+Redis分片架构，代理仅支持单机Redis的使用方式，cluster集群的Redis分片可以在无代理的情况下使用，但是仅支持原生集群的使用方式，需要根据客户端的类型来选择使用方式（命令限制见FAQs）。
Redis分片基于主备版Redis资源池，轻松突破Redis自身单线程瓶颈，支持在线扩容可极大满足对于Redis大容量或高性能的业务需求。

#### 1.分布式版Redis架构

![image](/images/uredis002.png)

分布式版Redis的每个Redis分片就是一个主备版Redis，同主备版Redis共享资源池，架构同主备版Redis高可用架构。单个分片的容量限制和扩缩容规则同主备版Redis保持一致。
分布式版Redis的代理是高可用架构，容器版代理当宿主机宕机时会由系统自动迁移至其他宿主机；快杰版代理由一主一备两台主机组成，主宕机后会由系统将高可用IP迁移至备主机。

#### 2.分布式高性能udredis(22年4月升级前创建版本)与cluster集群的对比

2022年4月以后创建的分布式版Redis后端Redis集群已经默认升级为cluster集群。

#### 相同点
在操作上，除代理外，两者操作相同，都可以在控制台分分片管理上对分片容量进行扩缩容，也可以对节点进行拆分操作。
节点都提供主备节点，保证节点的高可用。
cluster集群同样支持添加代理，使用代理来适配单机Redis的用法。

#### 不同点
cluster集群支持无代理使用，客户端使用集群模式连接后端Redis分片后直接使用。分布式高性能udredis则必须使用代理连接。
cluster集群支持所有Redis原生cluster命令（除了集群管理或禁用的命令），但同时也受到原生cluster命令的限制，比如涉及到多个key slot键值的命令如mget，keys。
cluster集群可以选择4.0、5.0、6.0版本创建。
性能上，cluster集群命令请求直接发送到后端Redis分片，而分布式高性能udredis需要经过代理转发到后端Redis分片，所以直连cluster集群的性能会优于分布式高性能udredis。
#### 3.分布式版Redis代理配置模式

![image](/images/udredisproxy_ms.png)
主从型代理各代理提供一个ip入口，用户可以自由配置代理入口


![image](/images/udredisproxy_lb.png)
负载均衡型代理模式下，集群只提供一个ip入口，业务集中发到ULB4上，由ULB4 Server进行故障下线和请求均衡

#### 4.分布式版Redis代理内部架构

![image](/images/uredis003.png)

分布式版Redis代理内部由多个Proxy进程(RedisProxy)组成，系统内核会对客户端连接做连接均衡，绑定到某个Proxy进程。
分布式版Redis代理内部会有1个处理多key命令的进程(BusyProxy)来处理复杂的查询。

分布式版Redis cluster采用多Redis节点架构，单节点基于主备版Redis资源池，备节点保持高可用，轻松突破Redis自身单线程瓶颈，支持在线扩容可极大满足对于Redis大容量或高性能的业务需求。

#### 5.分布式版Redis读写分离内部架构

![image](/images/rw.png)

分布式读写分离采用Redis主从架构，单节点基于主备版Redis资源池，备节点保持高可用，业务读请求会根据代理读模式进行分发。

#### 版本升级
2022.4月中 cluster集群版本做出调整升级。

1.创建的实例ID不再以udrediscluster-开头，改为udredis-开头。

2.新建的实例默认都为cluster集群，key slot跟原生Redis cluster保持一致为0-16383，目前可以通过key slot区分后端为cluster集群还是代理分片。