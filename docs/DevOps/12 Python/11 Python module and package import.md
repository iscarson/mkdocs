
## 一、模块导入
### 1. 定义
Python 模块(Module)，是一个 Python 文件，以 .py 结尾，包含了 Python 对象定义和Python语句。

模块让你能够有逻辑地组织你的 Python 代码段。

把相关的代码分配到一个模块里能让你的代码更好用，更易懂。 

模块能定义函数，类和变量，模块里也能包含可执行的代码。

包括：内置模块，自定义模块，第三方模块；

### 2. 作用
最大的好处是大大提高了代码的可维护性。其次，编写代码不必从零开始。当一个模块编写完毕，就可以被其他地方引用。我们在编写程序的时候，也经常引用其他模块，包括Python内置的模块和来自第三方的模块。

使用模块还可以避免函数名和变量名冲突。相同名字的函数和变量完全可以分别存在不同的模块中，因此，我们自己在编写模块时，不必考虑名字会与其他模块冲突。但是也要注意，尽量不要与内置函数名字冲突。

 

### 3. import

模块定义好后，我们可以使用 import 语句来引入模块，语法如下：

```
import module1[, module2[,... moduleN]
```

**例：**

```
#spam.py
print('from the spam.py')

money=1000

def read1():
    print('spam->read1->money',money)

def read2():
    print('spam->read2 calling read')
    read1()

def change():
    global money
    money=0
```

**导入模块**

```
import spam #只在第一次导入时才执行spam.py内代码,此处的显式效果是只打印一次'from the spam.py',当然其他的顶级代码也都被执行了,只不过没有显示效果.
执行结果：
 from the spam.py
```
 
 
**注：**模块可以包含可执行的语句和函数的定义，这些语句的目的是初始化模块，它们只在模块名第一次遇到导入import语句时才执行（import语句是可以在程序中的任意位置使用的,且针对同一个模块很import多次,为了防止你重复导入，python的优化手段是：第一次导入后就将模块名加载到内存了，后续的import语句仅是对已经加载大内存中的模块对象增加了一次引用，不会重新执行模块内的语句）

 

import导入模块干的事：
1.产生新的名称空间
2.以新建的名称空间为全局名称空间，执行文件的代码
3.拿到一个模块名spam，指向spam.py产生的名称空间

 

* 测试一:money与spam.money不冲突

```
#测试一:money与spam.money不冲突
#test.py
import spam 
money=10
print(spam.money)

'''
执行结果：
from the spam.py
1000
'''
```
 

 

* 测试二：read1与spam.read1不冲突

```
#测试二：read1与spam.read1不冲突
#test.py
import spam
def read1():
    print('========')
spam.read1()

'''
执行结果:
from the spam.py
spam->read1->money 1000
'''
```
 

* 测试三：执行spam.change()操作的全局变量money仍然是spam中的money

```
#测试三：执行spam.change()操作的全局变量money仍然是spam中的
#test.py
import spam
money=1
spam.change()
print(money)

'''
执行结果：
from the spam.py
1
'''
```
 

* as，为模块名起别名

```
 import spam as sm  #sm为spam的别名
 print(sm.money)
```

**例：**

为已经导入的模块起别名的方式对编写可扩展的代码很有用，假设有两个模块xmlreader.py和csvreader.py，它们都定义了函数read_data(filename):用来从文件中读取一些数据，但采用不同的输入格式。可以编写代码来选择性地挑选读取模块，例如

```
if file_format == 'xml':
     import xmlreader as reader
elif file_format == 'csv':
    import csvreader as reader
data=reader.read_date(filename)
```


在一行导入多个模块

```
import sys,os,re
```
 

### 4. From…import

```
from modname import name1[, name2[, ... nameN]]
```

要导入模块 fib 的 fibonacci 函数，使用如下语句：

```
from fib import fibonacci
```

这个声明不会把整个 fib 模块导入到当前的命名空间中，它只会将 fib 里的 fibonacci 单个引入到执行这个声明的模块的全局符号表。

 

from... import..导入模块干的事：

1.产生新的名称空间
2.以新建的名称空间为全局名称空间，执行文件的代码
3.直接拿到就是spam.py产生的名称空间中名字

 

