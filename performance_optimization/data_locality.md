# 数据本地性

Spark 是一个并行数据处理框架，这意味着在执行任务的时候数据应该放在一个合适的地方(既 最少的数据传输)。

## 检查本地性

检查任务是否在本地运行的最好方式是在 Spark UI 上查看 stage，注意下面截图中的 "Locality Level" 列显示任务运行在哪个地方。

![](../images/locality.png)

## 修改本地性配置

你可以调整 Spark 在每个数据本地性(data local --> process local --> node local --> rack local --> Any)上等待的时长。更多详细的参数信息请查看[程序配置文档的 Scheduling 章节](http://spark.apache.org/docs/latest/configuration.html#scheduling)里类似于 `spark.locality.*` 的配置。