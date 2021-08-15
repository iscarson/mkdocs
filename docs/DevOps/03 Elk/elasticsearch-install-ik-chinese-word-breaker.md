## 一. 编译IK分词器


### 1. maven环境配置

* 下载maven

```
wget http://mirrors.cnnic.cn/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
```

* 解压

```
tar xvf apache-maven-3.3.9-bin.tar.gz
mv maven to /usr/local/apache-maven

mv apache-maven-3.3.9  /usr/local/apache-maven
```

* 配置环境变量

```
vim  ~/.bashrc

# 添加如下内容
export M2_HOME=/usr/local/apache-maven
export M2=$M2_HOME/bin 
export PATH=$M2:$PATH
```

* 加载环境变量

```
source ~/.bashrc

```

* 查看版本

```
mvn -version
```

### 2. IK 版本选择


* IK 与 ElasticSearch对应版本

IK version | ES version
-----------|-----------
master | 5.x -> master
5.6.1| 5.6.1
5.5.3| 5.5.3
5.4.3| 5.4.3
5.3.3| 5.3.3
5.2.2| 5.2.2
5.1.2| 5.1.2
1.10.1 | 2.4.1
1.9.5 | 2.3.5
1.8.1 | 2.2.1
1.7.0 | 2.1.1
1.5.0 | 2.0.0
1.2.6 | 1.0.0
1.2.5 | 0.90.x
1.1.3 | 0.20.x
1.0.0 | 0.16.2 -> 0.19.0


目前使用的elasticsearch为2.4 版本，根据以上对应表得出，应该选择1.10.1版本IK

* 1.10.1 版本IK GitHub地址

```
https://github.com/medcl/elasticsearch-analysis-ik/tree/v1.10.1
```

## 二. 安装IK

下载IK源码上传到服务器，进入到该目录下

### 1. 打包IK

```
mvn package
```

打包需要向maven库下载一些jar包，需要一些时间，静等 ~


编译完成后，可以在target/releases目录下找到对应的zip包

```
cd target/releases
```
可以看到编译后的IK包，直接使用即可

```
elasticsearch-analysis-ik-1.10.1.zip
```

### 2. 配置IK

将IK 解压后，放到 `/data/components/elasticsearch/plugins/ik`目录下

```
vim plugin-descriptor.properties

# 按照实际情况修改以下参数
elasticsearch.version=2.4.1
java.version=1.7
```

## 三. 配置elasticsearch

### 1. 修改elasticsearch配置

```
vim /etc/elasticsearch/elasticsearch.yml

# 添加以下内容
index.analysis.analyzer.default.type: ik
```

### 2. 重启elasticsearch

```
systemctl restart elasticsearch.service
```

配置完成，测试下 ~

## 四. 测试IK分词器

ik 带有两个分词器
**ik_max_word ：**会将文本做最细粒度的拆分；尽可能多的拆分出词语
**ik_smart：**会做最粗粒度的拆分；已被分出的词语将不会再次被其它词语占有


### 1. ik_max_word 测试

```
curl -XGET 'http://172.16.200.101:9200/_analyze?pretty&analyzer=ik_max_word' -d '掌通家园是全球最大的幼教管理平台'

# 返回信息如下

{
  "tokens" : [ {
    "token" : "掌",
    "start_offset" : 0,
    "end_offset" : 1,
    "type" : "CN_WORD",
    "position" : 0
  }, {
    "token" : "通",
    "start_offset" : 1,
    "end_offset" : 2,
    "type" : "CN_CHAR",
    "position" : 1
  }, {
    "token" : "家园",
    "start_offset" : 2,
    "end_offset" : 4,
    "type" : "CN_WORD",
    "position" : 2
  }, {
    "token" : "家",
    "start_offset" : 2,
    "end_offset" : 3,
    "type" : "CN_WORD",
    "position" : 3
  }, {
    "token" : "全球",
    "start_offset" : 5,
    "end_offset" : 7,
    "type" : "CN_WORD",
    "position" : 4
  }, {
    "token" : "最大",
    "start_offset" : 7,
    "end_offset" : 9,
    "type" : "CN_WORD",
    "position" : 5
  }, {
    "token" : "幼教",
    "start_offset" : 10,
    "end_offset" : 12,
    "type" : "CN_WORD",
    "position" : 6
  }, {
    "token" : "教管",
    "start_offset" : 11,
    "end_offset" : 13,
    "type" : "CN_WORD",
    "position" : 7
  }, {
    "token" : "管理",
    "start_offset" : 12,
    "end_offset" : 14,
    "type" : "CN_WORD",
    "position" : 8
  }, {
    "token" : "平台",
    "start_offset" : 14,
    "end_offset" : 16,
    "type" : "CN_WORD",
    "position" : 9
  }, {
    "token" : "台",
    "start_offset" : 15,
    "end_offset" : 16,
    "type" : "CN_WORD",
    "position" : 10
  } ]
}
```


### 2. ik_smart 测试

```
curl -XGET 'http://172.16.200.101:9200/_analyze?pretty&analyzer=ik_smart' -d '掌通家园是全球最大的幼教管理平台'

# 返回信息如下
{
  "tokens" : [ {
    "token" : "掌",
    "start_offset" : 0,
    "end_offset" : 1,
    "type" : "CN_WORD",
    "position" : 0
  }, {
    "token" : "通",
    "start_offset" : 1,
    "end_offset" : 2,
    "type" : "CN_CHAR",
    "position" : 1
  }, {
    "token" : "家园",
    "start_offset" : 2,
    "end_offset" : 4,
    "type" : "CN_WORD",
    "position" : 2
  }, {
    "token" : "全球",
    "start_offset" : 5,
    "end_offset" : 7,
    "type" : "CN_WORD",
    "position" : 3
  }, {
    "token" : "最大",
    "start_offset" : 7,
    "end_offset" : 9,
    "type" : "CN_WORD",
    "position" : 4
  }, {
    "token" : "幼教",
    "start_offset" : 10,
    "end_offset" : 12,
    "type" : "CN_WORD",
    "position" : 5
  }, {
    "token" : "管理",
    "start_offset" : 12,
    "end_offset" : 14,
    "type" : "CN_WORD",
    "position" : 6
  }, {
    "token" : "平台",
    "start_offset" : 14,
    "end_offset" : 16,
    "type" : "CN_WORD",
    "position" : 7
  } ]
}
```


