# FAQs



## 云内存Redis的安全性如何？

### 访问安全性

云内存Redis是“仅内网访问且按账户隔离”的，因此仅有同一账户的云主机能够对云内存Redis实例进行访问。

### 数据安全性

所有的内存数据均持久化到磁盘，不会因服务故障重启而造成数据丢失，避免数据需要重新预热。

## 单实例最高支持多大容量？

主备版redis实例的容量上限为32G（北京二最大支持64G，其他机房超过32G需要提工单申请）

分布式版redis实例是没有容量瓶颈限制的，但超过1T容量需要工单申请。

## 云内存Redis升级容量，会中断业务吗？

不会，云内存redis支持平滑扩容，业务无感知。

## 主备版redis支持哪些协议？是否为原生协议？

主备版redis支持全部原生数据读写命令，但考虑到安全因素，禁用以下命令：

```
BGREWRITEAOF BGSAVE DEBUG CONFIG SAVE SHUTDOWN SLAVEOF
```

另外，对于FLUSH命令做了限制，用户可在控制台上实例详情页面中，通过“清理数据”功能进行FLUSHALL或FLUSHDB清理数据；如需使用该命令请联系技术支持。

## 分布式版Redis支持哪些协议？是否为原生协议？

分布式版Redis支持部分原生Redis协议，支持pipeline，支持异步读写。

不支持协议如下（相比Redis 2.8）：

```
BLPOP BRPOP CLIENT CONFIG DBSIZE DISCARD EVAL  EVALSHA EXEC  MIGRATE MONITOR MOVE MULTI PSUBSCRIBE PUBLISH PUNSUBSCRIBE SCAN SCRIPT  SELECT  SLAVEOF SLOWLOG SMOVE SUBSCRIBE SYNC UNSUBSCRIBE UNWATCH WATCH BRPOPLPUSH DEBUG
```

部分支持协议：

MSETNX - 不支持多Key操作

SORT - 不支持BY选项和GET选项

通过对分布式版Redis的性能优化改造，北京二、洛杉矶及首尔机房提供了具有高性能的分布式版Redis，经测试QPS性能达到同配置主备Redis的3倍，用户新建的分布式版Redis默认是具有高性能的版本，目前灰度中，存量的分布式Redis实例不受影响。具有高性能的分布式版Redis目前不支持部分协议（相比Redis3.2），具体如下：

```
KEYS MIGRATE MOVE OBJECT RANDOMKEY MSETNX BLPOP BRPOP BRPOPLPUSH PFMERGE GEOADD GEOPOS GEODIST GEORADIUS GEORADIUSBYMEMBER GEOHASH PSUBSCRIBE PUBLISH PUBSUB PUNSUBSCRIBE SUBSCRIBE UNSUBSCRIBE DISCARD EXEC MULTI UNWATCH WATCH EVAL EVALSHA SCRIPT AUTH ECHO SELECT BGREWRITEAOF BGSAVE CLIENT CONFIG DBSIZE DEBUG FLUSHALL FLUSHDB LASTSAVE MONITOR PSYNC SAVE SHUTDOWN SLAVEOF SLOWLOG SYNC TIME
```

GEO命令目前暂不支持，我们后续可以支持。

## 使用分布式版Redis有什么限制？

除部分协议不支持外，一个分布式版Redis实例只支持1个DB，即只能select 0，
select其它无意义。Redis的keys命令，比较耗费性能，业务中尽可能降低keys的使用频率，或者使用其它方式替代。mget，mset，del等批量命令或pipeline方式，建议批量数量不宜超过1000，数量过多易造成请求延时或超时现象。

## 云内存Redis如何确保存储服务的高可用？

云内存redis实例有主从两个存储节点，并且实时同步保证数据的一致性，如果主节点发生宕机，系统会自动切换到从节点上，继续提供读写服务。

## 如果云内存Redis存储空间容量不足了怎么办？

如果存储空间容量不足，可以对其进行扩容，并且该扩容操作是支持在线方式的，一键操作即可完成，扩容期间不会中断业务，读写操作仍然可以正常进行。

