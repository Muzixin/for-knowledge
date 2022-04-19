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

### 修改配置文件river.toml

```
# MySQL 配置：地址，用户名，密码
my_addr = "ip:3306"     
my_user = "root"
my_pass = "***"

# Elasticsearch地址
es_addr = "ip:端口"
    
# 存储数据的位置
data_dir = "./var"
    
# Inner Http status address
stat_addr = "127.0.0.1:12800"
    
# pseudo server id like a slave
server_id = 1001
    
# mysql or mariadb
flavor = "mysql"
    
# mysql备份文件，如果是在windows环境下最好直接指定mysqldump.exe的绝对路径
# mysqldump = "C:\\Program Files\\MySQL\\MySQL Server 8.0\\bin\\mysqldump.exe"
    
# minimal items to be inserted in one bulk
bulk_size = 128
    
# force flush the pending requests if we don't have enough items >= bulk_size
flush_bulk_time = "200ms"
    
# Ignore table without primary key
skip_no_pk_table = false
    
# MySQL数据源，schema：数据库，tables：表
[[source]]
schema = "test"
tables = ["link_info"]

[[rule]]
schema = "test"
table = "link_info"
index = "test_mysql2"
type = "link_info"
```
