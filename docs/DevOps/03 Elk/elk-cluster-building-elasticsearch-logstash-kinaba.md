## 1. Logstash

### 1.1 安装

**注：**安装在需要收集日志的机器上。

```
cd /data/softs
sudo wget https://download.elastic.co/logstash/logstash/logstash-2.4.0.tar.gz
sudo tar -zxf logstash-2.4.0.tar.gz
sudo mv logstash-2.4.0 /usr/local/logstash
```

### 1.2 创建配置

```
cd /usr/local/logstash
sudo vim logstash.conf
```

输入：

```
input {
    file {
        path => ["/data/logs/error/program.error.log"]
        type => "error"
        tags => ["error"]
        start_position => "beginning"
        #sincedb_path => "/dev/null"
        codec => "json"
    }
    file {
        path => ["/data/logs/error/program.warning.log"]
        type => "warning"
        tags => ["warning"]
        start_position => "beginning"
        #sincedb_path => "/dev/null"
        codec => "json"
    }
    #file {
    #    path => ["/data/logs/access/nginx.access.log"]
    #    type => "access"
    #    tags => ["access"]
    #    start_position => "beginning"
    #    codec => "json"
    #}
}
output {
    if "error" in [tags] {
        elasticsearch {
            hosts  => "10.0.0.23:9200"
            index  => "error_log"
        }
        stdout { codec=> rubydebug }
    }
    if "warning" in [tags] {
        elasticsearch {
            hosts  => "10.0.0.23:9200"
            index  => "warning_log"
        }
        stdout { codec=> rubydebug }
    }
    if "access" in [tags] {
        elasticsearch {
            hosts  => "10.0.0.23:9200"
            #index  => "access_log"
            index => "access_log_%{+YYYY.MM.dd}"
        }
        stdout { }
    }
}
```

### 1.3 启动

```
sudo /usr/local/logstash/bin/logstash agent -f /usr/local/logstash/logstash.conf 2>>/data/logs/error/logstash.error.log &
```

## 2. ElasticSearch集群（三台）

### 2.1 安装
```
	# 安装JDK
	sudo yum -y install java-1.8.0-openjdk

	# 下载ES RPM包
	sudo wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-2.4.0.rpm
	# 安装
	rpm -ivh elasticsearch-5.2.0.rpm
	
	# 开机启动
	sudo /bin/systemctl daemon-reload
	sudo /bin/systemctl enable elasticsearch.service
```

### 2.2 配置
#### 2.2.1 elasticsearch01
```

	# 更改配置 
	sudo vim /etc/elasticsearch/elasticsearch.yml
	
	path.data: /data/components/elasticsearch
	path.plugins: /data/components/elasticsearch/plugins

    node.name: zt-elk01
    path.logs: /data/logs/
    network.host: 10.0.0.23
    http.port: 9200
    discovery.zen.ping.unicast.hosts: ["10.0.0.24","10.0.0.25"]
	 
	# 重启
	sudo systemctl enable elasticsearch.service
	sudo systemctl restart elasticsearch.service
```
#### 2.2.2 elasticsearch02
```
# 更改配置 
	sudo vim /etc/elasticsearch/elasticsearch.yml
	
	path.data: /data/components/elasticsearch
	path.plugins: /data/components/elasticsearch/plugins
	
    cluster.name: zt-elk
    node.name: zt-elk02
    path.logs: /data/logs/
    network.host: 10.0.0.24
    http.port: 9200
    discovery.zen.ping.unicast.hosts: ["10.0.0.23","10.0.0.25"]

	# 重启
	sudo systemctl enable elasticsearch.service
	sudo systemctl restart elasticsearch.service
```
### 2.2.3 elasticsearch03
```
# 更改配置 
	sudo vim /etc/elasticsearch/elasticsearch.yml
	
	path.data: /data/components/elasticsearch
	path.plugins: /data/components/elasticsearch/plugins
	
    cluster.name: zt-elk
    node.name: zt-elk03
    path.logs: /data/logs/
    network.host: 10.0.0.25
    http.port: 9200
    discovery.zen.ping.unicast.hosts: ["10.0.0.23","10.0.0.24"]

	# 重启
	sudo systemctl enable elasticsearch.service
	sudo systemctl restart elasticsearch.service
```

## 3. 安装Kibana

### 3.1 安装

**注：**安装在能对外访问的机器上。

```
cd /data/softs
sudo wget https://download.elastic.co/kibana/kibana/kibana-4.6.0-linux-x86_64.tar.gz
sudo tar -zxf kibana-4.6.0-linux-x86_64.tar.gz
sudo mv kibana-4.6.0-linux-x86_64 /usr/local/kibana
```

### 3.2 配置

更改相关配置：

```
cd /usr/local/kibana
vim config/kibana.yml

	server.port: 5601 
	server.host: "127.0.0.1"
	elasticsearch.url: "http://10.0.0.23:9200"
	
```

### 3.3 启动

```
sudo /usr/local/kibana/bin/kibana
```

## 4. tips

### 4.1 删除索引

```
curl -XDELETE 'http://127.0.0.1:9200/applog'
```