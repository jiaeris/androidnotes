### MongoDB记录

Community Edition

[安装见官网](https://docs.mongodb.com/manual/installation/)

#### 启动方式

##### 方式一：为MongoDB配置一个Windows服务

1.创建目录

在任意盘中创建db\(数据保存目录\)和log\(日志保存目录\)。

例如在C盘中创建：

mkdir C:\data\db

mkdir C:\data\log

2.创建一个配置文件

配置文件示例如下

```
systemLog:
    destination: file
    path: c:\data\log\mongod.log
storage:
    dbPath: c:\data\db
```

创建配置文件必须包含systemLog.path即系统日志目录，根据需要包含其他配置信息。

3.

4.

