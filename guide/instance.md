## 实例管理

新建实例完成后，可以在控制台上管理Redis实例，如：扩容、续费及监控告警等实例管理操作。

主备版Redis控制台支持创建从库、配置升降级、设置密码、重启、慢查询分析、修改配置文件、更改实例名称、更改业务组、更改告警模板、删除、续费等功能。

### 主备Redis从库

主备版Redis支持创建从库，点击主备版Redis实例的操作项中的创建从库，创建从库弹窗确认即可完成创建从库。主备Redis从库最多支持5个。

![image](/images/redisv406.png)

![image](/images/redisv407.png)

### 配置升降级

主备Redis支持对Redis实例自主扩容和缩容，控制台上提供批量“配置升降级”、实例“配置升降级”，可进行容量的扩容或缩容，如下：

#### 实例配置升降级

控制台提供对主备Redis进行升降级操作;

1. 配置升降级无影响，如下图:

![image](/images/uredis_multi_resize_4.png)

2. 升降级对Redis实例有影响，如下图，控制台会提醒客户操作有影响，并提供“预约执行”，可预约业务低峰期间执行;

![image](/images/uredis_multi_resize_5.png)

有影响的升降级，影响如下：Redis升降级期间服务依然可用，开始同步数据时负载会升高，并且主备切换时有20秒左右的闪断，建议在业务低峰期间执行。

#### 批量配置升降级


1. 在控制台，云内存Redis页面勾选需要调整的实例，点击“配置升降级”；

![image](/images/uredis_multi_resize_1.png)

2. 选择需要调整的目标容量；对于调整至目标容量需要数据迁移的实例提供“预约执行”，可预约业务低峰期间执行；

![image](/images/uredis_multi_resize_2.png)

3. 预检查后，可以升降级的检查结果结果为，“可升降”和“可升降（有影响）”。

![image](/images/uredis_multi_resize_3.png)

- 可升降：表示升降级操作对Redis无任何影响。

- 可升降(有影响)：表示升降级会有影响。Redis升降级期间服务依然可用，开始同步数据时负载会升高，并且主备切换时有20秒左右的闪断，建议在业务低峰期间执行。


### 设置密码

控制台点击主备版Redis实例操作项中的“设置密码”，可进行修改密码或取消密码设置。

![image](/images/redis052901.png)

### 重启/删除 实例

控制台点击主备版Redis实例操作项中的重启，可进行redis重启；

![image](/images/redis052902.png)

点击删除确认，即可释放实例，退费根据退费相关规则退款至客户账户。

![image](/images/redis052903.png)

注意：删除实例将清除数据并释放资源，请谨慎操作。

### Redis慢查询分析

控制台点击主备版Redis实例操作项中的“慢查询分析”，弹窗可查看最近的10条日志信息，登录Redis使用slowlog
get命令可查看全部日志。

### 清理数据

主备版Redis实例控制台提供清理数据功能，支持清理全部（FLUSHALL）和指定数据库（FLUSHDB），FLUSHALL清空整个Redis实例的数据(删除所有数据库的所有key)，FLUSHDB清空当前数据库中的所有 key。

![image](/images/flushall01.png)

![image](/images/flushall02.png)

### 运维时间
主备Redis产品提供运维时间窗口设置，用户可以根据自身业务选择时间段来做AOF重写。控制台上运维时间设置如下：
![image](/images/redis202006003.png)

![image](/images/redis202006004.png)
