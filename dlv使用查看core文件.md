GOTRACEBACK=crash nohup ./clubserver -clear=true & 崩溃会有core文件

dlv core ./clubserver core.9090 以core文件启动

goroutines 查看所有协程

goroutine 1202 查看协程根据编号

bt 查看详细 