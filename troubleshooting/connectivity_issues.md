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

