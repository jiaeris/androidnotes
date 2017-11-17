## 使用grpc进行远程调用

### 文件生成

grpc在golang中简单使用案例。使用共有四种请求返回组合，这里记录两种最典型的。详见[grpc.io](/grpc.io)

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

### 代码中使用

server.go

```
const (
	port = ":10086"
)

type data int

func openServer() {
	l, err := net.Listen("tcp", port)
	if err != nil {
		panic(err)
	}
	s := grpc.NewServer()
	var x data
	RegisterControllerServer(s, &x)
	if err = s.Serve(l); err != nil {
		panic(err)
	}
}

func (d *data) SayHello(ctx context.Context, req *HelloRequest) (res *HelloResponse, err error) {
	reqMsg := req.GetName()
	fmt.Println("server: ", reqMsg)
	res = &HelloResponse{
		Message: "hello " + reqMsg,
	}
	return res, err
}

func (d *data) SayStream(stream Controller_SayStreamServer) error {
	//开启接收
	for {
		req, err := stream.Recv()
		if err == io.EOF {
			//return err
			break
		}
		if err != nil {
			//fmt.Println(err)
			//return err
			break
		}
		fmt.Println("reqeustid:", req.Requestid, "requestdata:", req.Data)
		time.Sleep(time.Second * 5) //假设处理5s
		
		//收到后返回消息
		res := &ResponseStreamObj{
			Responseid: req.Requestid,
			Data:       "response data",
		}
		stream.Send(res)
	}
	return nil
}
```

client.go

```
const (
	address = "localhost:10086"
	times   = 100
)

func openClient() {

	//模拟100个客户端并发请求
	i := 0
	wg := sync.WaitGroup{}
	for i < times {
		wg.Add(1)
		go func(i int) {
			client, err := grpc.Dial(address, grpc.WithInsecure())
			if err != nil {
				fmt.Println(err)
			}
			defer client.Close()
			cc := NewControllerClient(client)
			//单体调用
			res, err := cc.SayHello(context.Background(), 
						&HelloRequest{Name: "Yunga" + strconv.Itoa(i)}, 
						grpc.FailFast(true))
			if err != nil {
				fmt.Println(err)
			}
			fmt.Println("clent1: ", res.GetMessage())
			
			//流体调用
			streamClient, err := cc.SayStream(context.Background(), grpc.FailFast(true))
			resCh := make(chan string)
			go func() {//开启接收线程
				for {
					res, err := streamClient.Recv()
					if err != nil {
						//fmt.Println(err)
						break
					}
					fmt.Println("responseid:", res.Responseid, "responsedata:", res.Data)
					resCh <- "ok"
				}
			}()
			//开启请求
			req := &RequestStreamObj{
				Requestid: int32(i),
				Data:      "request data",
			}
			streamClient.Send(req)
			<-resCh
			streamClient.CloseSend()
			wg.Done()
		}(i)
		i++
	}
	wg.Wait()
	fmt.Println("clients request finshed.")
}
```



