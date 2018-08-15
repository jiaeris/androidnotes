GOTRACEBACK=crash nohup ./clubserver -clear=true & 崩溃会有core文件

dlv core ./clubserver core.9090 以core文件启动

goroutines 查看所有协程

goroutine 1202 查看协程根据编号

bt 查看详细 

```
(dlv) bt
 0  0x000000000045f5b4 in runtime.raise
    at /usr/lib/golang/src/runtime/sys_linux_amd64.s:113
 1  0x000000000045b9d0 in runtime.systemstack_switch
    at /usr/lib/golang/src/runtime/asm_amd64.s:298
 2  0x000000000042d2e8 in runtime.dopanic
    at /usr/lib/golang/src/runtime/panic.go:586
 3  0x000000000042ce7e in runtime.gopanic
    at /usr/lib/golang/src/runtime/panic.go:540
 4  0x000000000042bbae in runtime.panicmem
    at /usr/lib/golang/src/runtime/panic.go:63
 5  0x000000000044423c in runtime.sigpanic
    at /usr/lib/golang/src/runtime/signal_unix.go:367
 6  0x0000000000a36d96 in goserver/clubserver/clubmsghandler.(*club).TableMgr
    at /root/gopath/src/goserver/clubserver/clubmsghandler/club.go:175
 7  0x0000000000a3c392 in goserver/clubserver/clubmsghandler.(*ClubTable).Save
    at /root/gopath/src/goserver/clubserver/clubmsghandler/club_table.go:158
 8  0x0000000000a3c8eb in goserver/clubserver/clubmsghandler.(*ClubTable).SetGameServerTableID
    at /root/gopath/src/goserver/clubserver/clubmsghandler/club_table.go:191
 9  0x0000000000a3a6c4 in goserver/clubserver/clubmsghandler.(*ClubService).CreateGameServerTable
    at /root/gopath/src/goserver/clubserver/clubmsghandler/club_service.go:36
10  0x000000000084bc66 in goserver/protocol/clubproto._ClubService_CreateGameServerTable_Handler
    at /root/gopath/src/goserver/protocol/clubproto/protocol.pb.go:223
```

frame n (需要看的帧数)

```
(dlv) frame 9
> runtime.raise() /usr/lib/golang/src/runtime/sys_linux_amd64.s:113 (PC: 0x45f5b4)
Warning: debugging optimized function
Frame 9: /root/gopath/src/goserver/clubserver/clubmsghandler/club_service.go:36 (PC: a3a6c4)
Warning: listing may not match stale executable
    31:			roomInfo := roominfo.GetRoomServerInfo(int(msg.GetGameTableId()))
    32:			if roomInfo == nil || roomInfo.ServerID == 0 {
    33:				slog.Warn("游戏开始，把用户加入游戏服，游戏服房间不存在")
    34:				return voidObj, nil
    35:			}
=>  36:			clubTable.SetGameServerTableID(roomInfo.ServerID, msg.GetGameTableId())
    37:			//丢入游戏服桌子
    38:			for _, userId := range clubTable.Users {
    39:				u := userMgr.find(userId)
    40:				st := u.GetClubStatusTable()
    41:				var gwServerId int

```

p x  (输出对应变量的值)

```
(dlv) p tbMgr
*goserver/clubserver/clubmsghandler.TableManager {
	clubId: 562,
	redisTables: *goserver/redisproxy.RedisHash {
		redisPool: *(*goserver/redisproxy/proxy.PoolProxy)(0xc42029f5b0),
		key: "club:562.tables",},
	tableListeners: *goserver/redisproxy.RedisList {
		redisPool: *(*goserver/redisproxy/proxy.PoolProxy)(0xc42029f5b0),
		key: "club:562.tableListeners",},
	tableJoinLockerMap: map[uint32]*goserver/redisproxy.RedisMutex [],
	RedisMutex: *goserver/redisproxy.RedisMutex {
		redisPool: *(*goserver/redisproxy/proxy.PoolProxy)(0xc42029f5b0),
		key: "club:table_mgr_locker:type.create:club_id.562",
		value: "C/UFmHWSHmaKW98sf8SERZLSVyvNBmjS1sUvUFTi0IM=",},}
```