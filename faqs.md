# FAQs



## 云内存Redis的安全性如何？

### 访问安全性

云内存Redis是“仅内网访问且按账户隔离”的，因此仅有同一账户的云主机能够对云内存Redis实例进行访问。

### 数据安全性

所有的内存数据均持久化到磁盘，不会因服务故障重启而造成数据丢失，避免数据需要重新预热。

## 单实例最高支持多大容量？

主备版Redis实例的容量上限为64GB（如果上限为32GB，需要升级到NVMe版主备版Redis才可以提高容量上限）

分布式版Redis实例理论上没有容量上限，但超过16TB容量需要工单申请。

## 主备版Redis支持哪些协议？是否为原生协议？

主备版Redis支持全部原生数据读写命令，但考虑到安全因素，禁用以下命令：

```
BGREWRITEAOF BGSAVE DEBUG CONFIG SAVE SHUTDOWN SLAVEOF
```

另外，对于FLUSH命令做了限制，用户可在控制台上实例详情页面中，通过“清理数据”功能进行FLUSHALL或FLUSHDB清理数据；如需使用该命令请联系技术支持。

## 分布式版Redis代理支持哪些协议？是否为原生协议？

分布式版Redis代理适用于单机Redis使用方式的客户端，不支持集群使用方式（使用集群方式的客户端可以选择直连分布式版Redis后端分片），支持pipeline和异步读写。

目前分布式Redis代理不支持的Redis协议具体如下（相比Redis 3.2）：

```
CLUSTER KEYS MIGRATE MOVE OBJECT RANDOMKEY MSETNX BLPOP BRPOP BRPOPLPUSH PFMERGE GEOADD GEOPOS GEODIST GEORADIUS GEORADIUSBYMEMBER GEOHASH PSUBSCRIBE PUBLISH PUBSUB PUNSUBSCRIBE SUBSCRIBE UNSUBSCRIBE DISCARD EXEC MULTI UNWATCH WATCH EVAL EVALSHA SCRIPT AUTH ECHO SELECT BGREWRITEAOF BGSAVE CLIENT CONFIG DBSIZE DEBUG FLUSHALL FLUSHDB LASTSAVE MONITOR PSYNC SAVE SHUTDOWN SLAVEOF SLOWLOG SYNC TIME
```

部分支持协议：

MSETNX - 不支持多Key操作

SORT - 不支持BY选项和GET选项

## 使用分布式版Redis有什么限制？

除部分协议不支持外，一个分布式版Redis实例只支持1个DB，即只能select 0，
select其它无意义。Redis的keys命令，比较耗费性能，业务中尽可能降低keys的使用频率，或者使用其它方式替代。mget，mset，del等批量命令或pipeline方式，建议批量数量不宜超过1000，数量过多易造成请求延时或超时现象。

## 分布式版Redis后端分片 cluster 命令支持与禁用
与原生的cluster一样对于常用的Redis操作都是支持的，对于cluster节点的操作，只支持部分查询操作CLUSTER NODES，CLUSTER SLOTS，CLUSTER KEYSLOT，节点的修改操作可以在控制台实现。对于跨节点的操作不支持如mget，keys等。
![image](/images/redis1214.png)

## 云内存Redis如何确保存储服务的高可用？

云内存redis实例有主从两个存储节点，并且实时同步保证数据的一致性，如果主节点发生宕机，系统会自动切换到从节点上，继续提供读写服务。

## 如果云内存Redis存储空间容量不足了怎么办？

如果存储空间容量不足，建议对其进行扩容，主备版Redis如果达到容量上限(32GB或64GB)，扩容需要迁移至NVMe版主备版Redis或者分布式版Redis，
可以新建对应规格实例，自助使用UDTS迁移，也可以联系技术支持迁移；分布式版Redis如果后端分片达到容量上限，需要添加分片拆分任务进行拆分扩容。

如果未及时进行扩容，可能会造成写入失败(注: 开启allkeys-lru淘汰策略，是写入时淘汰数据，也可能有写入失败的情况发生)，
请在收到告警和提醒时及时进行扩容，以免影响业务。

