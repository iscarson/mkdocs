## 1. 函数对象
函数是第一类对象，即函数可以当做数据传递

*  可以被引用
*  可以当做参数传递
*  返回值可以是函数
*  可以当做容器类型的元素

```
def foo():
    print('from foo')

def index():
    print('from index')

dic = {
    'foo':foo,
    'index':index,
}

while True:
    choice = input(">>>>>").strip()
    if choice in dic:
        dic[choice]()
```

## 2. 函数的嵌套
### 2.1 函数的嵌套的调用


```
def max(x,y):
    return x if x > y else y

def max4(a,b,c,d):
    res1 = max(a,b)
    res2 = max(res1,c)
    res3 = max(res2,d)
    return res3
print(max4(234,456,123,789))
```
### 2.2 函数的嵌套定义

```
def f1():
    def f2():
        def f3():
            print("from f3")
        f3()
    f2()
f1()
# 返回值 from f3 ，即 f3的值
```

## 3. 名称空间

名称空间：存放名字的地方
名称空间分为三种

### 3.1 内置名称空间

随着python解释器的启动而产生

```
a = [1,2,3,4,5]
print(max(a))
```

### 3.2 全局名称空间

文件的执行会产生全局名称空间，指的是文件级别定义的名字都会放入改空间

```
x = 1
def fun():
    x = 2
    print(x)
fun()   
print(x)
```

### 3.3 局部名称空间
调用函数时会产生局部名称空间，只在函数调用时临时绑定，调用结束解绑定

```
x = 10000
def func():
    x = 1
    def f1():
        print(x)
        def f2():
            print(x)
        f2()
    f1()
func()
```

## 4. 作用域
作用域即范围（作用域关系是在函数定义阶段就已经固定的，与函数的调用位置无关）
查看作用域：globals(),locals()



## 4. 闭包函数

```
def f1():
    x = 1
    y = 2
    def f2():
        print(x,y)
    return f2

f = f1()
print(f.__closure__[0])
print(f.__closure__[0].cell_contents)
```

## 5. 装饰器
## 6. 迭代器
## 7. 生成器

```
def foo():
    print('一')
    yield  1
    print('二')
    yield 2
    print('三')
    yield 3
    print('四')

g = foo()
# for i in g:
#     print(i)

print(next(g))
print(next(g))
print(next(g))
print(next(g))
```

## 8. 内置函数

| - | - | Built-in Functions | - | - |
| --- | --- | --- | --- | --- |
| abs() | dict() | help() | min() | stator() |
| all() | dir() | hex() | next() | slice() |
| any() | divmod() | id() | object() | sorted() |
| ascii() | enumerate() | input() | oct() | staticmethod() |
| bin() | enav() | int() | open() | str() |
| bool() | exec() | isinstance() | ord() | sun() |
| bytearray() | filter() | issubclass() | pow() | super() |
| bytes() | float() | iter() | print() | tuple() |
| callable() | format() | len() | property() | type() |
| chr() | frozenset() | list() | range() | vars() |
| classmethod() | getattr() | locals() | repr() | zip() |
| compile() | globals() | map() | reversed() | \__import\__() |
| complex() | hasattr() | max() | round() | - |
| delattr() | hash() | memoryview() | set() | - |

