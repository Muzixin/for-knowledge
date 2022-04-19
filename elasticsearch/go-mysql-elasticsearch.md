# go-mysql-elasticsearch

### go-mysql-elasticsearch简介

go-mysql-elasticsearch是一个将MySQL数据自动同步到Elasticsearch的服务。

它首先使用mysqldump获取原始数据，然后用binlog增量地同步数据。

github地址：[https://github.com/go-mysql-org/go-mysql-elasticsearch](https://github.com/go-mysql-org/go-mysql-elasticsearch)



### 编译go-mysql-elasticsearch

建议在centos或者ubuntu上操作

```
//安装go
yum install -y go
apt-get install go

//设置环境变量
go env -w GOPROXY=https://goproxy.cn,direct

//拉取源码
go get -u github.com/siddontang/go-mysql-elasticsearch

//编译本系统对应可执行程序
GO111MODULE=on go build -o bin/go-mysql-elasticsearch ./cmd/go-mysql-elasticsearch
//编译windows下可执行程序
GO111MODULE=on GOOS=windows GOARCH=amd64 go build -o bin/go-mysql-elasticsearch.exe ./cmd/go-mysql-elasticsearch
```

