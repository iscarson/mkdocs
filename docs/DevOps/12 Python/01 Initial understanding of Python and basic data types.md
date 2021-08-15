## 1. python介绍
* 发展史等等....
* 减少开发成本


## 2. python与其他语言对比
 * C语言：代码-->机器码
 * 其他高级语言：代码-->字节码-->机器码

## 3. python种类
cpython:常用的python就是cpython，python代码-->字节码-->机器码（一行一行读取执行）
其他python：python代码-->字节码-->机器码
pypy:python代码-->字节码-->机器码（全部转换完再执行）pypy效率会比cpython要高，pypy是cpython的一个分支，关于pypy可参考知乎一篇文章：[PyPy 为什么会比 CPython 还要快？](https://www.zhihu.com/question/19588346)

## 4. python基础
### 4.1  解释器：
* 解释规则
windows：
python解释器+文件：c:\python3.5\python.exe d:\s17\day01\helloworld.py
python解释器内执行：c:\python3.5\python.exe

linux or mac：
python解释器+文件：/usr/bin/python /data/s17/day01/helloworld.py
python解释器内执行：/usr/bin/python

**注：** 在linux or mac系统上默认安装的是python2.x版本，如在linux or mac系统使用python，需注意下python版本。

* 潜规则
.py 结尾（当然你也可以采用其他的结尾也是可以的，不怕team成员杀了你也可以写。其实主要还是为了规范，python服务器上用的比较多，看见.py也就会知道这是python程序）
* 编码
编码发展：ASCII-->Unicode(万国码)-->UTF-8 

编码种类与区别

| 编码 | 支持语言 | 字节位数 | 缺点 | 备注 |
| --- | --- | --- | --- | --- | --- |
| ASCII | 英语 | 8位 | 只支持英文 | * |
| 万国码 | 所有 | 至少16位 | 字节位数较大 | * |
| UTF-8 | 所有 | 8+ | * | 对万国码压缩 |
| GBK | 中文、英文 | 16位 | 支持语言少 | * |

python编码相关：

* 文件编码

    文件编码创建文件时定义即可，或者在IDE中定义
   
* 解释器编码
    解释器编码需要在代码中标注，python3.x 版本不标注也是可以的(解释器默认编码为ASCII)，但为了统一规范，在文件第二行标注  `# -*- coding:utf-8 -*-`
    
## 5. IDE
PyCharm

* 使用

    a.创建一个项目，指定代码路径与python解释器路径
    b.创建一个文件夹
    c.创建一个python script 文件
    d.在py文件中右键， run xx.py
    
* 文件编码
在pycharm中首先要设置文件编码，将文件默认文件编码设置为utf-8
* 文件模板
修改python scripts文件模板，将python环境信息与编码信息定义在模板中
* 改变大小
配置编辑器中文字大小，设置可以用鼠标滑轮控制文字大小


## 6. 注释
* 单行注释: `#`
* 多行注释:   \`\`\` \`\`\`  

## 7. .pyc文件
当一个python文件导入另一个模块时候，会生成一个 .pyc文件，那么这个文件就是导入的那个文件的字节码。
## 8. 变量
python变量规则：

* 字母
* 数字（变量不能以数字开头）
* 下划线
* 不能以python内置关键字为变量
* python中变量建议使用下划线分割（驼峰式也不会报错）

## 9. 输入、输出
* 输入: `a = input('请输入xxxx')`
* 输出: `print (a)`

## 10. 条件语句
* 例1：

```
if 条件:
    ...
else:
    ...
```

* 应用

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# Author:Chuncheng.Fan <xmzncc@gmail.com>

username = input("请输入用户名：")
password = input("请输入密码：")


if username  == 'fcc' and password == '123':
    print("欢迎登陆！")
else:
    print("用户名或密码错误！")

```

* 例2:

```
if 条件：
    ...
elif 条件:
    ...
else:
    ...   
```
    
* 应用：

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# Author:Chuncheng.Fan <xmzncc@gmail.com>

sex = input("请输入你的性别：")

if sex == "男":
    print("傻x，自己性别都忘了!")
elif sex == "女":
    print("...你在想想，你忘了你已经做了手术了吗...")
else:
    print("人妖.......")
```


## 11. 循环语句

* while

```
while 条件:
    continue # 开始下一次循环
    break # 跳出所有循环
```

* 例：

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# Author:Chuncheng.Fan <xmzncc@gmail.com>

i = 0
while i < 3:
    username = input("请输入用户名：")
    password = input("请输入密码：")
    if username == 'fcc' and password == '123':
        print("欢迎登陆！")
        break
    else:
        print('用户名或密码错误')
        i += 1
```


## 12. 练习
    
* 1. 使用while循环输入 1 2 3 4 5 6    8 9 10

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# Author:Chuncheng.Fan <xmzncc@gmail.com>

i = 1
while True:
    if i == 7:
        i += 1
        continue
    print(i)
    i += 1
    if i == 11:
        break
```


* 2. 求1-100的所有数的和

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# Author:Chuncheng.Fan <xmzncc@gmail.com>

value = 0
i = 1
while i < 101:
    value = value + i
    i += 1
print(value)
```

* 3. 输出 1-100 内的所有奇数

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# Author:Chuncheng.Fan <xmzncc@gmail.com>

i = 1

while i < 101:
    if i % 2 == 1:
        print(i)
    i += 1
```

* 4. 输出 1-100 内的所有偶数

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# Author:Chuncheng.Fan <xmzncc@gmail.com>

i = 1

while i < 101:
    if i % 2 == 0:
        print(i)
    i += 1
```

* 5. 求1-2+3-4+5 ... 99的所有数的和

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# Author:Chuncheng.Fan <xmzncc@gmail.com>

value = 0
i = 1

while i < 100:
    if i % 2 == 1:
        value = value + i
        i += 1
    elif i % 2 == 0:
        value = value - i
        i +=1
print(value)
```

* 6. 用户登陆（三次机会重试）

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# Author:Chuncheng.Fan <xmzncc@gmail.com>

i = 0

while i < 3:
    username = input("请输入用户名：")
    password = input("请输入密码：")
    if username == 'fcc' and password == '123':
        print("欢迎登陆！")
        break
    else:
        print("用户名或密码错误")
        i += 1
```


## 13. 运算符
### 13.1 算数运算

| 运算符 | 描述 | 实例 |
| --- | --- | --- |
| + | 加 - 两个对象相加 | a+b 输出结果30 |
| - | 减 - 得到负数或是一个数减去另一个数 | a-b 输出结果-10 |
| * | 乘 - 两个数相乘或是返回一个被重复若干次的字符串 | a*b 输出结果200 |
| / | 除 - x以y | b/a输出结果2 |
| % | 取模 - 返回除法的余数 |  b%a输出结果0 |
| ** | 幂 - 返回x的y次幂 | a**b为10的20次方，输出结果100000000 |
| // | 取整除 - 返回商的整数部分 | 9//2输出结果4，9.0//2.0 输出结果4.0 |


### 13.2 比较运算

| 运算符 | 描述 | 实例 |
| --- | --- | --- |
| == | 等于 - 比较对象是否相等 | （a==b）返回False |
| != | 不等于 - 比较两个对象是否不相等 | （a!=b）返回True |
| <> | 不等于 - 比较两个对象是否不相等 | (a<>b)返回True，这个运算符类似!= |
| > | 大于 - 返回x是否大于y |（a>b）返回False|
| < | 小于 - 返回 |  b%a输出结果0 |
| >= | 幂 - 返回x的y次幂 | a**b为10的20次方，输出结果100000000 |
| <= | 取整除 - 返回商的整数部分 | 9//2输出结果4，9.0//2.0 输出结果4.0 |


### 13.3 赋值运算

| 运算符 | 描述 | 实例 |
| --- | --- | --- |
| = | 简单的赋值运算符 | c=a+b 将 a+b的运算结果赋值为c |
| += | 加法赋值运算符 | c+=a 等效于c=c+a |
| -= | 减法赋值运算符 | c-=a 等效于c=c-a|
| *= | 乘法赋值运算符 | c*=a 等效于c=c*a|
| /= | 除法赋值运算符 |c/=a 等效于c=c/a|
| %= | 取模赋值运算符 |  c%=a 等效于c=c%a |
| **= | 幂赋值运算符 | c\*\*=a 等效于c=c\*\*a |
| //= | 取整除赋值运算符 | c//=a 等效于c=c//a |

### 13.4 逻辑运算

| 运算符 | 描述 | 实例 |
| --- | --- | --- |
| and | 布尔“与”-如果x为False，x and y 返回False，否则它返回y的计算值。 | (a abd b) 返回True |
| or | 布尔“或”-如果x是True，它返回True，否则它返回y的计算值。 | (a or b)返回True |
| not | 布尔“非”-如果x为True，返回False，如果x为False，它返回True。 | not(a and b)返回False |

### 13.5 

| 运算符 | 描述 | 实例 |
| --- | --- | --- |
| in | 如果在指定的序列中找到值返回True，否则返回False。 | x在y序列，如果x在y序列中返回True。 |
| not in | 如果在指定的序列中没有找到值返回True，否则返回False。 | x不在y序列中，如果x不在y序列中返回False。 |

## 14. python数据类型

### 14.1 数字

int（整型）

* 在32位机器上，整数的位数为32位，取值范围为-2**31～2**31-1，即-2147483648～2147483647
* 在64位系统上，整数的位数为64位，取值范围为-2**63～2**63-1，即-9223372036854775808～9223372036854775807


### 14.2 布尔值

真或假
1 或 0

### 14.3 字符串

字符串常用功能:

* 移除空白
* 分割
* 长度
* 索引
* 切片

### 14.4 列表

```
name_list = ['alex', 'seven', 'eric']
或
name_list ＝ list(['alex', 'seven', 'eric'])
```

基本操作:

* 索引
* 切片
* 追加
* 删除
* 长度
* 切片
* 循环
* 包含

### 14.5 元祖

创建元祖：

```
ages = (11, 22, 33, 44, 55)
或
ages = tuple((11, 22, 33, 44, 55))
```
基本操作：

* 索引
* 切片
* 循环
* 长度
* 包含


### 14.6 字典（无序）

创建字典：

```
person = {"name": "mr.wu", 'age': 18}
或
person = dict({"name": "mr.wu", 'age': 18})
```
常用操作：

* 索引
* 新增
* 删除
* 键、值、键值对
* 循环
* 长度

### 14.7 其他

#### 14.7.1 for循环

用户按照顺序循环可迭代对象中的内容，
PS：break、continue

```
li = [11,22,33,44]
for item in li:
    print item
```
#### 14.7.2 enumrate

为可迭代的对象添加序号

```
li = [11,22,33]
for k,v in enumerate(li, 1):
    print(k,v)
```

#### 14.7.3 range和xrange

指定范围，生成指定的数字

```
print range(1, 10)
# 结果：[1, 2, 3, 4, 5, 6, 7, 8, 9]
 
print range(1, 10, 2)
# 结果：[1, 3, 5, 7, 9]
 
print range(30, 0, -2)
# 结果：[30, 28, 26, 24, 22, 20, 18, 16, 14, 12, 10, 8, 6, 4, 2]　　
```

## 15. 练习题

* 元素分类

有如下值集合 

```
v1 = [11,22,33,44,55,66,77,88,99,90]，
```

将所有大于 66 的值保存至字典的第一个key中，将小于 66 的值保存至第二个key的值中。
即： 

```
{'k1': 大于66的所有值, 'k2': 小于66的所有值}
            
v2 = {'k1': [],'k2':[] }
```

答案:

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# Author:Chuncheng.Fan <xmzncc@gmail.com>

v1 = [11,22,33,44,55,66,77,88,99,90]
v2 = {'k1':[],'k2':[]}

for item in v1:
    if item > 66:
        v2['k1'].append(item)
    elif item < 66:
        v2['k2'].append(item)
print(v2)
```
            

* 功能要求：
v = 2000
要求用户输入总资产，例如：2000
显示商品列表，让用户根据序号选择商品，加入购物车
购买，如果商品总额大于总资产，提示账户余额不足，否则，购买成功。

```
            goods = [
                {"name": "电脑", "price": 1999},
                {"name": "鼠标", "price": 10},
                {"name": "游艇", "price": 20},
                {"name": "美女", "price": 998},
            ]
            
            num = input('>>>') # 1
            num = int(num)
            goods[num]['price']
```




* 用户交互，显示省市县三级联动的选择

```
        
dic = {
    "河北": {
             "石家庄": ["鹿泉", "藁城", "元氏"],
                    "邯郸": ["永年", "涉县", "磁县"],
            }
    "河南": {
             ...
                }
    "山西": {
            ...
                }
            }
            
            for v in dic.keys():
                print(v)
            inp = input('>>>')
            dic[inp]
```



## 16. 作业

 * 基于文件存储的用户登录程序（3次登录失败，锁定用户）

 答案：
 
```
 #!/usr/bin/env python
# -*- coding: utf-8 -*-
# Author:Chuncheng.Fan <xmzncc@gmail.com>

f1 = open('db','r')
data = f1.read()
f1.close()

# 1.格式化数据

user_info_list = []
user_str_list = data.split('\n')
for item in user_str_list:
    temp = item.split('|')
    v = {
        'name': temp[0],
        'pwd': temp[1],
        'times': int(temp[2])
    }
    user_info_list.append(v)
print(user_info_list)

# 2.判断用户输入
count = 0
while count < 3:
    username = input('请输入用户名：')
    status = 0
    for user_login_info in user_info_list:
        if username == user_login_info['name']:
            if user_login_info['times'] >= 3:
                print('输入错误3次，用户已锁定，请联系管理员 ~')
                exit()
            else:
                count = 0
                status = 1
                pwd = input('请输入密码：')
                if pwd == user_login_info['pwd']:
                    print('用户 %s 登录成功！' %username)
                    user_login_info['times'] = 0
                    count = 3
                    break
                else:
                    if 0 == 2 - user_login_info['times']:
                        pass
                    else:
                        print('用户名或密码错误，请重新输入。')
                    user_login_info['times'] += 1

                if user_login_info['times'] >= 3:
                    print('输入错误3次，用户已锁定，请联系管理员 ~')
                    count = 3
                    break
    if status == 0:
        print('没有这个用户')
    count += 1

# 3.格式化并写入文件
new_db = ""
for user_login_info in user_info_list:
    user_info_str = user_login_info['name'] + "|" + user_login_info['pwd'] + '|' + str(user_login_info['times'])
    new_db = new_db + user_info_str + '\n'

f2 = open('db','w')
f2.write(new_db.strip())
f2.close()
```

