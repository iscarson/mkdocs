
### 1. 创建一个Django 项目

```
django-admin.py startproject <项目名称>
```

例：

```
django-admin.py startproject myproject
```

当前目录下会生成myproject的工程，目录结构如下：

```
myproject
|----manage.py
|____myproject
        |----__init__.py
        |----settings.py
        |----urls.py
        |----wsgi.py
        
```
> manage.py：Django项目里面的工具，通过它可以调用django shell和数据库等;
> settings.py：包含了项目的默认设置，包括数据库信息，调试标志以及其他一些工作的变量;
> urls.py：负责把URL模式映射到应用程序.


### 2. 在myproject项目中创建一个应用

```
python manage.py startapp <应用名称>
```

例：

```
python manage.py startapp myblog
```
当前目录下会生成myblog目录，目录结构如下：

```
myblog
|----__init__.py
|----admin.py
|----apps.py
|----migrations
|       |---__init__.py
|----models.py
|----tests.py
|----views.py
```

### 3. 启动Django项目

```
python manage.py runserver <web端口号>
```
例：

```
python manage.py runserver 8080
```
启动后web访问地址+端口就可以了 ~


### 4. 数据库操作

* 同步更改数据库表或字段

```
python manage.py syncdb
 
注意：Django 1.7.1 及以上的版本需要用以下命令
python manage.py makemigrations
python manage.py migrate
```
这种方法可以创建表，当你在models.py中新增了类时，运行它就可以自动在数据库中创建表了，不用手动创建

* 清空数据库

```
python manage.py flush
```
此命令会询问是 yes 还是 no, 选择 yes 会把数据全部清空掉，只留下空表。


5. 创建超级管理员

```
python manage.py createsuperuser
 
# 按照提示输入用户名和对应的密码就好了邮箱可以留空，用户名和密码必填
 
# 修改 用户密码可以用：
python manage.py changepassword username
```


### Django 基础命令汇总

```
# 1. 创建一个项目：django-admin.py startproject <项目名称>
django-admin.py startproject myproject

# 2. 创建一个应用：python manage.py startapp <应用名称>
python manage.py startapp myblog

# 3. 启动项目：python manage.py runserver <web端口号>
python manage.py runserver 8080

# 4. 同步更改数据库表或字段
python manage.py syncdb
 
注意：Django 1.7.1 及以上的版本需要用以下命令
python manage.py makemigrations
python manage.py migrate

# 5. 清空数据库
python manage.py flush

# 6. 创建超级管理员
python manage.py createsuperuser

# 7. 修改用户密码
python manage.py changepassword username
```


