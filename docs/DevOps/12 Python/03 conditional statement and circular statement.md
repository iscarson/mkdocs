
## 1 条件语句
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


## 2 循环语句

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


## 3 练习
    
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


