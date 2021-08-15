## 一.基本信息

### 1. 服务器基本信息
|主机名 |ip地址 | 安装服务  |
| --- | --- | --- |
| spark-master| 172.16.200.81 | jdk、hadoop、spark、scala |
| spark-slave01| 172.16.200.82 | jdk、hadoop、spark  |
|spark-slave02 | 172.16.200.83 | jdk、hadoop、spark  |
|spark-slave03 | 172.16.200.84 | jdk、hadoop、spark  |

### 2. 软件基本信息
|软件名 |版本 | 安装路径  |
| --- | --- | --- |
|oracle jdk|1.8.0_111|/usr/local/jdk1.8.0_111|
|hadoop|2.7.1|/usr/local/hadoop-2.7.3|
|spark|2.0.2|/usr/local/spark-2.0.2|
|scala|2.12.1|usr/local/2.12.1|

### 3.环境变量汇总
```
############# java ############
export JAVA_HOME=/usr/local/jdk1.8.0_111
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar


########### hadoop ##########
export HADOOP_HOME=/usr/local/hadoop-2.7.3
export PATH=$JAVA_HOme/bin:$HADOOP_HOME/bin:$PATH
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin



######### spark ############
export SPARK_HOME=/usr/local/spark-2.0.2
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin

######### scala ##########
export SCALA_HOME=/usr/local/scala-2.12.1
export PATH=$PATH:$SCALA_HOME/bin
```
 
### 4. 基本环境配置（master、slave相同操作）
#### 4.1 配置jdk

```
cd /usr/loca/src/
tar -C /usr/local/ -xzf /usr/local/src/jdk-8u111-linux-x64.tar.gz
```

#### 4.2 配置java环境变量

```
vim /etc/profile
```

添加如下信息
```
######### jdk ############
export JAVA_HOME=/usr/local/jdk1.8.0_111
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

#### 4.3 刷新配置文件：

```
source /etc/profile
```

#### 4.4 配置hosts

```
vim /etc/hosts
172.16.200.81	spark-master
172.16.200.82	spark-slave1
172.16.200.83	spark-slave2
```


#### 4.5 配置免密码
生成密钥对

```
ssh-keygen
```
如果密钥不设置密码，则连按几下回车

先配置本机免密码登录

```
cd /root/.ssh
cat id_rsa.pub > authorized_keys
chmod 600 authorized_keys
```

再将其它主机id_rsa.pub 内容追加到 authorized_keys中，三台配置完成后即可实现免密码登录

## 二.大数据平台搭建
### 1. 搭建Hadoop（master、slave相同操作）
#### 1.1 安装hadoop

```
cd /usr/loca/src/
tar -C /usr/local/ -xzf hadoop-2.7.3.tar.gz
```

#### 1.2 配置hadoop环境变量

```
vim /etc/profile
```

添加如下信息

```
######### hadoop ############
export HADOOP_HOME=/usr/local/hadoop-2.7.3
export PATH=$JAVA_HOme/bin:$HADOOP_HOME/bin:$PATH
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
```

#### 1.3 刷新配置文件：

```
source /etc/profile
```

### 1.4 修改hadoop配置文件

```
cd /usr/local/hadoop-2.7.3/etc/hadoop
```

查看

```
root@spark-master hadoop]# ll
总用量 152
-rw-r--r--. 1 root root  4436 8月  18 09:49 capacity-scheduler.xml
-rw-r--r--. 1 root root  1335 8月  18 09:49 configuration.xsl
-rw-r--r--. 1 root root   318 8月  18 09:49 container-executor.cfg
-rw-r--r--. 1 root root  1037 12月 21 14:58 core-site.xml
-rw-r--r--. 1 root root  3589 8月  18 09:49 hadoop-env.cmd
-rw-r--r--. 1 root root  4235 12月 21 11:17 hadoop-env.sh
-rw-r--r--. 1 root root  2598 8月  18 09:49 hadoop-metrics2.properties
-rw-r--r--. 1 root root  2490 8月  18 09:49 hadoop-metrics.properties
-rw-r--r--. 1 root root  9683 8月  18 09:49 hadoop-policy.xml
-rw-r--r--. 1 root root  1826 12月 21 14:11 hdfs-site.xml
-rw-r--r--. 1 root root  1449 8月  18 09:49 httpfs-env.sh
-rw-r--r--. 1 root root  1657 8月  18 09:49 httpfs-log4j.properties
-rw-r--r--. 1 root root    21 8月  18 09:49 httpfs-signature.secret
-rw-r--r--. 1 root root   620 8月  18 09:49 httpfs-site.xml
-rw-r--r--. 1 root root  3518 8月  18 09:49 kms-acls.xml
-rw-r--r--. 1 root root  1527 8月  18 09:49 kms-env.sh
-rw-r--r--. 1 root root  1631 8月  18 09:49 kms-log4j.properties
-rw-r--r--. 1 root root  5511 8月  18 09:49 kms-site.xml
-rw-r--r--. 1 root root 11237 8月  18 09:49 log4j.properties
-rw-r--r--. 1 root root   931 8月  18 09:49 mapred-env.cmd
-rw-r--r--. 1 root root  1383 8月  18 09:49 mapred-env.sh
-rw-r--r--. 1 root root  4113 8月  18 09:49 mapred-queues.xml.template
-rw-r--r--. 1 root root  1612 12月 21 12:03 mapred-site.xml
-rw-r--r--. 1 root root    56 12月 21 16:30 slaves
-rw-r--r--. 1 root root  2316 8月  18 09:49 ssl-client.xml.example
-rw-r--r--. 1 root root  2268 8月  18 09:49 ssl-server.xml.example
-rw-r--r--. 1 root root  2191 8月  18 09:49 yarn-env.cmd
-rw-r--r--. 1 root root  4564 12月 21 11:19 yarn-env.sh
-rw-r--r--. 1 root root  1195 12月 21 14:24 yarn-site.xml
```

#### 1.4.1 修改hadoop全局配置文件

```
vim core-site.xml
```

```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<!-- Put site-specific property overrides in this file. -->

