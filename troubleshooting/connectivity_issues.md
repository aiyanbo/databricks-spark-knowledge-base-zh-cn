# Spark 组件之间的网络连接问题

Spark 组件之间的网络连接问题会导致各式各样的警告/错误：

- *** SparkContext <-> Spark Standalone Master: ***

    如果 SparkContext 不能连接到 Spark standalone master，会显示下面的错误

    ```
    ERROR AppClient$ClientActor: All masters are unresponsive! Giving up.
    ERROR SparkDeploySchedulerBackend: Spark cluster looks dead, giving up.
    ERROR TaskSchedulerImpl: Exiting due to error from cluster scheduler: Spark cluster looks down
    ```
    