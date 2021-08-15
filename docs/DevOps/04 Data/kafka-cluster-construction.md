## 1. 服务器基本信息

| ip地址 | 安装服务  |
| --- | --- |
| 10.0.0.52 | zookeeper-3.4.10、kafka2.10、kafka-manager|
| 10.0.0.53 | zookeeper-3.4.10、kafka2.10 |
| 10.0.0.54 | zookeeper-3.4.10、kafka2.10  |

## 2. 环境信息

* JDK

jdk版本：jdk1.8.0_11

```
http://download.oracle.com/otn-pub/java/jdk/8u111-b14/jdk-8u111-linux-x64.tar.gz
```

* zookeeper

zookeeper版本：zookeeper-3.4.10

```
http://mirrors.cnnic.cn/apache/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz
```


## 3. 安装jdk(三台主机上执行）

```
cd /usr/loca/src/
tar -C /usr/local/ -xzf /usr/local/src/jdk-8u111-linux-x64.tar.gz
```

配置java环境变量

```
vim /etc/profile
```

添加如下信息

```
export JAVA_HOME=/usr/local/jdk1.8.0_111
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

刷新配置文件：

```
source /etc/profile
```


## 4. 安装zookeeper(三台主机上执行）

### 4.1 安装zookeeper

```
cd /usr/local/src/
tar -C /usr/local/ -xzf zookeeper-3.4.10.tar.gz
cd /usr/local/zookeeper-3.4.10
ln -s zookeeper-3.4.10 zookeeper
```

### 4.2 生成配置文件
```
cd /usr/local/zookeeper
cp conf/zoo_sample.cfg conf/zoo.cfg
```

### 4.3 修改zookeeper配置文件

```
vim /usr/local/zookeeper/conf/zoo.cfg
```

修改以下内容

```
maxClientCnxns=60
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/data/zookeeper/data
dataLogDir=/data/logs/zookeeper
clientPort=2181
server.1=10.0.0.52:2888:3888
server.2=10.0.0.53:2888:3888
server.3=10.0.0.54:2888:3888
```
> 2888表示zookeeper程序监听端口，3888表示zookeeper选举通信端口。

### 4.4 创建所需文件夹

```
mkdir -p /data/zookeeper/data
mkdir -p /data/logs/zookeeper
```

### 4.5 生成myid 
主机(10.0.0.52)

```
echo "1" >/data/zookeeper/data/myid  ##生成ID，这里需要注意， myid对应的zoo.cfg的server.ID，比如第二台zookeeper主机对应的myid应该是2
```

主机(10.0.0.53)

```
echo "2" >/data/zookeeper/data/myid
```

主机(10.0.0.54)

```
echo "3" >/data/zookeeper/data/myid
```

### 4.6 启动zookeeper

```
cd /usr/local/zookeeper/bin
./zkServer.sh start#
```

### 4.7 关闭zookeeper

```
cd /usr/local/zookeeper/bin
./zkServer.sh stot
```

### 4.8 查看zk状态

```
cd /usr/local/zookeeper/bin
./zkServer.sh status
```

### 4.9 查看相关信息

```
/usr/local/zookeeper/bin/zkCli.sh -server 127.0.0.1:2181
```

## 5. 安装kafka(三台主机上执行）

### 5.1 安装kafka

```
cd /usr/local/src
tar zxvf kafka_2.10-0.10.2.1.tgz
mv kafka_2.10-0.10.2.1 /usr/local/kafka
```

### 5.2 修改配置文件

```
vim /usr/local/kafka/config/server.properties
```

具体参数如下:

```
# 每台服务器的broker.id都不能相同
broker.id=1 

# 端口
port=19092

# 主机名
host.name=10.0.0.52

# 是否可以删除topic
delete.topic.enable=true

# 具体一些参数
log.retention.hours=168 
message.max.byte=5242880
default.replication.factor=2
replica.fetch.max.bytes=5242880

# 设置zookeeper集群地址与端口
zookeeper.connect=10.0.0.52:2181,10.0.0.53:2181,10.0.0.54:2181
```
### 5.3 启动kafka（三台）

```
cd /data/kafka/kafka_2.12-0.11.0.0/bin 
./kafka-server-start.sh -daemon ../config/server.properties &
```

### 5.4 创建topic

```
./kafka-topics.sh --create --zookeeper 10.0.0.52:2181,10.0.0.53:2181,10.0.0.54:2181 --replication-factor 2 --partitions 1 --topic tttt
```
参数解释

```
复制两份
--replication-factor 2
创建1个分区
--partitions 1
topic 名称
--topic tttt
```

### 5.5 查看已经存在的topic

```
./kafka-topics.sh --list --zookeeper 10.0.0.52:2181,10.0.0.53:2181,10.0.0.54:2181 
```