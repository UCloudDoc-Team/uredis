# 数据迁移

## 主备版Redis数据迁移

### UDTS迁移

UDTS 可以将 Redis 作为数据源/目标 进行全量+增量任务的传输。

注意事项:

当 Redis 源为公网端，且为集群模式的情况下，需要将目标端 VPC 下的子网 ID 绑定 NATGW 方能完成传输任务
需要确保源库的 repl-diskless-sync 配置为 NO

详细文档: https://docs.ucloud.cn/udts/type/redissource

### redis-port迁移

主备版redis-port数据同步，导入导出工具

#### Redis3.2及以下版本


下载地址：<https://redis-import-tool.cn-bj.ufileos.com/uredis-redis-port>

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

#### Redis4.0版本

下载地址： https://umemsh2.cn-sh2.ufileos.com/redis-port_4.0.tar

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
