# 实例管理



新建实例完成后，可以在控制台上管理Redis实例，如：扩容、续费及监控告警等实例管理操作

## 主备版Redis管理

主备版Redis控制台支持创建从库、配置升降级、设置密码、重启、慢查询分析、修改配置文件、更改实例名称、更改业务组、更改告警模板、删除、续费等功能。

#### Redis从库

主备版Redis支持创建从库，点击主备版Redis实例的操作项中的创建从库，创建从库弹窗确认即可完成创建从库。主备Redis从库最多支持5个。

![image](/images/redisv406.png)

![image](/images/redisv407.png)

#### 配置升降级

主备Redis支持对Redis实例自主扩容和缩容，控制台上点击所需更改容量实例操作项中的“配置升降级”，可进行容量的扩容或缩容，如下：

![image](/images/redisv405.png)

#### 设置密码

控制台点击主备版Redis实例操作项中的“设置密码”，可进行修改密码或取消密码设置。

![image](/images/redis052901.png)

#### 重启/删除 实例

控制台点击主备版Redis实例操作项中的重启，可进行redis重启；

![image](/images/redis052902.png)

点击删除确认，即可释放实例，退费根据退费相关规则退款至客户账户。

![image](/images/redis052903.png)

#### Redis慢查询分析

控制台点击主备版Redis实例操作项中的“慢查询分析”，弹窗可查看最近的10条日志信息，登录Redis使用slowlog
get命令可查看全部日志。

#### 数据清理

主备版Redis实例控制台提供数据清理功能，支持FLUSHDB和FLUSHALL，FLUSHALL清空整个Redis实例的数据(删除所有数据库的所有key)，FLUSHDB清空当前数据库中的所有 key。

![image](/images/flushall01.png)

![image](/images/flushall02.png)

## 分布式Redis实例管理

分布式版Redis控制台支持扩容、更改实例名称、更改业务组、更改告警模板、删除、续费等功能。

#### 扩容操作

分布式版redis用户可以对当前实例进行扩容操作：

![image](/images/redisv404.png)
