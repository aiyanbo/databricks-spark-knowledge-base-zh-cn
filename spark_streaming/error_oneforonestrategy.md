# ERROR OneForOneStrategy

如果你在 Spark Streaming 里启用 checkpointing，forEachRDD 函数使用的对象都应该可以被序列化(Serializable)。否则会出现这样的异常 "ERROR OneForOneStrategy: ... java.io.NotSerializableException:"

```scala
JavaStreamingContext jssc = new JavaStreamingContext(sc, INTERVAL);

// This enables checkpointing.
jssc.checkpoint("/tmp/checkpoint_test");

JavaDStream<String> dStream = jssc.socketTextStream("localhost", 9999);

NotSerializable notSerializable = new NotSerializable();
dStream.foreachRDD(rdd -> {
      if (rdd.count() == 0) {
        return null;
      }
      String first = rdd.first();

      notSerializable.doSomething(first);
      return null;
    }
);

// This does not work!!!!
```

按照下面的方式之一进行修改，上面的代码才能正常运行：

- 在配置文件里面删除 `jssc.checkpoint` 这一行关闭 checkpointing。
- 让对象能被序列化。
- 在 forEachRDD 函数里面声明 NotSerializable，下面的示例代码是可以正常运行的：

```scala
JavaStreamingContext jssc = new JavaStreamingContext(sc, INTERVAL);

jssc.checkpoint("/tmp/checkpoint_test");

JavaDStream<String> dStream = jssc.socketTextStream("localhost", 9999);

dStream.foreachRDD(rdd -> {
      if (rdd.count() == 0) {
        return null;
      }
      String first = rdd.first();
      NotSerializable notSerializable = new NotSerializable();
      notSerializable.doSomething(first);
      return null;
    }
);

// This code snippet is fine since the NotSerializable object
// is declared and only used within the forEachRDD function.
```

[阅读原文](http://databricks.gitbooks.io/databricks-spark-knowledge-base/content/spark_streaming/error_oneforonestrategy.html)
