## Redis7.0版本新功能介绍

#### 1.新增Redis Functions,支持持久化和主从复制

#### 2.ACL改进，支持key粒度的权限控制

#### 3.Sharded-Pub/Sub

#### 4.Multi-Part AOF，采用全量+增量的独立文件存储，避免了AOF重写开销

#### 5.Client-eviction，客户端逐出策略，对所有clien使用的内存做限制，如果超过限制，则释放内存消耗最大的client

#### 6.AOF增加时间戳

#### 7.Ziplist编码替换为Listpack编码

#### 8.支持Global Replication Buffer

#### 9.支持命令执行耗时直方图

#### 10.支持子命令级别的性能统计


### 标注：

URedis 7.0 禁用ACL
