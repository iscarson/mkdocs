

### 元组（tuple）

元祖为不可被修改的列表，不可变类型 

基本操作：

* 索引
* 切片
* 循环
* 长度
* 包含

以下为详细介绍 ~

> count 查找元素个数

```
user_tuple = ('xmzncc','asd','fcc')
v = user_tuple.count('xmzncc')
print(v)
```

> index 获取元祖中第一个元素的索引位置

```
user_tuple = ('xmzncc','asd','fcc','xmzncc')
v = user_tuple.index('xmzncc')
print(0)
```

###  字典（dict）

> get 根据key值去对应的value
> 取不到值返回None，不报错

```
dic = {'k1':'v1','k2':'v2'}
v = dic.get('k1')
print(v)
```

> clear 清空

```
dic = {'k1':'v1','k2':'v2'}
v = dic.clear()
print(dic)
```

> copy 拷贝（浅拷贝）

```
dic = {'k1':'v1','k2':'v2'}
v = dic.copy()
print(dic)
```

> pop 删除并获取对应的value值

```
dic = {'k1':'v1','k2':'v2'}
v = dic.pop('k1')
print(dic)
print(v)
```

> popitem 随机删除键值对，并获取到删除的键值

```
dic = {'k1':'v1','k2':'v2'}
v = dic.popitem()
print(v)
print(dic)
```

> setdefault 增加，如果key值存在则不操作

```
dic = {'k1':'v1','k2':'v2'}
dic.setdefault('k3','v3')
print(dic)
```

> update 批量增加或修改

```
dic = {'k1':'v1','k2':'v2'}
dic.update({'k3':'v3','k4':'v4'})
print(dic)
```

