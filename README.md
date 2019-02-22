# mysql5.7 elasticsearch数据同步

## 前提
- [x] mysql 5.7 (开启 binlog row)
- [x] go环境
- [x] es环境安装

`es 环境安装参考github:`[es-head docker 安装](https://github.com/longfens/es-head)

##### 1.下载 go-mysql-elasticsearch

 - `go get github.com/siddontang/go-mysql-elasticsearch` 
 - `cd $GOPATH/src/github.com/siddontang/go-mysql-elasticsearch` 通过 go env 查看go运行环境
 - `make`

##### 2. 配置文件 river.toml

```
#基础配置 本地目录在/data/river.toml
# MySQL 的相关配置
# 指定用户必须具备复制权限
my_addr = "127.0.0.1:3306"
my_user = "root"
my_pass = "123456"

# ES 相关配置
es_addr = "127.0.0.1:9200"
es_user = ""
es_pass = ""

# 数据源配置
# 以 Slave 模式工作
server_id = 1
# mysql/mariadb
flavor = "mysql"

# mysqldump 路径，如果为空或者未设置，会跳过这一环节。
mysqldump = "mysqldump"
bulk_size = 128
flush_bulk_time = "200ms"
skip_no_pk_table = false


[[source]]
# 数据库名称
schema = "estest"
# 数据表同步范围，支持通配符
tables = ["news"]

# 规则定义
[[rule]]
# 数据库名称
schema = "estest"
# 规则对应的数据表，支持通配符
table = "news"
# 目标 ES 索引
index = "news_index"
# 该规则在 ES 中生成的文档类型
type = "log_db"
```
##### 3.运行执行
`./bin/go-mysql-elasticsearch -config /data/river.toml`

*go-mysql-elasticsearch 首次会全量同步数据，后面会差分同步数据*
> 附几张图
>![效果图](https://img-blog.csdnimg.cn/20190104143518560.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3psNDk0ODA1MDk1,size_16,color_FFFFFF,t_70)
