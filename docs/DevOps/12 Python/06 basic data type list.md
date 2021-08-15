

例：

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

详细介绍 如下 ~

> append 追加

```
user_list = ['xmzncc','asd','fcc']
user_list.append('123')
print(user_list)
```

> clear 清空

```
user_list = ['xmzncc','asd','fcc']
user_list.clear()
print(user_list)
```

> copy 拷贝（浅拷贝）

```
user_list = ['xmzncc','asd','fcc']
v = user_list.copy()
print(v)
```

> count 计数

```
user_list = ['xmzncc','asd','fcc','xmzncc']
v = user_list.count('xmzncc')
print(v)
```

> extend 扩展原列表

```
user_list = ['xmzncc','asd','fcc']
user_list.extend(['qwe'])
print(user_list)
```

> index 查找元素索引

```
user_list = ['xmzncc','asd','fcc']
v = user_list.index('xmznc')
print(v)
```

> pop 删除索引，获取元素

```
user_list = ['xmzncc','asd','fcc']
v = user_list.pop(0)
print(v)
print(user_list)
```

> remove 删除元素

```
user_list = ['xmzncc','asd','fcc']
v = user_list.remove('xmzncc')
print(v)
print(user_list)
```

> reverse 翻转

```
user_list = ['xmzncc','asd','fcc']
user_list.reverse()
print(user_list)
```

> sort 排序
> 从大到小

```
nums = [11,12,13,4,6,7,99,101]
nums.sort()
print(nums)
```

> 从小到大

```
nums = [11,12,13,4,6,7,99,101]
nums.sort(reverse=True)
print(nums)
```




