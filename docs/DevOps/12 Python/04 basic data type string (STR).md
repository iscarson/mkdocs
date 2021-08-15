
字符串常用功能:

* 移除空白
* 分割
* 长度
* 索引
* 切片

下面来详细介绍下 ~

> capitalize 字符串首字母大写

```
name = 'xmzncc'
v = name.capitalize()
print(v)
```
> casefold 将所有大写变小写（支持多种国家语言）

```
name = 'Xmzncc'
v = name.casefold()
print(v)
```
> lower 将大写变成小写（只支持英文）

```
name = 'Xmzncc'
v = name.lower()
print(v)
```

> center 文本居中
> 参数1: 表示总长度
> 参数2：空白处填充的字符（长度为1）

```
name = 'xmzncc'
v = name.center(20,'*')
print(v)
```

> rjust,ljust 左右填充，包含自身长度

```
name = 'xmzncc'
v = name.rjust(20,'*')
print(v)
```

```
name = 'xmzncc'
v = name.ljust(20,'*')
print(v)
```

> count 表示传入之在字符串中出现的次数
> 参数1： 要查找的值（子序列）
> 参数2： 起始位置（索引）
> 参数3： 结束位置（索引）

```
name = 'asdasdjasdhaiuyeluqjh'
v = name.count('as')
print(v)
```

> endswith 是否以xx结尾

```
name = 'xmzncc'
v = name.endswith('cc')
print(v)
```

> startswith 是否以xx开头

```
name = 'xmzncc'
v = name.startswith('xm')
print(v)
```

> index 找到指定子序列索引的所在位置

```
name = 'xmzncc'
v = name.index('c')
print(v)
```

> find 找到指定子序列索引的所在位置，不存在返回 -1，不报错
> 与index 不同，index找不到报错

```
name = 'xmzncc'
v = name.find('f')
print(v)
```

> format 字符串格式化

```
tpl = '我是:{0};年龄{1};性别{2}'
v = tpl.format('fcc',18,'man')
print(v)
```

> isalnum/isalpha 是否是数字、汉字

```
name  = 'xmzncc范春成'
v = name.isalnum() 
print(v)
v2 = name.isalpha()
print(v2)
```

> isdecima/isdigit/isnumeric 判断是否是数字
> isdecima 仅可以判断阿拉伯数字
> isdigit 可判断阿拉伯数字、②
> isnumeric 可判断阿拉伯数字、②、三

```
name = '2'
v1 = name.isdecimal()
print(v1)
v2 = name.isdigit()
print(v2)
v3 = name.isnumeric()
print(v3)
```

> isidentifier 是否为表示符
> 内置关键字除外

```
name = 'xmzncc'
v = name.isidentifier()
print(v)
```

> islower 是否全部为小写

```
name ='Xmzncc'
v = name.islower()
print(v)
```

> isupper 是否全部为大写

```
name = 'XMZNCC'
v = name.isupper()
print(v)
```

> upper 全部变为大写

```
name = 'xmzncc'
v = name.upper()
print(v)
```


> lower 全部变为小写

```
name = 'XMZNCC'
v = name.lower()
print(v)
```

> isprintable 是否包含隐含的 xxx
> 如果有返回False

```
name = 'asdasdadasd\tsadasd'
v = name.isprintable()
print(v)
```

> isspace 是否全部为空格

```
name = '     '
v = name.isspace()
print(v)
```

> join 元素拼接字符串

```
name = 'xmzncc'
v = '_'.join(name)
print(v)
```

> translate 对应关系再翻译
> 也就是说 查找到并替换

```
m = str.maketrans('asd','xxx')
name = "123asd890"
v = name.translate(m)
print(v)
```

> partition 分割并且保留分割元素

```
name = 'xmzncc000fcc'
v = name.partition('000')
print(v)

```

> replace 替换
> 可设置索引位置

```
name = 'xmzncc000fcc000asdasd'
v = name.replace('000','111',1)
print(v)
```

> strip 移除空白

```
name = 'xmzncc\n'
v = name.strip()
print(v)
```

> swapcase 大小写相互转换

```
name = 'XMznCC'
v = name.swapcase()
print(v)
```

> zfill 填充 0 

```
name = 'xmzncc'
v = name.zfill(20)
print(v)
```

**字符串功能总结：**

```
name.upper()
name.lower()
name.split()
name.find()
name.strip()
name.startswith()
name.format()
name.replace()
"xmzncc".join(["as",'bb'])
```

**额外功能：**

```
name[0]
name[0:3]
name[0:3:2]
len(name)
for循环，每个元素是字符
```