如果未及时进行扩容，可能会造成写入失败，请在收到告警和提醒时及时进行扩容，以免影响业务。

在控制台进行扩容后，后台分配的资源也可能需要调整；增加资源，是通过在线迁移实现，一般会选择该实例QPS较低时进行。用户如果需要及时增加后台资源，可以联系技术支持。

## 如何将数据导入导出云内存Redis？

可以使用以下两种工具导入导出数据，使用方法见下载包说明。

下载链接: [import\_data.zip](http://tools.ufile.ucloud.cn/import_data.zip)

下载链接:
[import\_data\_online.zip](http://redis-import-tool.cn-bj.ufileos.com/import_data_online.zip)

## 使用主备版云内存Redis的高可用需要注意什么

主备版云内存Redis的高可用，是基于原生Redis的主从（slaveof）实现的，因为Redis的主从是异步的，在发生故障时，URedis会自动切换；虽然时间极短，但理论上，仍然可能出现数据差异。

## 分布式版Redis的AOF文件重写机制是怎样的？

分布式版Redis支持AOF文件重写功能，触发重写时，对较大的Redis可能造成短暂影响；为降低影响，我们降低了重写的频率，分布式Redis自动重写阈值设为60G；在凌晨3点到6点之间，如果aof文件大于20G，强制重写。

## 分布式版Redis的QPS限制是多少？

基准测试数据（非批量请求）：

key大小为128B，value大小为100B，并发连接数为1000；

1G: 读QPS可以达到3000，写QPS可以达到3000；

20G: 读QPS可以达到60000，写QPS可以达到20000；

内存实例性能和内存容量成线性关系，单个IP最高支持10万QPS，高于此值需要同时两个或多个IP。

分布式版Redis性能和容量直接相关，可以水平扩展，没有QPS限制。

## 主备版云内存Redis提供几个访问IP

主备版云内存Redis只提供一个可访问ip，此IP在发生故障时，会自动迁移；请不要使用slave的ip，发生故障时，salve的ip可能失效。

## 分布式版云内存redis是否支持缩容到16G以下

分布式版redis最小容量为16G，不支持缩容到16G以下。如果需要16G以下的容量，建议选择主备版redis，主备版redis完全兼容分布式版redis，而且支持更多的特性，性能也更高。对于控制台改版之前已创建的容量小于16G的分布式版Redis不再支持缩容，用户可以提工单在线同步迁移到主备版Reids，使用的IP不变更。

## 主备版云内存redis的从节点（Slave）是否会与其主节点（Master）一起保持最新状态？

主节点（Master）的更新会自动复制到其关联的从节点（Slave）。不过，鉴于 Redis 的异步复制技术，出于各种原因，Slave
节点更新可能会落后于其 Master 节点。可能的原因包括，Master 节点的 I/O 写入量超过了 Slave
节点同步的速度；或者Master 节点和 Slave 节点之间有网络延迟。因此 Slave 节点与其 Master
节点之间可能存在滞后或在某一时候有一定程度上的数据不一致。

## 云内存Redis 过期 key 数据删除规则是什么？

云内存Redis有2种方式来删除已过期的 key：

1、主动过期，系统后台会周期性的检测，发现已过期的 key 时，会将其删除。

2、被动过期，当用户访问某个 key 时，如果该 key 已经过期，则将其删除。

## 云内存Redis 默认的数据逐出策略是什么？

分布式版Redis默认淘汰策略：volatile-lru，如果想修改需要提工单进行非标操作；

主备版Redis默认淘汰策略：no-enviction，用户可在控制台配置文件管理中更改；

volatile-lru：使用LRU算法从已设置过期时间的数据集合中淘汰数据。

volatile-ttl：从已设置过期时间的数据集合中挑选即将过期的数据淘汰。

volatile-random：从已设置过期时间的数据集合中随机挑选数据淘汰。

allkeys-lru：使用LRU算法从所有数据集合中淘汰数据。

allkeys-random：从数据集合中任意选择数据淘汰

no-enviction：禁止淘汰数据。

## 主备版Redis的QPS是多少？

主备Redis的QPS参考值是80000，具体QPS大小请参考[](uredis/test)的压测数据文档； 负载100%后，QPS无法提高。

## 主备Redis为什么删除了大量key ，使用内存没有明显下降？

主备redis内存使用量是根据redis的info命令中返回的used\_memory（数据大小）和used\_memory\_rss（占用物理内存大小）信息取max；很多情况下，即使删除了大量Key，used\_memory\_rss也不会有明显下降，但是used\_memory会相应下降的，这是redis内存管理策略造成的现象；used\_memory\_rss与used\_memory的差值，相当于一个buffer，可以继续使用；
如果想要完全释放内存，可以通过清空数据，重启Redis，迁移Redis，来实现。

## Redis扩容是否影响在线服务？

在控制台上对分布式和主备redis进行扩容，不会对redis有任何影响。

分布式redis扩容后，后台可能会对其进行增加分片的操作，从而增加处理能力，提高性能；增加分片，是通过在线迁移实现；由于迁移过程中，会出现请求延迟增大现象，因此一般安排在凌晨做迁移操作，如迁移量大会分多次在凌晨做；根据数据实际情况，迁移流程可能会持续1个或多个凌晨完成整个迁移任务。如果需要立刻迁移，可以通知技术支持。如果需要缩容，请联系技术支持，会根据业务情况排期进行非标操作。

## 主备版Redis短连接并发性能怎么样呢？ 

使用工具redis_benchmark进行压测，主备版Redis对于短连接的并发性能为1W
QPS左右； 如果用户业务服务的短连接请求非常高，建议使用单机版Memcache

## Redis是怎么计算使用量，使用率的？

主备Redis：

使用量：info命令中返回的used\_memory（数据大小）和used\_memory_rss（占用物理内存大小，即向OS申请了多少内存使用，实际使用中可能存在内存碎片）信息取max，即为使用量。

使用率：使用量/购买容量\*100%

分布式Redis：

分布式Redis暂时只根据used_memory计算使用量和使用率

## 主备Redis重启，有什么需要注意？

重启过程，是管理服务对Redis进程调用shutdown命令，关闭Redis，随后再拉起Redis进程；拉起Redis进程后，Redis会进行AOF文件数据加载。目前，容量大于2G的Redis，AOF文件一般较大，加载时间可能会比较久。如果在配置管理中关闭了AOF持久化功能，重启后数据将清空，请谨慎操作；

## 主备Redis的AOF重写机制是怎么样的？

容量 < = 2G，redis自动重写阈值设为20G，在凌晨3点到6点之间，如果aof文件大于5G，强制重写；

2G < 容量 < = 8G，redis自动重写阈值设为60G，在凌晨3点到6点之间，如果aof文件大于10G，强制重写；

8G < 容量 < = 12G，redis自动重写阈值设为60G，在凌晨3点到6点之间，如果aof文件大于20G，强制重写；

12G < 容量 < = 16G，redis自动重写阈值设为100G，在凌晨3点到6点之间，如果aof文件大于20G，强制重写；

16G < 容量 < = 32G，redis自动重写阈值设为100G，在凌晨3点到6点之间，如果aof文件大于40G，强制重写；

32G < 容量 < = 52G，redis自动重写阈值设为100G，在凌晨3点到6点之间，如果aof文件大于60G，强制重写；

容量 > 52G，redis自动重写阈值设为100G，在凌晨3点到6点之间，如果aof文件大于70G，强制重写。

## 新建主备版Redis的最大连接数是多少？

实例容量<2G 最大连接数 10000

实例容量\>=2~4G 最大连接数 20000

实例容量\>=4G 最大连接数 30000

实例容量\>=8G 最大连接数 40000

实例容量\>=16G 最大连接数 50000

实例容量\>=32G 最大连接数 80000

## Redis实例删除之后备份会被删除么？

主备Redis实例删除后，备份（包括自动备份和手工备份）会保留7天，7天之后自动回收。

