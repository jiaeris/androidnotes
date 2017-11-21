## docker常用命令

docker官网 [https://www.docker.com](https://www.docker.com/ "Docker官网")

docker中国[ https://www.docker-cn.com](https://www.docker-cn.com)

参考资料 docker技术入门与实战

说明：&lt;&gt;内表参数

### 第一部分：镜像

本地镜像列表

```
docker images
```

搜索镜像

```
docker search <image-name>
```

获取镜像

```
docker pull <image-name>

注：默认获取的为最新版本
```

获取指定版本镜像

```
docker pull <image-name>:<version-number>
```

从其他注册服务器仓库获取镜像

```
docker pull <host>:<port>/<image-name>
```

给镜像添加标签

```
docker tag <old-repository>/<old-tag> <new-repository>/<new-tag>

注：添加标签并不会重新保存镜像，因此，在images列表中将出现指向同一imageid的镜像，实际标签只起到
引用或快捷方式的作用。
```

获取镜像详细信息

```
docker inspect <image-id>|<repository>
```

删除镜像

```
docker rmi <image-id>|<repository>
```

基于已有容器创建新镜像

```
docker commit <options> <container-id> <repository:tag>

可选参数：

-a,--author=" "作者信息

-m,--message=" "提交消息

-p,--pause=true 提交时暂停容器运行

simple: docker commit -a "YUNGA" -m "added a new file" a925cb40b3f0 test:latest
```

基于模板导入镜像

```
docker import - ubuntu:14.04

模板可在OPENVZ下载
```

存出镜像

```
docker save -o <new-image-name>.tar <repository:tag>
```

载入镜像

```
docker load --input <image-name>.tar
```

上传镜像

```
docker push <repository:tag>
```

### 第二部分：容器

新建容器

```
docker create -itd <image-id>|<repository:tag>

-t 让docker分配一个伪终端（pseudo-tty)并绑定到容器标准输入上
-i 让容器打开标准输入
-d 使得容器在后台以守护态（daemonized）形式运行
```

启动/关闭/重启容器

```
docker start/stop/restart <container-id>|<container-name>
```

创建并启动容器

```
docker run -p <system-port>:<container-port> --name <container-name> -it -v <system-path>:<container-path> <command>

以下是一个使用例子：
docker run -p 10011:10086 --name webc -it -v /yunga:/yunga ubuntu /bin/bash

注：
-p 指定映射端口，系统端口:容器内端口（-P 若将-p改写为大写-P则自动根据容器端口映射主机端口）
--name 指定创建容器的名字NAME
-it 同新建容器
-v 挂载数据卷（挂载普通数据卷，直接跟上数据卷路径。挂载主机目录作为数据据卷，主机目录:容器目录），若路径不存在会自动创建
command 启动容器时第一时间执行的liunx命令
```

运行中容器

```
docker ps
```

所有容器

```
docker ps -a
```

删除容器

```
docker rm <container-id>|<container-name>

注：参数为容器的ID或者容器的Name
```

进入容器

```
docker attach <container-id>|<container-name>

docker exec -ti <container-id>|<container-name> /bin/bash
```

导出容器

```
docker export <container-id>|<container-name>

simple ： docker export ce5 >test-for-run.tar

注：导出容器不区分容器的运行状态
```

导入容器

    `cat <file>.tar | docker import - <repository:tag>`