```
#测试一：导入的函数read1，执行时仍然回到spam.py中寻找全局变量money
#test.py
from spam import read1
money=1000
read1()
'''
执行结果:
from the spam.py
spam->read1->money 1000
'''

#测试二:导入的函数read2，执行时需要调用read1(),仍然回到spam.py中找read1()
#test.py
from spam import read2
def read1():
    print('==========')
read2()

'''
执行结果:
from the spam.py
spam->read2 calling read
spam->read1->money 1000
'''
```
 

但如果当前有重名read1或者read2，那么会有覆盖效果。

```
#测试三:导入的函数read1，被当前位置定义的read1覆盖掉了
#test.py
from spam import read1
def read1():
    print('==========')
read1()
'''
执行结果:
from the spam.py
==========
'''
```

需要特别强调的一点是：python中的变量赋值不是一种存储操作，而只是一种绑定关系，如下：

```
from spam import money,read1
money=100 #将当前位置的名字money绑定到了100
print(money) #打印当前的名字
read1() #读取spam.py中的名字money,仍然为1000

'''
from the spam.py
100
spam->read1->money 1000
'''
```
 

from ... import ...
优点:方便，不用加前缀
缺点：容易跟当前文件的名称空间冲突

 

* from ... import...也支持as和导入多模块

```
from spam import read1 as read

from spam import (read1,
                  read2,
                  money)
 ```

* from spam import *
把spam中所有的不是以下划线(_)开头的名字都导入到当前位置，大部分情况下我们的python程序不应该使用这种导入方式，因为*你不知道你导入什么名字，很有可能会覆盖掉你之前已经定义的名字。而且可读性极其的差，在交互式环境中导入时没有问题。 

```
from spam import *   ## 将模块spam中所有的名字都导入到当前名称空间
print(money)
print(read1)
print(read2)
print(change)

'''
执行结果:
from the spam.py
1000
<function read1 at 0x1012e8158>
<function read2 at 0x1012e81e0>
<function change at 0x1012e8268>
'''
```
 

* __all__来控制*（用来发布新版本）
在spam.py中新增一行

```
__all__=['money','read1'] spam import *就这能导入列表中规定的两个名字
```
 

* __name__
spam.py当做脚本执行，__name__='__main__'
spam.py当做模块导入，__name__=模块名

作用：用来控制.py文件在不同的应用场景下执行不同的逻辑

```
.py

def fib(n):    # write Fibonacci series up to n
    a, b = 0, 1
    while b < n:
        print(b, end=' ')
        a, b = b, a+b
    print()

def fib2(n):   # return Fibonacci series up to n
    result = []
    a, b = 0, 1
    while b < n:
        result.append(b)
        a, b = b, a+b
    return result

if __name__ == "__main__":
    import sys
    fib(int(sys.argv[1]))
```

执行

```
fib.py <arguments>
python fib.py 50 
```

### 5.模块的搜索路径

模块的查找顺序是：内存中已经加载的模块->内置模块->sys.path路径中包含的模块

python解释器在启动时会自动加载一些模块，可以使用sys.modules查看，在第一次导入某个模块时（比如spam），会先检查该模块是否已经被加载到内存中（当前执行文件的名称空间对应的内存），如果有则直接引用

如果没有，解释器则会查找同名的内建模块，如果还没有找到就从sys.path给出的目录列表中依次寻找spam.py文件。

特别注意的是：自定义的模块名不应该与系统内置模块重名。

 

在初始化后，python程序可以修改sys.path,路径放到前面的优先于标准库被加载。

 

**注意：**搜索时按照sys.path中从左到右的顺序查找，位于前的优先被查找，sys.path中还可能包含.zip归档文件和.egg文件，python会把.zip归档文件当成一个目录去处理，

```
module.zip foo.py bar.py

import sys
sys.path.append('module.zip')
import foo,bar


sys.path.append('module.zip/lib/python')
```
 

**注意：**windows下的路径不加r开头，会语法错误

```
windows下的路径不加r开头，会语法错误
sys.path.insert(0,r'C:\Users\Administrator\PycharmProjects\a')
```

## 二、包的导入
### 1. 定义
包是一种通过使用‘.模块名’来组织python模块名称空间的方式。

无论是import形式还是from...import形式，凡是在导入语句中（而不是在使用时）遇到带点的，都要第一时间提高警觉：这是关于包才有的导入语法。.的左边必须是包；

包是一个分层次的文件目录结构，它定义了一个由模块及子包，和子包下的子包等组成的 Python 的应用环境。简单来说，包就是文件夹，但该文件夹下必须存在 __init__.py 文件, 该文件的内容可以为空。__int__.py用于标识当前文件夹是一个包。

