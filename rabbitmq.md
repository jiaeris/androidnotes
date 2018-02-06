### 在ubuntu中安装RabbitMQ-Server
地址：
```http request
http://www.rabbitmq.com/install-debian.html
```
账户配置(例如配置密码为root的账户)：
1. 创建一个test用户：rabbitmqctl add_user root root
2. 设置该用户为administrator角色：rabbitmqctl set_user_tags root  administrator
3. 设置权限： rabbitmqctl  set_permissions  -p  '/'  root '.' '.' '.'
4. 重启rabbitmq服务：sudo service rabbitmq-server restart