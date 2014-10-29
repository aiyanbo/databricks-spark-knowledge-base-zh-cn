# 避免使用 GroupByKey

让我们看一下使用两种不同的方式去计算单词的个数，第一种方式使用 `reduceByKey` 另外一种使用 `groupByKey`：

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

这些方法都能产生出正确的结果，`reduceByKey`这个例子更适合使用在大数据集上。这是因为 Spark 在搬移每个区的数据之前会组合输出一个通用 key。

借助下图可以理解在 `reduceByKey` 里发生了什么。 注意在数据被搬移前同一机器上同样的 key 是怎样被成对地组合的(`reduceByKey` 中的 lamdba 函数)。当 lamdba 函数被再次调用时来自每个区上的所有值被生成了一个最终的结果。

![](http://databricks.gitbooks.io/databricks-spark-knowledge-base/content/images/reduce_by.png)
