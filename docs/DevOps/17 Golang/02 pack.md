# 打包
# go打包命令
```bash
go build                      常规打包方法
go build -ldflags '-w -s'     使用 “-dflags” 缩小大小
upx  ...二进制文件              使用upx打包为最小程序
```


# 本机打包记录
```bash
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o target/ranksrv_`date +%Y_%m_%d` ${SRCPATH}
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags '-w -s' -o pkg/prome_ali 
```


# 各个平台打包说明
* Mac 打包Linux  windows

```bash
$ CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build test.go
$ CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build test.go
```
* Linux打包Mac windows

```bash
$ CGO_ENABLED=0 GOOS=darwin GOARCH=amd64 go build test.go
$ CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build test.go
```
* windows编译Linux Mac

```bash
$ SET CGO_ENABLED=0SET GOOS=darwin3 SET GOARCH=amd64 go build test.go
$ SET CGO_ENABLED=0 SET GOOS=linux SET GOARCH=amd64 go build test.go
```
* 参数说明

```bash
GOOS：目标可执行程序运行操作系统，支持 darwin，freebsd，linux，windows
GOARCH：目标可执行程序操作系统构架，包括 386，amd64，arm
```