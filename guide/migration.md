# 数据迁移



## 分布式Redis数据迁移

一、分布式版redis-port数据同步，导入dump工具

下载地址：<http://redis-import-tool.ufile.ucloud.com.cn/import_data_from_dump.zip>

使用方法如下：

```
./redis-port sync --redis --psync -f sourceIP:sourcePort -t umemIp:6379  [--filterdb=DB_Num]
```

sourceIP: 源Redis IP

sourcePort： 源Redis Port

umemIp：umem的 IP

filterdb: 可选参数；如果源Redis有多个db，可通过此参数，更改DB\_Num来选择db number

rdb文件离线导入：

```
./redis-port restore --input="dump.rdb" -t DstIP:DstPort [--addslot] [--rmslot]
```

说明：

1\. 支持在线同步数据到umem；

2\. 不支持从umem导出数据到自建Redis，如有此需求，可用其它导出工具或者咨询技术人员；

3\. 此工具为开源软件，详解https://github.com/wandoulabs/redis-port;

4\. 暂时只包含centos版本，如有其它版本需求，可以自行下载源码编译；

5\. 使用时需要加上参数--redis, --psync;

6\. 从分布式umem离线导入到自建或者主备，需要加上参数 --rmslot。

分布式Redis导入数据

使用redis-port可以实现导入数据；
如果源端redis比较大，失败概率较大，建议逐步写入数据，或者提交非标需求，我们为您导入；需要注意分布式Redis只支持一个DB。

分布式Redis导出数据

不支持导出，如果需要导出数据，请提交非标需求；导出的数据，key是被最新编码过的，如果需要还原，加载数据后，使用工具decode\_key即可。

二、分布式Redis解码key工具decode\_key

下载地址：<http://redis-import-tool.ufile.ucloud.cn/decodekey>

说明：

分布式导出的数据文件，每个key包含了slot前缀，可以使用这个工具去除前缀。

## 主备版Redis数据迁移

主备版redis-port数据同步，导入导出工具

### Redis3.2及以下版本


下载地址：<http://redis-import-tool.ufile.ucloud.com.cn/uredis-redis-port>

1、主备Redis同步导入导出

```
./uredis-redis-port sync --psync -f source_ip:source_port -P PASSWORD -t dest_ip:dest_port -A PASSWORD
```

出现如下结果，表示同步完成，正在保持增量同步

![image](/images/redis数据导入.png)

2、主备Redis导出RDB数据文件

```
./uredis-redis-port dump -f source_ip:source_port -P PASSWORD -o save.rdb
```

3、主备Redis导入RDB数据文件

```
./uredis-redis-port restore -i save.rdb -t dest_ip:dest_port -A PASSWORD
```

4、主备Redis导入AOF数据文件

```
redis-cli -h dest_ip -p dest_port -A PASSWORD --pipe < appendonly.aof
```

说明：分布式Redis与主备Redis进行迁移，请提交非标需求，我们后台进行同步迁移。

### Redis4.0版本

下载地址： http://umemsh2.cn-sh2.ufileos.com/redis-port_4.0.tar

使用方法如下:

1  redis-decode: DECODE dumped payload to human readable format (hex-encoding)
``` 
  Usage:
    redis-decode [--ncpu=N] [--input=INPUT|INPUT] [--output=OUTPUT]
  Examples:
    redis-decode -i dump.rdb -o dump.log
    redis-decode    dump.rdb -o dump.log
    cat dump.rdb | redis-decode --ncpu=8 > dump.log
```
2  redis-dump: DUMP rdb file from master redis
```
  Usage:
    redis-dump [--ncpu=N] (--master=MASTER|MASTER) [--output=OUTPUT] [--aof=FILE]
  Examples:
    redis-dump    127.0.0.1:6379 -o dump.rdb
    redis-dump    127.0.0.1:6379 -o dump.rdb -a
    redis-dump -m passwd@192.168.0.1:6380 -o dump.rdb -a dump.aof
```
3  redis-restore: RESTORE rdb file to target redis
```
  Usage:
    redis-restore [--ncpu=N] [--input=INPUT|INPUT] --target=TARGET [--aof=FILE] [--db=DB] [--unixtime-in-milliseconds=EXPR] 
  Examples:
    redis-restore    dump.rdb -t 127.0.0.1:6379
    redis-restore -i dump.rdb -t 127.0.0.1:6379 --aof dump.aof --db=1
    redis-restore             -t 127.0.0.1:6379 --aof dump.aof
    redis-restore             -t 127.0.0.1:6379 --db=0
```
4  redis-sync: SYNC data from master to slave
```
  Usage:
    redis-sync [--ncpu=N] (--master=MASTER|MASTER) --target=TARGET [--db=DB] [--tmpfile-size=SIZE [--tmpfile=FILE]]
  Examples:
    redis-sync -m 127.0.0.1:6379 -t 127.0.0.1:6380
    redis-sync    127.0.0.1:6379 -t passwd@127.0.0.1:6380
    redis-sync    127.0.0.1:6379 -t passwd@127.0.0.1:6380 --db=0
```

## 同步数据到URedis

很多场景，需要把云主机中自建的Redis，或者其它机房的Redis，同步导入到URedis中；下载如下工具即可实现：<http://tools.ufile.ucloud.cn/import_data_online.zip>

在一台可以连接源端IP，目的端URedis IP的主机上，运行该工具；即可实现源端Redis，目的端URedis同步。
