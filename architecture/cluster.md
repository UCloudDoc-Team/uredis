# 分布式版Redis Cluster架构



分布式版Redis cluster采用多Redis节点架构，单节点基于主备版Redis资源池，备节点保持高可用，轻松突破Redis自身单线程瓶颈，支持在线扩容可极大满足对于Redis大容量或高性能的业务需求。

### 分布式高性能udredis与cluster模式的对比

#### 相同点
在操作上，除代理外，两者操作相同，都可以在控制台分分片管理上对分片容量进行扩缩容，也可以对节点进行拆分操作。
节点都提供主备节点，保证节点的高可用。

#### 不同点
cluster支持所有redis的原生命令（除了禁用的命令），但对于节点间的命令操作如mget，keys等命令不支持。
性能上，cluster命令请求直接发送到后端redis，udredis需要经过代理转发到后端redis，cluster的性能会优于udredis。