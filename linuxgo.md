linux中安装golang

1. 下载压缩包，解压
2. 配置环境变量（/etc/profile.d 目录下创建go.sh，加入如下环境变量）
```bash
export GOROOT=/home/yunga/go 
export GOPATH=/mnt/d/WorkSpace/GW 
export PATH=$PATH:$GOROOT/bin:$GOPATH
```