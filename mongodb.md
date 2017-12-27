### MongoDB记录

Community Edition

[安装见官网](https://docs.mongodb.com/manual/installation/)

### 启动MongoDB服务

##### 方式一：直接启动

1.创建MongoDB数据目录，在任意地方，例如 C:\data\db

2.以配置数据路径启动MongoDB

```
"C:\Program Files\MongoDB\Server\3.6\bin\mongod.exe" --dbpath "c:\data\db"
```

3.连接MongoDB

使用mongo.exe，或者MongoCompass，或者使用驱动

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

5.连接使用同方式一:3

6.关闭MongoDB

```
net stop MongoDB
```

7.删除MongoDB服务

```
"C:\Program Files\MongoDB\Server\3.6\bin\mongod.exe" --remove
```

##### 方式三：手动添加MongoDB服务（此方式同添加其他WINDOWS服务一样）

1.创建目录

同方式二

2.创建配置文件

同方式二

3.创建MongoDB服务

```
sc.exe create MongoDB binPath= "\"C:\Program Files\MongoDB\Server\3.6\bin\mongod.exe\" --service --config=\"C:\Program Files\MongoDB\Server\3.6\mongod.cfg\"" DisplayName= "MongoDB" start= "auto"
```

如果创建成功，将显示如下消息

```
[SC] CreateService SUCCESS
```

4.启动服务

同方式二

5.连接MongoDB

同方式二

6.关闭服务

同方式二

7.删除服务

```
sc.exe delete MongoDB
```



