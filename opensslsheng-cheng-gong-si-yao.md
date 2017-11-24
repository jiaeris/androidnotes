## 使用Openssl生成公私钥

在ubuntu环境下获取openssl功能工具

```
apt-get install openssl
```

进入生成文件存放目录开启openssl

```
openssl
```

开始生成1024位私钥

```
genrsa -out rsa_private_key.pem 1024
```

将私钥转为pkcs8格式

```
pkcs8 -topk8 -inform PEM -in rsa_private_key.pem -outform PEM -nocrypt
```

根据私钥生成公钥

    `rsa -in rsa_private_key.pem -pubout -out rsa_public_key.pem`

