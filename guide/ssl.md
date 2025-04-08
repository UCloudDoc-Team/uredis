## 设置SSL加密

主备版Redis支持开启SSL加密通信的功能，在开启了SSL功能之后，您可以将SSL CA证书安装到您的应用服务。SSL加密功能在传输层对网络连接进行加密，在提升数据安全性的同时，保证数据的完整性。

### 注意事项
1. 目前支持SSL功能的实例版本为6.0(小版本大于20.55), 和7.0(小版本大于15.14)。
2. 目前只有主备版本Redis支持SSL功能。
3. 证书有效期默认为3年。更新证书也是默认3年。
4. 目前Redis服务端支持的TLS协议有：TLSv1.1，TLSv1.2，TLSv1.3。
5. 该功能目前没有全量开放，如需使用请联系技术支持开放权限。

### 打开SSL功能
主备实例点解详情按钮，选择“数据安全”的tab。点击立即开通，如下所示：
![image](/images/ssl1.png)
![image](/images/ssl2.png)
点击确定之后，实例状态会变成“修改SSL中”，此时需要等待几分钟，等待实例恢复到“运行状态”。
![image](/images/ssl3.png)

### 更新证书有效期
SSL开启之后，如果想要更新证书有效期，点击如下的按钮：
![image](/images/ssl4.png)
点击确定按钮更新证书。
![image](/images/ssl5.png)
更新证书和打开SSL功能一样需要重启Redis实例，需要等待几分钟，更新成功后，实例状态会重新变成运行状态。

### 更换协议
如果想要切换协议可以点击协议版本右侧的按钮，目前支持：TLSv1.1，TLSv1.2，TLSV1.3。如下图所示：
![image](/images/ssl6.png)
![image](/images/ssl7.png)

### 下载证书
Redis SSL功能提供三个格式的证书，分别是：ca.crt、ca.jks、ca.p7b。您可以根据自身的情况使用相应格式的文件。下载按钮如下图所示：
![image](/images/ssl8.png)

### 关闭SSL功能
关闭SSL功能在“数据安全”的tab，关闭SSL功能需要重启，会等待几分钟，此期间实例状态为“修改SSL中”，修改完成之后实例状态变为“运行”。
![image](/images/ssl9.png)


### SSL连接方法参考：
1. 通过redis-cli连接redis
```
redis-cli --tls --cacert ca.crt -h IP -a password
```