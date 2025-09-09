# 性能趋势
性能趋势页面的监控指标，采集频率为：5s/次；前端页面可以开启自动刷新，开启自动刷新后，前端页面的刷新频率为：15s/次。

## 进入性能趋势页面
首先进入到“实例管理”页面，然后选择需要分析的数据库实例，点击“性能趋势”进入性能趋势页面：
![image](/images/udac_performance_trends_1.png)

![image](/images/udac_performance_trends_2.png)

## 指标筛选
点击“指标筛选”选择需要展示的监控指标(用户自选/关键指标):
![image](/images/udac_performance_trends_3.png)
选择完成后，界面会展示选择的指标数据。

## 指标诊断
指标诊断功能针对一段时间范围内的指标（CPU使用率，内存使用率，QPS，延迟）数据进行健康诊断，在性能趋势页面，启用“诊断”：
![image](/images/udac_performance_trends_4.png)
选择诊断的时间范围：
![image](/images/udac_performance_trends_5.png)
点击“诊断”获取结果（正常、警告、异常三个状态）。
告警和异常分别有自己的阈值，触发到对应的阈值就会得到对应的状态： 
![image](/images/udac_performance_trends_6.png)
同时支持“重新诊断”，获取新的诊断结果：
![image](/images/udac_performance_trends_7.png)
诊断完成可以关闭“诊断”：
![image](/images/udac_performance_trends_8.png)

## 指标联动
 性能趋势页面，对于数据库实例监控指标数据，在时间纬度上支持“联动”，在界面勾选“联动图标”设置为 “ON”：
![image](/images/udac_performance_trends_9.png)
启用联动后，在页面选择某个时间指标数据，不同监控指标数据在时间纬度上进行联动。
![image](/images/udac_performance_trends_10.png)

## 性能趋势对比
趋势页面选择“性能趋势对比”，支持对同一指标在不同时间纬度上的一个对比，选择性能趋势下方的性能趋势对比：
![image](/images/udac_performance_trends_11.png)
首先筛选需要进行对比的“监控指标”：
![image](/images/udac_performance_trends_12.png)
然后选择对比的2个时间范围：
![image](/images/udac_performance_trends_13.png)
接着就可以对着监控图来进行监控的对比了。