## 使用grpc进行远程调用

grpc在golang中简单使用案例。使用共有四种请求返回组合，这里记录两种最典型的。

准备好protoc、protoc-gen-go 详见官网

编写服务所需proto文件，如下：

```
syntax = "proto3";
package grpc;

service Controller {
    rpc SayHello (HelloRequest) returns (HelloResponse) {}
    rpc SayStream (stream RequestStreamObj) returns (stream ResponseStreamObj) {}
}

message HelloRequest {
    string name = 1;
}

message HelloResponse {
    string message = 1;
}

message RequestStreamObj {
    int32 requestid = 1;
    string data = 2;
}

message ResponseStreamObj {
    int32 responseid = 1;
    string data = 2;
}
```

注：packge必须和将要使用的路径一致。

放入protoc 和 protoc-gen-go同一目录中

创建生成脚本，并执行，生成rpcmsg.pb.go文件

```
protoc --go_out=plugins=grpc:. ./rpcmsg.proto
```

将rpcmsg.pb.go复制致对应路径

