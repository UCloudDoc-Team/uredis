# 压力测试



测试条件：

1.开启pipeline，不同连接数

2.关闭pipeline，不同连接数

3.开启pipeline，不同Data size

测试脚本模板：

```
#!/bin/bash
for clients in {1,2,4,8,16,32,64,128,256,512,800}; do
echo $clients
redis-benchmark  -c $clients -n 5000000 -P 100 -h 10.10.214.139  -d 256 -t get,set -q
done
```

测试结果：

华北一可用区B

1.开启pipeline，不同连接数

```
redis-benchmark  -c $连接数 -n 5000000 -P 100 -h IP  -d 256 -t get,set
```

![image](/images/test_1.png)

2.关闭pipeline，不同连接数

```
redis-benchmark  -c $连接数 -n 1000000 -h IP -d 256 -t get,set -q
```

![image](/images/test_2.png)

3.开启pipeline，不同Data size

```
redis-benchmark -c 64 -n 5000000 -P 100 -h IP -d $字节 -t get,set -q
```

![image](/images/test_3.png)

