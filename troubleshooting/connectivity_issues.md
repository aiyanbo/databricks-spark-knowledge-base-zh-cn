# Spark 组件之间的网络连接问题

Spark 组件之间的网络连接问题会导致各式各样的警告/错误：

- **SparkContext <-> Spark Standalone Master:**

    如果 SparkContext 不能连接到 Spark standalone master，会显示下面的错误

    ```
    ERROR AppClient$ClientActor: All masters are unresponsive! Giving up.
    ERROR SparkDeploySchedulerBackend: Spark cluster looks dead, giving up.
    ERROR TaskSchedulerImpl: Exiting due to error from cluster scheduler: Spark cluster looks down
    ```

    如果 driver 能够连接到 master 但是 master 不能回连到 driver上，这时 Master 的日志会记录多次尝试连接 driver 失败并且会报告不能连接：

    ```
    INFO Master: Registering app SparkPi
    INFO Master: Registered app SparkPi with ID app-XXX-0000
    INFO: Master: Removing app app-app-XXX-0000
    [...]
    INFO Master: Registering app SparkPi
    INFO Master: Registered app SparkPi with ID app-YYY-0000
    INFO: Master: Removing app app-YYY-0000
    [...]
    ```

    在这个事例中，master 报告应用已经被成功地注册了。但是注册成功的通知 driver 接收失败了， 这时 driver 会自动尝试几次重新连接直到失败的次数太多而放弃重试。 Master web UI 的结果会报告几个失败的应用即使只有一个 SparkContext 被创建。

## 建议

如果你遇到上述的任何错误：

- 在 Spark master web UI/日志 里面检查 workers 和 drivers 配置的连接 Spark master 的地址是否是正确的。
- 设置 driver，master，worker 的 `SPARK_LOCAL_IP` 为集群的可寻地址主机名。

## 配置 hostname/port

