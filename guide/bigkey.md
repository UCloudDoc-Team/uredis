# 主备Redis大key分析

## 背景信息
Redis 提供了 list、hash、zset 等复杂类型的数据结构，业务在使用的时候可能由于 key 设计不合理导致某个 key过大，可以使用我们提供的大key分析工具来发现大key。

## 注意事项
* 支持扫大key的URedis版本为主备版4.0, 5.0, 6.0, 7.0。
* 大key分析后产生的结果文件只保留24小时，24小时后文件将无法下载。
* 一次只能执行一个key分析的任务。

## 操作步骤
1. 进入到“云内存 UMem Redis”产品页面。
2. 选择主备版Redis产品，在更多按钮中找到“大key分析”，并点击确认执行。
   ![image](/images/bigkeys_1.png)
   ![image](/images/bigkeys_2.png)

3. 此时可以进入定时任务管理页面查看正在执行的任务(点击执行任务后会自动跳转到该页面)。
   ![image](/images/bigkeys_3.png)
   ![image](/images/bigkeys_4.png)

4. 等待任务执行结束后任务状态边改成功,此时可以点击下载按钮下载大key分析的文件。
   ![image](/images/bigkeys_5.png)

5. 下载完成后可以查看大key分析文件，结果文件分为两部分。
   ![image](/images/bigkeys_6.png)
   * **第一部分**：展示大key的详细信息，总共有4列。第一列是key所在的db，第二列是key的类型，第三列是key名，第四列是大key的长度。
   * **第二部分**：对分析出来的大key进行总结，统计了各个类型的key的大key的数量。

## 常见问题
* Q: 大key分析的任务要执行多久？ <br />
  A: 取决于主备实例的key的数量。1000万个key的话，大概需要2分钟。
* Q: 大key的标准是什么？<br />
  大key的标准可见下表：
  | key类型 | 大key标准 |
  | -- | -- |
  | list | list中元素的个数大于等于10000个 |
  | hash | hash中元素的个数大于等于10000个 |
  | string | value的长度大于等于10000 |
  | stream | stream中元素的个数大于等于10000个 |
  | set | set中元素的个数大于等于10000个 |
  | zset | zset中元素的个数大于等于10000个 |

  <br />
  如果想要调整这些标准，可以联系技术支持非标修改。