## 使用主备版Redis的高可用需要注意什么

主备版Redis的高可用，是基于原生Redis的主从（slaveof）实现的，因为Redis的主从是异步的，在发生故障时，URedis会自动切换；虽然时间极短，但理论上，仍然可能出现数据差异。

## 分布式版Redis的AOF文件重写机制是怎样的？

分布式版Redis每个分片的AOF重写机制同主备版Redis的AOF重写机制一致。

## 分布式版Redis的QPS限制是多少？

基准测试数据（非批量请求）：

key大小为128B，value大小为100B，并发连接数为1000；

8G两分片: QPS可以达到12W+；

16G四分片: QPS可以达到24W+；

内存实例性能和内存容量成线性关系，单分片可提供6W+ QPS。单个IP最高支持10万QPS，高于此值提高性能需要同时两个或多个IP。

分布式版Redis性能和容量直接相关，可以水平扩展，没有QPS限制。

## 主备版Redis提供几个访问IP

主备版Redis只提供一个可访问ip，此IP在发生故障时，会自动迁移；请不要使用slave的ip，发生故障时，slave的ip可能失效。

## 主备版Redis的从节点（Slave）是否会与其主节点（Master）一起保持最新状态？

主节点（Master）的更新会自动复制到其关联的从节点（Slave）。不过，鉴于 Redis 的异步复制技术，出于各种原因，Slave
节点更新可能会落后于其 Master 节点。可能的原因包括，Master 节点的 I/O 写入量超过了 Slave
节点同步的速度；或者Master 节点和 Slave 节点之间有网络延迟。因此 Slave 节点与其 Master
节点之间可能存在滞后或在某一时候有一定程度上的数据不一致。

## 云内存Redis 过期 key 数据删除规则是什么？

云内存Redis有2种方式来删除已过期的 key：

1、主动过期，系统后台会周期性的检测，发现已过期的 key 时，会将其删除。

2、被动过期，当用户访问某个 key 时，如果该 key 已经过期，则将其删除。

## 云内存Redis 默认的数据逐出策略是什么？

前缀以umem开头的分布式Redis默认淘汰策略：volatile-lru，如果想修改需要提工单进行非标操作；

前缀以udredis开头分布式版Redis默认淘汰策略：no-enviction，如果想修改需要提工单进行非标操作；

主备版Redis默认淘汰策略：no-enviction，用户可在控制台配置文件管理中更改；

volatile-lru：使用LRU算法从已设置过期时间的数据集合中淘汰数据。

volatile-ttl：从已设置过期时间的数据集合中挑选即将过期的数据淘汰。

volatile-random：从已设置过期时间的数据集合中随机挑选数据淘汰。

allkeys-lru：使用LRU算法从所有数据集合中淘汰数据。

allkeys-random：从数据集合中任意选择数据淘汰

no-enviction：禁止淘汰数据。

## 主备版Redis的QPS是多少？

主备版Redis的QPS参考值是80000，具体QPS大小请参考[](uredis/test)的压测数据文档； 负载100%后，QPS无法提高。

## 主备版Redis为什么删除了大量key，使用内存没有明显下降？

主备redis内存使用量是根据redis的info命令中返回的used\_memory（数据大小）和used\_memory\_rss（占用物理内存大小）信息取max；很多情况下，即使删除了大量Key，used\_memory\_rss也不会有明显下降，但是used\_memory会相应下降的，这是redis内存管理策略造成的现象；used\_memory\_rss与used\_memory的比值称为内存碎片率，一般在150%以下；
如果想要降低碎片率，可以考虑在控制台运行在线碎片整理，或者重启Redis，也可以非标迁移Redis。

## Redis扩容是否影响在线服务？

在控制台上对分布式版和主备版Redis进行容量升降级操作，都有可能涉及到数据迁移，如果需要迁移控制台会有提示，具体影响如下：Redis升降级期间服务依然可用，但开始同步数据时负载会升高，并且高可用IP切换时有3秒左右的闪断，请尽量在业务低峰期间执行。

