### Linux相关操作

1.输入su命令获取root权限时， 出现了authentication failure 的问题，即身份验证失败

```
$sudo passwd
Password:当前密码
Enter new UNIX password: root用户新密码
Retype new UNIX password:再次输入root用户密码
```

2.新系统安装ssh

```
sudo apt-get install openssh-server

端口重新配置
vi /etc/ssh/sshd_config 内port 改为需要端口
systemctl restart sshd.service 重启
```

3.账户管理

```
查看所有用户 cat etc/passwd
查看所有用户组 cat etc/group
创建用户 useradd <username>
创建并指定用户有效期 useradd -e 12/30/2018 <username>
创建并指定用户用户ID useradd -u <username>
修改用户名 usermod -l <oldname> <newname>
将用户加入到其他组 usermod -g <oldgroup> <newgroup>
修改用户目录 usermod -d <path> <username>
删除用户 userdel <username>
删除用户并删除工作目录 userdel -r <username>
查看用户信息 id <username>
查看用户详细信息 finger <username>
创建用户组 groupadd -g <gid> <groupname>
为用户组添加新成员 gpasswd -a <username> <groupname>
把用户退出组 gpasswd -d <username> <groupname>
修改组名 groupmod -n <oldname> <newname>
删除用户组 groupdel <groupname>

etc/passwd中字段意义
用户名：密码：用户ID：组ID：描述：主目录：默认shell
第二个字段就是密码，一般加密后放在/etc/shadow里
etc/group中
组名:口令:组标识号:组内用户列表(如果字段为空表示用户组为GID的用户名)
```

4.文件权限

```
ls -l
显示如下 -rwxrw-r‐-1 root root 1213 Feb 2 09:39 abc
前10个字符：
第一个字符：(-)文件，(d)目录，(l)链接
其余三个为一组
第一组rwx:文件所有者的权限是读、写和执行
第二组rw-:与文件所有者同一组的用户的权限是读、写但不能执行
第三组r--:不与文件所有者同组的其他用户的权限是读不能写和执行
数字表示，r=4,w=2,x=1，因此rwx=7
1表示连接文件数
root表示用户
root表示用户所在组
1213表示文件大小（字节）
Feb 2 09:39 表示最后修改时间
abc 文件名称

改变权限的命令
chmod 改变文件或目录的权限
chmod 755 abc：赋予abc权限rwxr-xr-x
chmod u=rwx，g=rx，o=rx abc：同上u=用户权限，g=组权限，o=不同组其他用户权限
chmod u-x，g+w abc：给abc去除用户执行的权限，增加组写的权限
chmod a+r abc：给所有用户添加读的权限
```

f.防火墙相关

f.1firewalld的基本使用

```
启动： systemctl start firewalld
查看状态： systemctl status firewalld
停止： systemctl disable firewalld
禁用： systemctl stop firewalld
```

f.2systemctl是CentOS7的服务管理工具中主要的工具，它融合之前service和chkconfig的功能于一体。

```
启动一个服务：systemctl start firewalld.service
关闭一个服务：systemctl stop firewalld.service
重启一个服务：systemctl restart firewalld.service
显示一个服务的状态：systemctl status firewalld.service
在开机时启用一个服务：systemctl enable firewalld.service
在开机时禁用一个服务：systemctl disable firewalld.service
查看服务是否开机启动：systemctl is-enabled firewalld.service
查看已启动的服务列表：systemctl list-unit-files|grep enabled
查看启动失败的服务列表：systemctl --failed
```

f.2配置firewalld-cmd

```
查看版本： firewall-cmd --version
查看帮助： firewall-cmd --help
显示状态： firewall-cmd --state
查看所有打开的端口： firewall-cmd --zone=public --list-ports
更新防火墙规则： firewall-cmd --reload
查看区域信息:  firewall-cmd --get-active-zones
查看指定接口所属区域： firewall-cmd --get-zone-of-interface=eth0
拒绝所有包：firewall-cmd --panic-on
取消拒绝状态： firewall-cmd --panic-off
查看是否拒绝： firewall-cmd --query-panic
```

f.4开启一个端口

```
添加
firewall-cmd --zone=public --add-port=80/tcp --permanent    （--permanent永久生效，没有此参数重启后失效）
重新载入
firewall-cmd --reload
查看
firewall-cmd --zone= public --query-port=80/tcp
删除
firewall-cmd --zone= public --remove-port=80/tcp --permanent
```

f.5 ufw防火墙

```
安装
apt-get install ufw 
开启/关闭
ufw enable/disable
查看状态
ufw status
系统启动自动开启
ufw default deny
添加或禁止可访问端口
ufw allow|deny <port>/tcp 
删除访问端口
ufw delete allow <port>/tcp
```



