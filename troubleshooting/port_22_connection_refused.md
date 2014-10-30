# 执行 start-all.sh 错误: Connection refused

如果是使用 Mac 操作系统运行 start-all.sh 发生下面错误时：

```
% sh start-all.sh
starting org.apache.spark.deploy.master.Master, logging to ...
localhost: ssh: connect to host localhost port 22: Connection refused
```

你需要在你的电脑上打开 `远程登录` 功能。进入 `系统偏好设置` ---> `共享` 选择打开 `远程登录`。