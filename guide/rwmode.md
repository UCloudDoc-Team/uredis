# 分布式版读写分离


## 开启读写分离

创建读写分离版本数据库，节点数量支持最大规格为1主8从，创建默认所有节点均衡模式。

![image](/images/remode_create.png)

## 设置读模式

提供三种读模式，用户可以根据自身业务情况进行调整；点击节点管理页面点击设置读权重，即可修改

![image](/images/rwmode_setweight_1.png)

![image](/images/read_node_balance.png)

![image](/images/node_balance.png)

![image](/images/coustomize.png)

## 添加只读从节点

节点管理页面点击添加只读从节点，添加从节点，从节点数量最多支持8个

![image](/images/add_node.png)

## 删除只读从节点


![image](/images/delete_node.png)

## 备份管理 

![image](/images/rwmode_backup.png)

## 删除代理

代理管理页面点击删除代理，且代理数量最小为1

![image](/images/rwmode_delete_proxy.png)