

## 1. 什么是 PyMySQL？
PyMySQL 是在 Python3.x 版本中用于连接 MySQL 服务器的一个库，Python2中则使用mysqldb。

PyMySQL 遵循 Python 数据库 API v2.0 规范，并包含了 pure-Python MySQL 客户端库。

 
## 2. PyMySQL 安装

```
pip3 install PyMySQL
```

### 3. 使用操作

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
import pymysql
  
# 创建连接
conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='123', db='t1')
# 创建游标
cursor = conn.cursor()
  
# 执行SQL，并返回收影响行数
effect_row = cursor.execute("update hosts set host = '1.1.1.2'")
  
# 执行SQL，并返回受影响行数
#effect_row = cursor.execute("update hosts set host = '1.1.1.2' where nid > %s", (1,))
  
# 执行SQL，并返回受影响行数
#effect_row = cursor.executemany("insert into hosts(host,color_id)values(%s,%s)", [("1.1.1.11",1),("1.1.1.11",2)])
  
  
# 提交，不然无法保存新建或者修改的数据
conn.commit()
  
# 关闭游标
cursor.close()
# 关闭连接
conn.close()
```
 

**注：**当使用字符串格式化时容易引起sql注入

```
sql = 'select * from userinfo where username="%s" and password="%s" ' %(user,pwd,)      #不推荐使用
```
```
sql = 'select * from userinfo where username=%s and password=%s',[user,pwd]    #推荐使用，mysql会自动格式化，避免SQL注入
```
 

```
import pymysql

user = input('请输入用户名:')
pwd = input('请输入密码:')

# 获取数据
conn = pymysql.Connect(host='192.168.12.89',port=3306,user='root',password="123",database="s17day11db",charset='utf8')
cursor = conn.cursor()

v = cursor.execute('select * from userinfo where username=%s and password=%s',[user,pwd])
result = cursor.fetchone()
cursor.close()
conn.close()

print(result)
```
 

 

* 获取新创建数据自增ID

```
new_class_id = cursor.lastrowid # 获取新增数据自增ID
复制代码
import pymysql

# 获取数据
conn = pymysql.Connect(host='192.168.12.89',port=3306,user='root',password="123",database="s17day11db",charset='utf8')
cursor = conn.cursor()

cursor.execute('insert into class(caption) values(%s)',['新班级'])
conn.commit()
new_class_id = cursor.lastrowid # 获取新增数据自增ID

cursor.execute('insert into student(sname,gender,class_id) values(%s,%s,%s)',['李杰','男',new_class_id])
conn.commit()

cursor.close()
conn.close()
```
 

* 查询语句

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
import pymysql
  
conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='123', db='t1')
cursor = conn.cursor()
cursor.execute("select * from hosts")
  
# 获取第一行数据
row_1 = cursor.fetchone()
  
# 获取前n行数据
# row_2 = cursor.fetchmany(3)
# 获取所有数据
# row_3 = cursor.fetchall()
  
conn.commit()
cursor.close()
conn.close()
```

**注：**
在fetch数据时按照顺序进行，可以使用cursor.scroll(num,mode)来移动游标位置，如：


* cursor.scroll(1,mode='relative')  # 相对当前位置移动
* cursor.scroll(2,mode='absolute') # 相对绝对位置移动
 

* 更改fetch数据类型
默认获取的数据是元祖类型，如果想要或者字典类型的数据

cursor = conn.cursor(cursor=pymysql.cursors.DictCursor)

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
import pymysql
  
conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='123', db='t1')
  
# 游标设置为字典类型
cursor = conn.cursor(cursor=pymysql.cursors.DictCursor)
v = cursor.execute('SELECT * FROM score')
result = cursor.fetchall()
# result = cursor.fetchone()
# result = cursor.fetchmany()
print(result)



cursor.close()
conn.close()
```


