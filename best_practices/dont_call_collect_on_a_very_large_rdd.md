# 不要将大型 RDD 的所有元素拷贝到请求驱动者

如果你的驱动机器(submit 请求的机器)内存容量不能容纳一个大型 RDD 里面的所有数据，不要做以下操作：

```scala
val values = myVeryLargeRDD.collect()
```

Collect 操作会试图将 RDD 里面的每一条数据复制到驱动机器(submit 请求的机器)上，这时候会发生内存溢出和崩溃。

相反，你可以调用 `take` 或者 `takeSample` 来确保数据大小的上限。或者在你的 RDD 中使用过滤或抽样。

同样，要谨慎使用下面的操作，除非你能确保数据集小到足以存储在内存中：

- `countByKey`
- `countByValue`
- `collectAsMap`

如果你确实需要将 RDD 里面的大量数据保存在内存中，你可以将 RDD 写成一个文件或者把 RDD 导出到一个容量足够大的数据库中。

[阅读原文](http://databricks.gitbooks.io/databricks-spark-knowledge-base/content/best_practices/dont_call_collect_on_a_very_large_rdd.html)
