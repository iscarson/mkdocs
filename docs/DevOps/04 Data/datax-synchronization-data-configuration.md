### 1. dataX 说明

DataX 是阿里巴巴集团内被广泛使用的离线数据同步工具/平台，实现包括 MySQL、Oracle、SqlServer、Postgre、HDFS、Hive、ADS、HBase、OTS、ODPS 等各种异构数据源之间高效的数据同步功能。

### 2. 下载
使用git
> git clone git@github.com:alibaba/DataX.git

使用wget

> wget http://datax-opensource.oss-cn-hangzhou.aliyuncs.com/datax.tar.gz

### 3. 安装

```
> git 源码编译
    参考:https://github.com/alibaba/DataX/wiki/compile-datax
    
> wget 安装

    参考  https://github.com/alibaba/DataX/wiki/Quick-Start

    下载后解压至本地某个目录，修改权限为755，进入bin目录，即可运行样例同步作业：
       $ tar zxvf datax.tar.gz
       $ sudo chmod -R 755 {YOUR_DATAX_HOME}
       $ cd  {YOUR_DATAX_HOME}/bin
       $ python datax.py ../job/job.json
```

### 4. 配置

>

### 5 job json 文件配置

nysql数据同步到odps(mysq2odsp.json)：

```
{
    "job": {
        "content": [
            {
                "reader": {
                    "name": "mysqlreader",
                    "parameter": {
                        "username": "username",
                        "password": "password",
                        "column": ["uid","date_time"],
                        "connection": [
                            {
                                "table": [
                                    "datax_user"
                                ],
                                "jdbcUrl": [
                                    "jdbc:mysql://127.0.0.1:3306/test"
                                ]
                            }
                        ]
                    }
                },
                "writer": {
                    "name": "odpswriter",
                    "parameter": {
                        "accessId": "accessId",
                        "accessKey": "accessKey",
                        "column": ["uid","date_time"],
                        "odpsServer": "http://service.odps.aliyun.com/api",
                        "partition": "",
                        "project": "odps_ project",
                        "table": "odps_ project",
                        "truncate": true
                    }
                }
            }
        ],
        "setting": {
            "speed": {
                "channel": 5
            }
        }
    }
}
```

odps同步到mysql(odsp2mysq.json)：


```
{
    "job": {
        "content": [
            {

	        "reader": {
                    "name": "odpsreader",
                    "parameter": {
                        "accessId": "accessId",
                        "accessKey": "accessKey",
                        "project": "project",
                        "table": "stat_user_tag",
                        "column": [
                            "user_id",
                            "province",
                            "city",
                            "county",
                            "birthday",
                            "baby_id",
                            "baby_birthday",
                            "model"
                        ],
                        "packageAuthorizedProject": "project",
                        "splitMode": "record",
                        "odpsServer": "http://service.odps.aliyun.com/api"
                    }
                },
                "writer": {
                    "name": "mysqlwriter",
                    "parameter": {
                        "writeMode": "insert",
                        "username": "username",
                        "password": "password",
                        "column": [
                            "user_id",
                            "province",
                            "city",
                            "county",
                            "birthday",
                            "baby_id",
                            "baby_birthday",
                            "model"
                        ],
                        "session": [
                            "set session sql_mode='ANSI'"
                        ],
                        "preSql": [
                            "truncate log_stat_user_tag"
                        ],
                        "connection": [
                            {
                                "jdbcUrl": "jdbc:mysql://172.16.200.17:3306/test?useUnicode=true&characterEncoding=utf8",
                                "table": [
                                    "log_stat_user_tag"
                                ]
                            }
                        ]
                    }
                    
                }
            }
        ],
        "setting": {
            "speed": {
                "channel": 5
            }
        }
    }
}
```

### 5. 执行测试

```
mysql同步到odps  python datax.py /job/mysql2odps.json

odps同步到mysql  python datax.py /job/odps2mysql.json
```