包是由一系列模块组成的集合。模块是处理某一类问题的函数和类的集合。

**例：**

```
glance/                   package

├── __init__.py      the glance package

├── api                  for api

│   ├── __init__.py

│   ├── policy.py

│   └── versions.py

├── cmd                for cmd

│   ├── __init__.py

│   └── manage.py

└── db                  for db

    ├── __init__.py

    └── models.py
```

文件内容

```


.py
def get():
    print('from policy.py')

.py
def create_resource(conf):
    print('from version.py: ',conf)

.py
def main():
    print('from manage.py')

.py
def register_models(engine):
    print('from models.py: ',engine)
```
 

### 2. import
在使用一个模块中的函数或类之前，首先要导入该模块。模块的导入使用import语句。

调用模块的函数或类时，需要以模块名作为前缀。

import导入文件时，产生名称空间中的名字来源于文件，import 包，产生的名称空间的名字同样来源于文件，即包下的__init__.py，导入包本质就是在导入该文件

**例：**
在与包glance同级别的文件中测试

```
import glance.db.models
glance.db.models.register_models('mysql') 
```
凡是在导入时带点的，点的左边都必须是一个包。

 

### 3.from ... import ...
如果不想在程序中使用前缀符，可以使用from…import…语句将模块导入。

需要注意的是from后import导入的模块，必须是明确的一个不能带点，否则会有语法错误，如：from a import b.c是错误语法

**例：**

在与包glance同级别的文件中测试 

```
from glance.db import models
models.register_models('mysql')

from glance.db.models import register_models
register_models('mysql')
```

执行的文件的当前路径就是sys.path

```
import sys
print(sys.path)
``` 

**注意：**

1.关于包相关的导入语句也分为import和from ... import ...两种，但是无论哪种，无论在什么位置，在导入时都必须遵循一个原则：凡是在导入时带点的，点的左边都必须是一个包，否则非法。可以带有一连串的点，如item.subitem.subsubitem,但都必须遵循这个原则。

2.对于导入后，在使用时就没有这种限制了，点的左边可以是包,模块，函数，类(它们都可以用点的方式调用自己的属性)。

3.对比import item 和from item import name的应用场景：
如果我们想直接使用name那必须使用后者。

 

### 4. __init\__.py文件

不管是哪种方式，只要是第一次导入包或者是包的任何其他部分，都会依次执行包下的__init__.py文件(我们可以在每个包的文件内都打印一行内容来验证一下)，这个文件可以为空，但是也可以存放一些初始化包的代码。

 

### 5.from glance.api import *

从包api中导入所有，实际上该语句只会导入包api下__init__.py文件中定义的名字，我们可以在这个文件中定义__all___:


```
.py中定义
x=10

def func():
    print('from api.__init.py')

__all__=['x','func','policy']
```

此时我们在于glance同级的文件中执行from glance.api import *就导入__all__中的内容（versions仍然不能导入）。

 

### 6.绝对导入和相对导入

最顶级包glance是写给别人用的，然后在glance包内部也会有彼此之间互相导入的需求，这时候就有绝对导入和相对导入两种方式：

绝对导入：以glance作为起始

相对导入：用.或者..的方式最为起始（只能在一个包中使用，不能用于不同目录内）

例：我们在glance/api/version.py中想要导入glance/cmd/manage.py

```
在glance/api/version.py


from glance.cmd import manage
manage.main()


from ..cmd import manage
manage.main()
```

测试结果：注意一定要在于glance同级的文件中测试

```
from glance.api import versions 
``` 

注意：可以用import导入内置或者第三方模块（已经在sys.path中），但是要绝对避免使用import来导入自定义包的子模块(没有在sys.path中)，应该使用from... import ...的绝对或者相对导入,且包的相对导入只能用from的形式。

 

### 7.单独导入包
单独导入包名称时不会导入包中所有包含的所有子模块，如

```
.py中
import glance
glance.cmd.manage.main()

'''
执行结果：
AttributeError: module 'glance' has no attribute 'cmd'

'''
```

解决方法：

```
.py
from . import cmd
 
.py
from . import manage
```

执行：

```
.py中
import glance
glance.cmd.manage.main()
```

千万别问：__all__不能解决吗，__all__是用于控制from...import *