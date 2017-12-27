### MongoDB记录

Community Edition

[安装见官网](https://docs.mongodb.com/manual/installation/)

#### 启动方式

##### 方式一：直接启动

##### 方式二：为MongoDB配置一个Windows服务

1.创建目录

在任意盘中创建db\(数据保存目录\)和log\(日志保存目录\)。

例如在C盘中创建：

```
mkdir C:\data\db
mkdir C:\data\log
```

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

3.安装MongoDB服务

以下所有命令请在管理员权限下执行

通过mongod.exe安装MongoDB服务，使用 --install 和 --config 选项关联之前的配置文件

```
"C:\Program Files\MongoDB\Server\3.6\bin\mongod.exe" --config "C:\Program Files\MongoDB\Server\3.6\mongod.cfg" --install
```

路径视自己的配置路径而定

4.启动MongoDB服务

```
net start MongoDB
```

查看log文件，最后一行为：\[initandlistn\] waiting for connections on port 21017表示启动成功

5.连接MongoDB

使用mongo.exe，或者MongoCompass，或者使用驱动

6.关闭MongoDB

    net stop MongoDB

