## Redis5.0版本新功能介绍

Redis5.0版本是Redis的重大版本发布，新增数据结构Stream，是一个新的强大的支持多播的可持久化的消息队列，在消息队列方面提供了新的选择，具备丰富的应用场景和想象空间。

Redis5.0最新特点具体有：

1.新的Stream数据类型(Stream data type)

2.RDB 增加存储LFU和LRU信息

3.新的有序集合(sorted set)命令：ZPOPMIN/MAX 和阻塞变量(blocking variants)

4.升级 Active defragmentation 至 v2

5.增强 HyperLogLog 的实现

6.更好的内存统计报告

7.许多包含子命令的命令现在都有一个 HELP 子命令

8.对于客户端频繁连接和断开，连接时性能更好

9.一些错误修复和其他方面的改进

10.升级 Jemalloc 至 5.1 版本

11.增加 CLIENT UNBLOCK 和 CLIENT ID

12.增加命令LOLWUT

13.对于不存在需要保持向后兼容性的地方，不再使用 "slave" 术语

14.网络层中的差异进行了优化

15.Lua改进

16.增加动态的 HZ(Dynamic HZ) 来平衡空闲 CPU 使用率和响应性

17.对 Redis 核心代码进行了重构，并在多方面进行了优化
