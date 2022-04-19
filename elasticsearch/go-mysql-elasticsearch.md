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

### Mysql配置开启binlog

Mysql的binlog必须是ROW模式，不然启动会报错。 连接Mysql的用户权限需要大一些。

首先查询一下是否开启了binlog

```
#进入mysql
mysql -uroot -p
#输入密码，然后输入如下命令查看binlog开启状态
show variables like '%log_bin%';
#，ON为开启了，如果没有开启的话为OFF。
```

1、windows

打开my.ini文件，在mysqld下面添加,保存文件，重启mysql服务

```
log_bin=mysql-bin
binlog-format=ROW
server-id=1
```

2、linux

需要在my.cnf配置中添加如下配置(其中server-id可以根据情况设置，这里设置为1，log-bin为日志位置，一定要给日志写的权限，不然会报错，binlog\_format为模式，这里必须为ROW)：

```
server-id=1
log-bin=/usr/local/mysql-log/mysql-bin.log
binlog_format="ROW"
//重启Mysql
service mysqld restart
```

### 运行同步脚本

```
//Linux
bin/go-mysql-elasticsearch -config=river.toml

//windows
bin/go-mysql-elasticsearch.exe -config=river.toml
```

### 注意事项

1、尽量不要在实时同步数据的同时修改表结构，可能会导致异常错误

2、同步脚本可能会异常断开连接，重启即可，会自动根据同步未同步的数据

3、若重启脚本仍异常，则删除var/master.info文件后重启即可，会同步所有数据
