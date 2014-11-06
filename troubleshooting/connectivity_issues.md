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

    在这样的情况下，master 报告应用已经被成功地注册了。但是注册成功的通知 driver 接收失败了， 这时 driver 会自动尝试几次重新连接直到失败的次数太多而放弃重试。其结果是 Master web UI 会报告多个失败的应用，即使只有一个 SparkContext 被创建。

## 建议

如果你遇到上述的任何错误：

- 检查 workers 和 drivers 配置的 Spark master 的地址就是在 Spark master web UI/日志中列出的那个地址。
- 设置 driver，master，worker 的 `SPARK_LOCAL_IP` 为集群的可寻地址主机名。

## 配置 hostname/port

这节将描述我们如何绑定 Spark 组件的网络接口和端口。

在每节里，配置会按照优先级降序的方式排列。如果前面所有配置没有提供则使用最后一条作为默认配置。

### SparkContext actor system:

**Hostname:**

- `spark.driver.host` 属性
- 如果 `SPARK_LOCAL_IP` 环境变量的设置是主机名(hostname)，就会使用设置时的主机名。如果 `SPARK_LOCAL_IP` 设置的是一个 IP 地址，这个 IP 地址会被解析为主机名。
- 使用默认的 IP 地址，这个 IP 地址是Java 接口 `InetAddress.getLocalHost` 方法的返回值。

**Port:**

- `spark.driver.port` 属性。
- 从操作系统(OS)选择一个临时端口。

### Spark Standalone Master / Worker actor systems:

**Hostname:**

- 当 `Master` 或 `Worker` 进程启动时使用 `--host` 或 `-h` 选项(或是过期的选项 `--ip` 或 `-i`)。
- `SPARK_MASTER_HOST` 环境变量(仅应用在 `Master` 上)。
- 如果 `SPARK_LOCAL_IP` 环境变量的设置是主机名(hostname)，就会使用设置时的主机名。如果 `SPARK_LOCAL_IP` 设置的是一个 IP 地址，这个 IP 地址会被解析为主机名。
- 使用默认的 IP 地址，这个 IP 地址是Java 接口 `InetAddress.getLocalHost` 方法的返回值。

**Port:**

- 当 `Master` 或 `Worker` 进程启动时使用 `--port` 或 `-p` 选项。
- `SPARK_MASTER_PORT` 或 `SPARK_WORKER_PORT` 环境变量(分别应用到 `Master` 和 `Worker` 上)。
- 从操作系统(OS)选择一个临时端口。

[阅读原文](http://databricks.gitbooks.io/databricks-spark-knowledge-base/content/troubleshooting/connectivity_issues.html)
