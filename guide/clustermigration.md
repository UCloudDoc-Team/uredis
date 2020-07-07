## 数据迁移


### 分布式Redis数据迁移

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

6\. 从分布式umem离线导入（通过备份导入）到自建或者主备，需要加上参数 --rmslot。

分布式Redis导入数据

使用redis-port可以实现导入数据；
如果源端redis比较大，失败概率较大，建议逐步写入数据，或者提交非标需求，我们为您导入；需要注意分布式Redis只支持一个DB。

分布式Redis导出数据

不支持导出，如果需要导出数据，请提交非标需求；导出的数据，key是被最新编码过的，如果需要还原，加载数据后，使用工具decode\_key即可。

二、分布式Redis解码key工具decode\_key

下载地址：<http://redis-import-tool.ufile.ucloud.cn/decodekey>

说明：

分布式导出的数据文件，每个key包含了slot前缀，可以使用这个工具去除前缀。