<configuration>
<!--配置namenode的地址-->

  <property>
	<name>fs.defaultFS</name>
	<value>hdfs://172.16.200.81:9000</value>
  </property>
<!-- 指定hadoop运行时产生文件的存储目录 -->
  <property>
	<name>hadoop.tmp.dir</name>
	<value>file:///data/hadoop/data/tmp</value>
 </property>        
</configuration>
```
#### 1.4.2 配置hadoop关联jdk
`vim hadoop-env.sh`

```
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Set Hadoop-specific environment variables here.

# The only required environment variable is JAVA_HOME.  All others are
# optional.  When running a distributed configuration it is best to
# set JAVA_HOME in this file, so that it is correctly defined on
# remote nodes.

# The java implementation to use.
#配置jdk的环境
export JAVA_HOME=/usr/local/jdk1.8.0_111

# The jsvc implementation to use. Jsvc is required to run secure datanodes
# that bind to privileged ports to provide authentication of data transfer
# protocol.  Jsvc is not required if SASL is configured for authentication of
# data transfer protocol using non-privileged ports.
#export JSVC_HOME=${JSVC_HOME}

export HADOOP_CONF_DIR=${HADOOP_CONF_DIR:-"/etc/hadoop"}

# Extra Java CLASSPATH elements.  Automatically insert capacity-scheduler.
for f in $HADOOP_HOME/contrib/capacity-scheduler/*.jar; do
  if [ "$HADOOP_CLASSPATH" ]; then
    export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:$f
  else
    export HADOOP_CLASSPATH=$f
  fi
done

# The maximum amount of heap to use, in MB. Default is 1000.
#export HADOOP_HEAPSIZE=
#export HADOOP_NAMENODE_INIT_HEAPSIZE=""

# Extra Java runtime options.  Empty by default.
export HADOOP_OPTS="$HADOOP_OPTS -Djava.net.preferIPv4Stack=true"

# Command specific options appended to HADOOP_OPTS when specified
export HADOOP_NAMENODE_OPTS="-Dhadoop.security.logger=${HADOOP_SECURITY_LOGGER:-INFO,RFAS} -Dhdfs.audit.logger=${HDFS_AUDIT_LOGGER:-INFO,NullAppender} $HADOOP_NAMENODE_OPTS"
export HADOOP_DATANODE_OPTS="-Dhadoop.security.logger=ERROR,RFAS $HADOOP_DATANODE_OPTS"

export HADOOP_SECONDARYNAMENODE_OPTS="-Dhadoop.security.logger=${HADOOP_SECURITY_LOGGER:-INFO,RFAS} -Dhdfs.audit.logger=${HDFS_AUDIT_LOGGER:-INFO,NullAppender} $HADOOP_SECONDARYNAMENODE_OPTS"

export HADOOP_NFS3_OPTS="$HADOOP_NFS3_OPTS"
export HADOOP_PORTMAP_OPTS="-Xmx512m $HADOOP_PORTMAP_OPTS"

# The following applies to multiple commands (fs, dfs, fsck, distcp etc)
export HADOOP_CLIENT_OPTS="-Xmx512m $HADOOP_CLIENT_OPTS"
#HADOOP_JAVA_PLATFORM_OPTS="-XX:-UsePerfData $HADOOP_JAVA_PLATFORM_OPTS"

# On secure datanodes, user to run the datanode as after dropping privileges.
# This **MUST** be uncommented to enable secure HDFS if using privileged ports
# to provide authentication of data transfer protocol.  This **MUST NOT** be
# defined if SASL is configured for authentication of data transfer protocol
# using non-privileged ports.
export HADOOP_SECURE_DN_USER=${HADOOP_SECURE_DN_USER}

# Where log files are stored.  $HADOOP_HOME/logs by default.
#export HADOOP_LOG_DIR=${HADOOP_LOG_DIR}/$USER

# Where log files are stored in the secure data environment.
export HADOOP_SECURE_DN_LOG_DIR=${HADOOP_LOG_DIR}/${HADOOP_HDFS_USER}

###
# HDFS Mover specific parameters
###
# Specify the JVM options to be used when starting the HDFS Mover.
# These options will be appended to the options specified as HADOOP_OPTS
# and therefore may override any similar flags set in HADOOP_OPTS
#
# export HADOOP_MOVER_OPTS=""

###
# Advanced Users Only!
###

# The directory where pid files are stored. /tmp by default.
# NOTE: this should be set to a directory that can only be written to by 
#       the user that will run the hadoop daemons.  Otherwise there is the
#       potential for a symlink attack.
export HADOOP_PID_DIR=${HADOOP_PID_DIR}
export HADOOP_SECURE_DN_PID_DIR=${HADOOP_PID_DIR}

# A string representing this instance of hadoop. $USER by default.
export HADOOP_IDENT_STRING=$USER

```
#### 1.4.3 配置hdfs
`vim hdfs-site.xml`
```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
<!--指定hdfs的副本数-->
  <property>
        <name>dfs.replication</name>
        <value>3</value>
  </property>
<!--设置hdfs的权限-->  
  <property>
      	 <name>dfs.permissions</name>
         <value>false</value>
  </property>
<!-- secondary name node web 监听端口 -->		
  <property>
         <name>dfs.namenode.secondary.http-address</name>
         <value>172.16.200.81:50090</value>
  </property>
 <!-- name node web 监听端口 -->
		
  <property>
  	<name>dfs.namenode.http-address</name>
  	<value>172.16.200.81:50070</value>
  </property>
<!-- 真正的datanode数据保存路径 -->
  <property>
  	<name>dfs.datanode.data.dir</name>
  	<value>file:///data/hadoop/data/dfs/dn</value>
  </property>
<!-- NN所使用的元数据保存-->
  <property>
  	<name>dfs.namenode.name.dir</name>
  	<value>file:///data/hadoop/data/dfs/nn/name</value>
  </property>
<!--存放 edit 文件-->
  <property>
  	<name>dfs.namenode.edits.dir</name>
  	<value>file:///data/hadoop/data/dfs/nn/edits</value>
  </property>
<!-- secondary namenode 节点存储 checkpoint 文件目录-->
  <property>
  	<name>dfs.namenode.checkpoint.dir</name>
  	<value>file:///data/hadoop/data/dfs/snn/name</value>
  </property>
<!-- secondary namenode 节点存储 edits 文件目录-->
  <property>
  	<name>dfs.namenode.checkpoint.edits.dir</name>
  	<value>file:///data/hadoop/data/dfs/snn/edits</value>
  </property>

</configuration>
```

#### 1.4.4 配置mapred
`vim mapred-site.xml`

```
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
<!-- 指定mr运行在yarn上 -->
  <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
  </property>
<!--历史服务的web端口地址  -->
  <property>
  	<name>mapreduce.jobhistory.webapp.address</name>
  	<value>172.16.200.81:19888</value>
  </property>
<!--历史服务的端口地址-->
  <property>
 	<name>mapreduce.jobhistory.address</name>
  	<value>172.16.200.81:10020</value>
  </property>
<!--Uber运行模式-->
  <property>
  	<name>mapreduce.job.ubertask.enable</name>
  	<value>false</value>
  </property>
<!--MapReduce作业产生的日志存放位置。-->
  <property>
	<name>mapreduce.jobhistory.intermediate-done-dir</name>
	<value>${yarn.app.mapreduce.am.staging-dir}/history/done_intermediate</value>
  </property>
<!--MR JobHistory Server管理的日志的存放位置-->
  <property>
	<name>mapreduce.jobhistory.done-dir</name>
	<value>${yarn.app.mapreduce.am.staging-dir}/history/done</value>
  </property>
<!--是job运行时的临时文件夹-->
  <property>
	<name>yarn.app.mapreduce.am.staging-dir</name>
	<value>/data/hadoop/hadoop-yarn/staging</value>
  </property>
</configuration>
```
#### 1.4.5 配置slaves

```
vim slaves
```

```
172.16.200.81
172.16.200.82
172.16.200.83
172.16.200.84
```
#### 1.4.6 配置yarn

```
vim yarn-site.xml
```

```
<?xml version="1.0"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->
<configuration>
<!-- 指定nodeManager组件在哪个机子上跑 -->
  <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
  </property>
<!-- 指定resourcemanager组件在哪个机子上跑 -->
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>172.16.200.81</value>
  </property> 
 <!--resourcemanager web地址-->
  <property>
    <name>yarn.resourcemanager.webapp.address</name>
    <value>172.16.200.81:8088</value>
  </property>
 <!--启用日志聚集功能-->
  <property>
    <name>yarn.log-aggregation-enable</name>
    <value>true</value>
  </property>
<!--在HDFS上聚集的日志最多保存多长时间-->
  <property>
    <name>yarn.log-aggregation.retain-seconds</name>
    <value>86400</value>
  </property> 



</configuration>

```

### 2. 搭建Spark（master、slave相同操作）
### 2.1  安装spark

```
cd /usr/loca/src/
tar zxvf spark-2.0.2-bin-hadoop2.7.tgz
mv spark-2.0.2-bin-hadoop2.7  /usr/local/spark-2.0.2
```

### 2.2 配置spark环境变量

```
vim /etc/profile
```

添加如下信息

```
######### spark ############
export SPARK_HOME=/usr/local/spark-2.0.2
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
```

#### 2.3 刷新配置文件：

```
source /etc/profile
```

### 2.4 修改spark配置文件

```
cd /usr/local/spark-2.0.2/conf
mv spark-env.sh.template spark-env.sh
```

```
[root@spark-master conf]# ll
总用量 36
-rw-r--r--. 1  500  500  987 11月  8 09:58 docker.properties.template
-rw-r--r--. 1  500  500 1105 11月  8 09:58 fairscheduler.xml.template
-rw-r--r--. 1  500  500 2025 11月  8 09:58 log4j.properties.template
-rw-r--r--. 1  500  500 7239 11月  8 09:58 metrics.properties.template
-rw-r--r--. 1  500  500  912 12月 21 16:55 slaves
-rw-r--r--. 1  500  500 1292 11月  8 09:58 spark-defaults.conf.template
-rwxr-xr-x. 1 root root 3969 12月 21 15:50 spark-env.sh
-rwxr-xr-x. 1  500  500 3861 11月  8 09:58 spark-env.sh.template
```

#### 2.4.1 spark关联jdk

```
vim spark-env.sh
```

```
#!/usr/bin/env bash

#
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

# This file is sourced when running various Spark programs.
# Copy it as spark-env.sh and edit that to configure Spark for your site.

# Options read when launching programs locally with
# ./bin/run-example or ./bin/spark-submit
# - HADOOP_CONF_DIR, to point Spark towards Hadoop configuration files
# - SPARK_LOCAL_IP, to set the IP address Spark binds to on this node
# - SPARK_PUBLIC_DNS, to set the public dns name of the driver program
# - SPARK_CLASSPATH, default classpath entries to append

# Options read by executors and drivers running inside the cluster
# - SPARK_LOCAL_IP, to set the IP address Spark binds to on this node
# - SPARK_PUBLIC_DNS, to set the public DNS name of the driver program
# - SPARK_CLASSPATH, default classpath entries to append
# - SPARK_LOCAL_DIRS, storage directories to use on this node for shuffle and RDD data
# - MESOS_NATIVE_JAVA_LIBRARY, to point to your libmesos.so if you use Mesos

# Options read in YARN client mode
# - HADOOP_CONF_DIR, to point Spark towards Hadoop configuration files
# - SPARK_EXECUTOR_INSTANCES, Number of executors to start (Default: 2)
# - SPARK_EXECUTOR_CORES, Number of cores for the executors (Default: 1).
# - SPARK_EXECUTOR_MEMORY, Memory per Executor (e.g. 1000M, 2G) (Default: 1G)
# - SPARK_DRIVER_MEMORY, Memory for Driver (e.g. 1000M, 2G) (Default: 1G)

# Options for the daemons used in the standalone deploy mode
# - SPARK_MASTER_HOST, to bind the master to a different IP address or hostname
# - SPARK_MASTER_PORT / SPARK_MASTER_WEBUI_PORT, to use non-default ports for the master
# - SPARK_MASTER_OPTS, to set config properties only for the master (e.g. "-Dx=y")
# - SPARK_WORKER_CORES, to set the number of cores to use on this machine
# - SPARK_WORKER_MEMORY, to set how much total memory workers have to give executors (e.g. 1000m, 2g)
# - SPARK_WORKER_PORT / SPARK_WORKER_WEBUI_PORT, to use non-default ports for the worker
# - SPARK_WORKER_INSTANCES, to set the number of worker processes per node
# - SPARK_WORKER_DIR, to set the working directory of worker processes
# - SPARK_WORKER_OPTS, to set config properties only for the worker (e.g. "-Dx=y")
# - SPARK_DAEMON_MEMORY, to allocate to the master, worker and history server themselves (default: 1g).
# - SPARK_HISTORY_OPTS, to set config properties only for the history server (e.g. "-Dx=y")
# - SPARK_SHUFFLE_OPTS, to set config properties only for the external shuffle service (e.g. "-Dx=y")
# - SPARK_DAEMON_JAVA_OPTS, to set config properties for all daemons (e.g. "-Dx=y")
# - SPARK_PUBLIC_DNS, to set the public dns name of the master or workers

# Generic options for the daemons used in the standalone deploy mode
# - SPARK_CONF_DIR      Alternate conf dir. (Default: ${SPARK_HOME}/conf)
# - SPARK_LOG_DIR       Where log files are stored.  (Default: ${SPARK_HOME}/logs)
# - SPARK_PID_DIR       Where the pid file is stored. (Default: /tmp)
# - SPARK_IDENT_STRING  A string representing this instance of spark. (Default: $USER)
# - SPARK_NICENESS      The scheduling priority for daemons. (Default: 0)
#java的环境变量
export JAVA_HOME=/usr/local/jdk1.8.0_111
#spark主节点的ip
export SPARK_MASTER_IP=172.16.200.81
#spark主节点的端口号
export SPARK_MASTER_PORT=7077
```
#### 2.4.2 配置slaves

```
vim slaves
```

```
#
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

# A Spark Worker will be started on each of the machines listed below.
172.16.200.81
172.16.200.82
172.16.200.83
172.16.200.84
```
### 3. 安装scala

```
cd /usr/loca/src/
tar zxvf scala-2.12.1.tgz
mv scala-2.12.1  /usr/local
```

### 3.1 配置scala环境变量（只master安装）

```
vim /etc/profile
```

添加如下信息

```
######### scala ##########
export SCALA_HOME=/usr/local/scala-2.12.1
export PATH=$PATH:$SCALA_HOME/bin
```

#### 3.2 刷新配置文件：

```
source /etc/profile
```


### 4. 启动程序

#### 4.1 启动hadoop

#### 4.1.1 格式化namenode

```
hadoop namenode -format
```

#### 4.1.2 master启动hadoop

```
cd /usr/local/hadoop-2.7.3/sbin
./start-all.sh
```

提示

```
start-all.sh                    //启动master和slaves
stop-all.sh                    //停止master和slaves
```

查看进程 （master）

```
[root@spark-master sbin]# jps
8961 NodeManager
8327 DataNode
8503 SecondaryNameNode
8187 NameNode
8670 ResourceManager
9102 Jps
[root@spark-master sbin]#
```

查看进程 （slave）

```
[root@spark-slave01 ~]# jps
4289 NodeManager
4439 Jps
4175 DataNode
[root@spark-slave01 ~]#
```

slave01、slve02、slave03显示相同


#### 4.2 启动spark

#### 4.1.2 master启动hadoop

```
cd /usr/local/spark-2.0.2/sbin
./start-all.sh
```

提示

```
start-all.sh                    //启动master和slaves
stop-all.sh                    //停止master和slaves
```