分布式redis扩容后，后台可能会对其进行增加分片的操作，从而增加处理能力，提高性能；增加分片，是通过在线迁移实现；由于迁移过程中，会出现请求延迟增大现象，因此一般安排在凌晨做迁移操作，如迁移量大会分多次在凌晨做；根据数据实际情况，迁移流程可能会持续1个或多个凌晨完成整个迁移任务。如果需要立刻迁移，可以通知技术支持。如果需要缩容，请联系技术支持，会根据业务情况排期进行非标操作。

## 主备版Redis短连接并发性能怎么样呢？ 

使用工具redis_benchmark进行压测，主备版Redis对于短连接的并发性能为1W
QPS左右； 如果用户业务服务的短连接请求非常高，建议使用单机版Memcache

## Redis是怎么计算使用量，使用率的？

使用量：info命令中返回的used\_memory（数据大小）和used\_memory_rss（占用物理内存大小，即向OS申请了多少内存使用，实际使用中可能存在内存碎片）信息取max，即为使用量。

使用率：使用量/购买容量\*100%

## 主备版Redis重启，有什么需要注意？

重启过程，是管理服务对Redis进程调用shutdown命令，关闭Redis，随后再拉起Redis进程；拉起Redis进程后，Redis会进行AOF文件数据加载。目前，容量大于2G的Redis，AOF文件一般较大，加载时间可能会比较久。如果在配置管理中关闭了AOF持久化功能，重启后数据将清空，请谨慎操作；


## 主备版Redis的AOF重写机制是怎么样的？


| 容量               | 自动重写阈值(aof大小) | 运维时间重写阈值(aof大小) |
| ------------------| -----------  | ---------- |
| 1G、2G             | 20G         | 5G         |
| 4G、6G、8G         | 50G         | 10G        |
| 12G、16G、24G、 32G | 100G        | 40G        |
| 40G、 48G           | 100G        | 60G         |
| 56G、 64G           | 100G        | 70G         |


## 新建主备版Redis的最大连接数是多少？


| 容量                   | 连接数      |
| ----------------------| ---------- |
| 1G                    | 10000      |
| 2G                    | 20000      |
| 4G、6G                | 30000      |
| 8G、12G               | 40000      |
| 16G、24G              | 50000      |
| 32G、40G、48G、56G、64G | 80000     |

## 分布式版Redis代理最大连接数是多少？
| 核数 | 连接数 |
| -- | -- |
| 2 | 20000 |
| 4 | 40000 |
| 8 | 80000 |
| 16 | 160000 |
| 32 | 160000 |
| 32 | 320000 |
| 64 | 640000 |

## Redis实例删除之后备份会被删除么？

主备版Redis实例删除后，备份（包括自动备份和手工备份）会保留7天，7天之后自动回收。

## 主备版Redis带宽限制
普通型主备版Redis带宽限制为2Gb。

性能加强型主备版Redis带宽限制
| 容量              | 带宽限制  |
| ------------------| -----------  |
| 4G、6G、8G、12G、16G、24G、32G  | 2Gb  |
| 40G | 2Gb |
| 48G、56G | 4Gb |
| 64G  | 4Gb   |

注：带宽限制并不表示实例运行时一定能达到的带宽值，这取决于客户端业务的实际情况。

## 分布式版Redis代理带宽限制

| 核数 | 内网带宽限制 |
| -- | -- |
| 4核及以下 | 1Gb |
| 8核 | 2Gb |
| 16核 | 3.5Gb |
| 32核 | 7.5Gb |
| 64核 | 11Gb |

注：带宽限制并不表示实例运行时一定能达到的带宽值，这取决于客户端业务的实际情况。

## 分布式版Redis代理规格
分布式代理支持规格为：2核4G内存、4核8G内存、8核16G内存、16核32G内存、32核64G内存、64核128G内存。