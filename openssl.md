## 使用Openssl生成公私钥

在ubuntu环境下获取openssl功能工具

```
apt-get install openssl
```

默认情况下openssl输出格式为PKCS\#1-PEM

生成RSA私钥【无加密】

```
openssl genrsa -out rsa_private_key.pem 1024
```

生成RSA公钥

```
openssl rsa -in rsa_private_key.pem -pubout -out rsa_public_key.pem
```

生成RSA私钥【使用aes256加密】

```
openssl genrsa -aes256 -passout pass:1203 -out rsa_aes_private_key.pem 2048
```

其中passout 代替shell进行密码输入，否则会提示输入密码。

生成RSA公钥-根据aes256加密的私钥

```
openssl rsa -in rsa_aes_private_key.pem -passin pass:1203 -pubout -out rsa_public_key.pem
```

转换命令

私钥转非加密

```
openssl rsa -in rsa_aes_private_key.pem -passin pass:1203 -out rsa_private_key.pem
```

私钥转加密

```
openssl rsa -in rsa_private_key.pem -aes256 -passout pass:1203 -out rsa_aes_private_key.pem
```

私钥PEM转DER

```
openssl rsa -in rsa_private_key.pem -outform der-out rsa_private.der
```

-inform和-outform 参数制定输入输出格式，由der转pem格式同理

查看私钥明细

```
openssl rsa -in rsa_private_key.pem -noout -text
```

使用-pubin参数查看公钥明细

将私钥转为pkcs8格式

```
openssl pkcs8 -topk8 -inform PEM -in rsa_private_key.pem -outform PEM -out pkcs8_private_key.pem -nocrypt
```



