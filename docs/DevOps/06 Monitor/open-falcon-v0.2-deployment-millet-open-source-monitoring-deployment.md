## 所依赖其他服务

* memcached
* redis

yum安装即可

## go环境配置

* 下载go

```
cd /usr/local/src
wget https://golang.org/doc/install?download=go1.5.2.linux-amd64.tar.gz
```

* 解包

```
cd /usr/local/src
tar -C /usr/local -xzf go1.5.2.linux-amd64.tar.gz
```

* 新建gopath

```
mkdir /usr/local/gopkg
```

* 配置go环境变量

```
vim /etc/profile
```

* 添加如下信息

```
export GOROOT=/usr/local/go
export GOPATH=/usr/local/gopkg
export PATH=$GOROOT/bin:$PATH
```

* 刷新配置文件：

```
source /etc/profile
```

* 查看go版本：

```
[root@codis01 ~]#go version
go version go1.5.2 linux/amd64
```

## 编译open-falcon

```
cd $GOPATH/src/github.com/open-falcon/
git clone https://github.com/open-falcon/falcon-plus.git

make all

make pack
```
这时候，你会在当前目录下面，得到open-falcon-v0.2.0.tar.gz的压缩包，就表示已经编译和打包成功了。

## 安装open-falcon

```
cp $GOPATH/src/github.com/open-falcon/falcon-plus/open-falcon-v0.2.0.tar.gz /usr/local/src

cd /usr/local/src/
tar zxvf open-falcon-v0.2.0.tar.gz -C /usr/local/open-falcon

```

## 修改配置文件

open-falcon每个组件的配置文件都存放在该目录下的config下，修改相应地址与数据库信息即可


## 导入数据库

```
cd /usr/local/src/ && git clone https://github.com/open-falcon/falcon-plus.git 
cd /usr/local/src/falcon-plus/scripts/mysql/db_schema/

mysql -h 127.0.0.1 -uroot -p < 1_uic-db-schema.sql
mysql -h 127.0.0.1 -uroot -p < 2_portal-db-schema.sql
mysql -h 127.0.0.1 -uroot -p < 3_dashboard-db-schema.sql
mysql -h 127.0.0.1 -uroot -p < 4_graph-db-schema.sql
mysql -h 127.0.0.1 -uroot -p < 5_alarms-db-schema.sql
```

## 启动open-falcon后端服务


* 启动

```
cd /usr/local/open-falcon
./open-falcon start
```

* 检查服务状态

```
./open-falcon check
```
* 更多的命令行工具用法

```
# ./open-falcon [start|stop|restart|check|monitor|reload] module
./open-falcon start agent

./open-falcon check
        falcon-graph         UP           53007
          falcon-hbs         UP           53014
        falcon-judge         UP           53020
     falcon-transfer         UP           53026
       falcon-nodata         UP           53032
   falcon-aggregator         UP           53038
        falcon-agent         UP           53044
      falcon-gateway         UP           53050
          falcon-api         UP           53056
        falcon-alarm         UP           53063

For debugging , You can check $WorkDir/$moduleName/log/logs/xxx.log
```

## 安装dashboard

* 克隆代码

```
cd /usr/local/open-falcon
git clone https://github.com/open-falcon/dashboard.git
```

* 安装依赖

```
yum install -y python-virtualenv
yum install -y python-devel
yum install -y openldap-devel
yum install -y mysql-devel
yum groupinstall "Development tools"


cd /usr/local/open-falcon/dashboard/
virtualenv ./env

./env/bin/pip install -r pip_requirements.txt -i http://pypi.douban.com/simple
```

* 修改配置

```
dashboard的配置文件为： 'rrd/config.py'，请根据实际情况修改

## API_ADDR 表示后端api组件的地址
API_ADDR = "http://127.0.0.1:8080/api/v1" 

## 根据实际情况，修改PORTAL_DB_*, 默认用户名为root，默认密码为""
## 根据实际情况，修改ALARM_DB_*, 默认用户名为root，默认密码为""
```
* 启动dashboard

```
cd /usr/local/open-falcon/dashboard
bash control start
```

