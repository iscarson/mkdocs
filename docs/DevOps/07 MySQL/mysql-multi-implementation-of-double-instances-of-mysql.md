## 1、添加mysql用户
以root登录，新建mysql用户组
```
 groupadd mysql
 useradd -d /data/mariadb -g mysql -m mysql
```
## 2、 mariadb的安装置
### 2.1 安装所需组件
```
 yum install -y cmake
 yum install -y gcc
 yum install -y gcc-c++
 yum install -y ncurses-devel
 yum install -y libaio
 yum install -y bison 
```
### 2.2创建mariadb编译根目录
```
mkdir -p /usr/local/mariadb
```
2.3解压缩源码包
```
tar zxvf mariadb-5.5.51.tar.gz 
cd mariadb-5.5.51
```
### 2.4编译安装
```
cd /usr/local/mariadb/mariadb-5.5.51/
cmake -DCMAKE_INSTALL_PREFIX=/usr/local/mariadb -DMYSQL_DATADIR=/data/mariadb -DSYSCONFDIR=/usr/local/mariadb -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_ARCHIVE_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_PERFSCHEMA_STORAGE_ENGINE=1 -DMYSQL_UNIX_ADDR=/usr/local/mariadb/mysql.sock
make     
make install   
```
### 2.5修改环境变量文件，修改vi ~/.bash_profile，添加如下内容；
```
PATH=$PATH:$HOME/bin:/usr/local/mariadb/bin
export PATH
添加完成后，source ~/.bash_profile
```
## 3.mariadb双实例的建立
### 3.1建立两个实例所需目录并授权
```
 makdir -p /usr/local/mariadb1_3307
 makdir -p /usr/local/mariadb2_3308
 makdir -p /data/mariadb1_3307
 makdir -p /data/mariadb2_3308
```
### 3.2 拷贝配置文件到/etc目录下
```
 cp /usr/localmariadb/support-files/my-medium.cnf/my.cnf /etc/my.cnf 
```
### 3.3 配置mariadb1实例1
 3.3.1修改配置文件为
```
[mysqld_multi]
mysqld=/usr/local/mariadb/bin/mysqld_safe
mysqladmin=/usr/local/mariadb/bin/mysqladmin
log=/data/mariadb/mysqld_multi.log
[mysqld1]
port = 3307
socket = /usr/local/mariadb1_3307/mysql.sock
basedir = /usr/local/mariadb
datadir = /data/mariadb1_3307
pid-file = /data/mariadb1_3307/mysql.pid
user = mysql
bind-address = 0.0.0.0
server-id = 1230
slave-skip-errors=all
binlog=/data/mariadb1_3307/binlog
relay-log=/data/mariadb1_3307/relay_log/mysql-relay-bin
log_slave_updates = 1
read_only = 0
binlog_format = row
expire_logs_days = 10
default_storage_engine = InnoDB
innodb_buffer_pool_size = 128M
innodb_flush_log_at_trx_commit = 0
```
 3.3.2 初始化数据库
进入bin目录，
```
cd /usr/local/mariadb/scripts
 ./mysql_install_db --user=mysql --basedir=/usr/local/mariadb --datadir=/data/mariadb1_3307
```
### 3.4配置mariadb2实例2
方法同实例1
4、启动多实例数据库
### 4.1启动数据库实例
```
 mysqld_multi start 1-2
```
### 4.2关闭防火墙
```
systemctl stop firewalld.service    
systemctl disable firewalld.service   
查看：netstat -tnlp
```
### 4.3 修改环境变量文件，修改vi ~/.bash_profile，添加如下内容；
```
PATH=$PATH:/usr/local/mariadb/bin
export PATH
添加完成后，source ~/.bash_profile
```
### 4.4访问数据库
```
 mysql -uroot -p -S /usr/local/mariadb1_3307/mysql.sock   --实例1
 mysql -uroot -p -S /usr/local/mariadb2_3308/mysql.sock   --实例2
```
### 4.5 查看多实例
```
mysqld_multi report
```
### 4.6 关闭数据库
刚开始无法使用mysql_multi关闭数据库
原因：需对账号授权
 ```
mysqld_multi stop 1,2
```
### 4.6 完成