### 生成公私钥

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

以上使用-nocrypt参数，输出无加密的pkcs8私钥

但是pkcs8默认使用des3加密算法，如下使用：

```
openssl pkcs8 -topk8 -in rsa_private_key.pem -passout pass:1203 -out pkcs8_private_key.pem
```

### 生成CA证书
- 创建服务器证书密钥文件 server.key：
 ```
openssl genrsa -des3 -out server.key 1024
```
输入密码，确认密码，自己随便定义，但是要记住，后面会用到。
- 创建服务器证书的申请文件 server.csr
```
openssl req -new -key server.key -out server.csr
```
输出内容为：
Enter pass phrase for root.key: ← 输入前面创建的密码 

Country Name (2 letter code) [AU]:CN ← 国家代号，中国输入CN 

State or Province Name (full name) [Some-State]:BeiJing ← 省的全名，拼音 

Locality Name (eg, city) []:BeiJing ← 市的全名，拼音 

Organization Name (eg, company) [Internet Widgits Pty Ltd]:MyCompany Corp. ← 公司英文名 

Organizational Unit Name (eg, section) []: ← 可以不输入 

Common Name (eg, YOUR name) []: ← 此时不输入 

Email Address []:admin@mycompany.com ← 电子邮箱，可随意填


Please enter the following ‘extra’ attributes 

to be sent with your certificate request 

A challenge password []: ← 可以不输入 

An optional company name []: ← 可以不输入

- 备份一份服务器密钥文件
```
cp server.key server.key.org
```
- 去除文件口令
```
openssl rsa -in server.key.org -out server.key
```
- 生成证书文件server.crt
```
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```