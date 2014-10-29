# 避免使用 GroupByKey

让我们看一下使用两种不同的方式去计算单词的个数，第一种方式使用 `reduceByKey` 另外一种方式使用 `groupByKey`：

```scala
val words = Array("one", "two", "two", "three", "three", "three")
val wordPairsRDD = sc.parallelize(words).map(word => (word, 1))

val wordCountsWithReduce = wordPairsRDD
  .reduceByKey(_ + _)
  .collect()

val wordCountsWithGroup = wordPairsRDD
  .groupByKey()
  .map(t => (t._1, t._2.sum))
  .collect()
```

这些方法都能产生出正确的结果，`reduceByKey` 更适合使用在大数据集上。这是因为 Spark 在每个区刷新数据之前会组合一个共用的 key。

借助下图可以理解在 `reduceByKey` 里发生了什么。 注意在数据被搬移前同一机器上同样的 key 是怎样被成对地组合的(`reduceByKey` 中的 lamdba 函数)。当 lamdba 函数被再次调用时来自每个区上的所有值被生成了一个最终的结果。

![](http://databricks.gitbooks.io/databricks-spark-knowledge-base/content/images/reduce_by.png)

在另一种方法中，当调用 `groupByKey` 所有的键值对(key-value pair) 都会被刷新。当数据在网络上被传输，这非常没有必要。

Spark 调用分区算法得到一个 key 来确定数据在哪一对机器上传输。当刷新的数据量大于单台执行机器内存所容纳的量时 Spark 会把数据保存到磁盘上。它会使用一个 key 把他们保存在磁盘上，不然当单个 key 的许多键值对超过内存容量就会存在内存溢出的异常。 这将会在之后发行的 Spark 版本中更加优雅地处理，这样的工作还可以继续完善。但是，应该继续避免 Spark 把数据保存到磁盘上，这会严重影响性能。

![](http://databricks.gitbooks.io/databricks-spark-knowledge-base/content/images/group_by.png)

你可以想象一个非常大的数据集，在使用 `reduceByKey` 和 `groupByKey` 时他们的差别会被放大更多倍。

这里有更多的函数比 `groupByKey` 更好：

- `combineByKey` 组合数据，但是组合之后的数据类型与输入时值的类型不一样。
- `foldByKey` 合并每一个 key 的所有值，在级联函数和“零值”中使用。

[阅读原文](http://databricks.gitbooks.io/databricks-spark-knowledge-base/content/best_practices/prefer_reducebykey_over_groupbykey.html)