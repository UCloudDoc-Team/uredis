# 主备Redis热key分析

## 背景信息
URedis以最近最少使用算法LFU（Least Frequently Used）为基础，经过高效的排序以及统计算法识别出当前实例的热点Key

## 注意事项
* 支持扫大key的URedis版本为主备版4.0, 5.0, 6.0, 7.0。
* 热key分析后产生的结果文件只保留24小时，24小时后文件将无法下载。
* 一次只能执行一个热key分析的任务。
* 热key分析会导致主实例负载上升，在执行热key分析之前，需要评估一下是否会对实例造成影响。
* 热key分析默认是找出前100个热点key，如果热点key频率相同，则只显示先扫出来的热key。 
* 热key分析之前，需要将配置参数项“maxmemory-policy”设置为“allkeys-lfu”或“volatile-lfu”。

## 操作步骤
1. 进入到“云内存 UMem Redis”产品页面。
2. 选择主备版Redis产品，在更多按钮中找到“热key分析”，并点击确认执行。
   ![image](/images/hotkeys_1.png)
   ![image](/images/hotkeys_2.png)

3. 此时会自动跳转到定定时任务管理页面，如下图所示。
   ![image](/images/hotkeys_3.png)

4. 等待任务执行结束后任务状态边改成功,此时可以点击下载按钮下载热key分析的文件。
   ![image](/images/hotkeys_4.png)

5. 下载完成后可以查看热key分析文件，结果文件分为两部分。
   ![image](/images/hotkeys_5.png)
   * **第一部分**：展示热key的详细信息，总共有3列。第一列是key所在的db，第二列是key的访问热度，第三列是key名。
   * **第二部分**：热key的数量。