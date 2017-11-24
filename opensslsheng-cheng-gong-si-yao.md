## 使用Openssl生成公私钥

在ubuntu环境下获取openssl功能工具

```
apt-get install openssl
```

进入生成文件存放目录开启openssl

    `openssl`

开始生成1024位私钥

    `genrsa -out rsa_private_key.pem 1024`

