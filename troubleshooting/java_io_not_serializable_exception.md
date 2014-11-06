# Job aborted due to stage failure: Task not serializable:

如果你能看到以下错误：

```
org.apache.spark.SparkException: Job aborted due to stage failure: Task not serializable: java.io.NotSerializableException: ...
```

上述的错误在这个时候会被触发：当你在 master 上初始化一个变量，但是试图在 worker 上使用。在这个示例中， Spark Streaming 试图将对象序列化之后发送到 worker 上，如果这个对象不能被序列化就会失败。思考下面的代码片段：

```java
NotSerializable notSerializable = new NotSerializable();
JavaRDD<String> rdd = sc.textFile("/tmp/myfile");

rdd.map(s -> notSerializable.doSomething(s)).collect();
```

这段代码会触发那个错误。这里有一些建议修复这个错误：

- 让 class 实现序列化
- 在作为参数传递给 map 方法的 lambda 表达式内部声明实例
- 在每一台机器上创建一个 NotSerializable 的静态实例
- 调用 `rdd.forEachPartition` 并且像下面这样创建 NotSerializable 对象：

```java
rdd.forEachPartition(iter -> {
  NotSerializable notSerializable = new NotSerializable();

  // ...Now process iter
});
```

[阅读原文](http://databricks.gitbooks.io/databricks-spark-knowledge-base/content/troubleshooting/javaionotserializableexception.html)
