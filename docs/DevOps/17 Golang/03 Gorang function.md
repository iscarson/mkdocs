[TOC]

# Go函数

* Go函数概念

```go
/*
 go函数定义使用
func functi_name( [parameter list] ) [return_types] {
   函数体
}
函数function
	Go函数不支持嵌套、重载和默认菜蔬
	但支持一下特性：
		无需声明原型、不定长度变参、多返回值、命名返回值参数、匿名函数、闭包
	定义函数使用关键字func，且做大括号不能另起一行
	函数也可以作为一种类型使用
defter
	执行方式类型其他语言中的析构函数，在函数体执行结束后按照调用顺序的想反顺序逐个执行
	即使函数发生严重错误也会执行
	支持匿名函数的调用
	常用语资源清理，文件关闭，解锁以及记录时间等操作
	通过与匿名函数配合可在return之后修改函数计算结果
	如果函数体内某个变量作为defer时匿名函数的参数，则在定义defer时即已经获取了拷贝，否则则是应用某个变量的地址
	Go没有异常机制，但是panic/recover模式来处理错误
	Panic可以再任何地方引发，但是recover只有在defer调用的函数中有效
 */
```



* Go函数定义

    ```go
    func function_name( [parameter list] ) [return_types] {
       函数体
    }
    ```
    * func：Go的函数声明关键字，声明一个函数。
    * function_name：函数名称，函数名和参数列表一起构成了函数签名。
    * parameter list：参数列表，指定的是参数类型、顺序、及参数个数。参数是可选的，即函数可以不包含参数。参数就像一个占位符，这是参数被称为形参，当函数被调用时，将具体的值传递            给参数，这个值被称为实际参数。
    * return_types：返回类型，函数返回一列值。return_types 是该列值的数据类型。这里需要注意的是Go函数支持多返回值。有些功能不需要返回值，这种情况下 return_types 不是必须的。
    * 函数体：函数定义的代码集合，表示函数完成的动作。

## 函数调用
   * 1.小写字母开头的函数只在本包内可见，大写字母开头的函数才能被其他包使用。 
   * 2.同时这个规则也适用于变量的可见性，即首字母大写的变量才是全局的

    ```go
    func max(num1,num2 int) int  {
    	var result int
    	if num1 > num1 {
    		result = num1
    	}else {
    		result = num2
    	}
    	return result
    }
    
    func main()  {
    	var a int = 100
    	var b int = 200
    	var ret int
    	ret = max(a,b)
    	fmt.Println(ret)
    }
    ```
   * 多返回值
   ```go
    func swap(x,y string) (string,string)  {
    	return y,x
    }
    func main()  {
    	//a,b := swap("hello","world")
    	a,_ := swap("hello","world") //只关注一个值可以使用下划线代替
    	fmt.Println(a)
    }
   ```
## 函数参数
   * 值传递：指在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。
   * 引用传递：是指在调用函数时将实际参数的地址传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数。
```go
* 值传递
    func main()  {
    	var a int = 100
    	var b int = 200
    	fmt.Println("交换前",a)
    	fmt.Println("交换后",b)
    	swap(a,b)
    	fmt.Println("交换后",a)
    	fmt.Println("交换后",b)
    }
    func swap(x,y int) int  {
    	var temp int
    	temp = x
    	x = y
    	y = temp
    	return temp
    }
* 执行结果
交换前 100
交换后 200
交换后 100
交换后 200
```
```go
* 引用传递
    func main()  {
    	var a int = 100
    	var b int = 200
    	fmt.Println("交换前",a)
    	fmt.Println("交换后",b)
    	/* 调用 swap() 函数
    	&a 指向 a 指针，a 变量的地址
    	&b 指向 b 指针，b 变量的地址
    	*/
    	swap(&a,&b)
    	fmt.Println("交换后",a)
    	fmt.Println("交换后",b)
    }
    func swap(x,y *int) int  {
    	var temp int
    	temp = *x
    	*x = *y
    	*y = temp
    	return temp
    }
* 执行结果
交换前 100
交换后 200
交换后 200
交换后 100
```
## 不定参数

### 不定参数类型

* …type在Go中只能作为形参出现，而且只能作为函数的最后一个参数
* 函数的定义

```go
func myfunc(args ...int) {
}
```

```go
* 引用参数
    func main()  {
        var a int = 100
        var b int = 200
        myfuc(a,b)
    }
    func myfuc(args ... int)  {
        fmt.Println(args)
        for _,args := range args {
            fmt.Println(args)
        }
    }
* 执行结果
[100 200]
100
200
```

### 不定参数传递

```go
* 参数可以统一赋值(数组或者切片)
    func main()  {
        arr := []int{100,200,300}
        myfuc(arr...)
        myfuc(arr[:2]...)
    }
    func myfuc(args ... int)  {
        fmt.Println(args)
        for _,args := range args {
            fmt.Println(args)
        }
    }
* 执行结果
[100 200 300]
100
200
300
[100 200]
100
200
```

### 任意类型不定参数

* 使用interface函数传递类型，接受任意类型数据

```go
* interface类型是安全的
    func main()  {
        arr := []int{100,200,300}
        myfuc(100,"abc",arr)
    }
    func myfuc(args ...interface{})  {
        fmt.Println(args)
        for _,args := range args{
            fmt.Println(args)
            fmt.Println(reflect.TypeOf(args))
            fmt.Println("=======")
        }
    }
* 执行结果
[100 abc [100 200 300]]
100
int
=======
abc
string
=======
[100 200 300]
[]int
=======
```

## 匿名函数

```go
* 将函数当做一个变量使用
func main()  {
	getSqrt := func(a float64) float64 {
		return math.Sqrt(a)
	}
	fmt.Println(getSqrt(4))
}
* 执行效果
2
```

## 闭包

* 闭包是有函数及其相关引用环境组合而成的实体(即：闭包=函数+引用环境)
* “官方”的解释是：所谓“闭包”，指的是一个拥有许多变量和绑定了这些变量的环境的表达式（通常是一个函数），因而这些变量也是该表达式的一部分。

```go
* 第一个例子
func a() func() int {
	i := 0
	b := func() int {
		i++
		fmt.Println(i)
		return i
	}
	return b
}

func main() {
	c := a()
	c()
	c()
	c()

	//a() //不会输出i
}

* 第二个例子
func outer(x int) func(int) int {
	return func(y int) int {
		return x + y
	}
}

func main() {
	f := outer(10)
	fmt.Println(f(100))
}
```