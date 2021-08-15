## 一、BS模式
BS(Browser-Server)模式：顾名思义为浏览器-服务器的意思，对比的话类似我们PC上面浏览器使用的产品即为BS模式产品，例如google doc、各类网站等。

服务端开启一个socke进程

```
#!/usr/bin/env python
# -*- coding:utf-8 -*-
import socket

def main():

    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.bind(('localhost',8082))
    sock.listen(5)

    while True:
        print("server is working.....")
        conn, address = sock.accept()

        request = conn.recv(1024)

        conn.sendall(bytes("HTTP/1.1 201 OK\r\n\r\n<h1>Hello world</h1>","utf8"))
        conn.close()

if __name__ == '__main__':

    main()
```
 

浏览器输入127.0.0.1:8082访问到对应的网站

```
<h1>Hello world</h1>
```

## 二、HTML的定义

超文本标记语言（英语：HyperText Markup Language，简称：HTML）是一种用于创建网页的标准标记语言。可以简单理解为一套规则，浏览器认识的规则。

HTML是一种基础技术，常与CSS、JavaScript一起被众多网站用于设计令人赏心悦目的网页、网页应用程序以及移动应用程序的用户界面。网页浏览器可以读取HTML文件，并将其渲染成可视化网页。HTML描述了一个网站的结构语义随着线索的呈现，使之成为一种标记语言而非编程语言。需要注意的是，对于不同的浏览器，对同一标签可能会有不完全相同的解释（兼容性）。

静态网页文件扩展名：.html 或 .htm

 

## 三、HTML的结构
pcharm创建出来的默认html文档

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>网页标题</title>
</head>
<body>

</body>
</html>
```
 
```
<!DOCTYPE html> 告诉浏览器使用什么样的html或者xhtml来解析html文档
<html></html>是文档的开始标记和结束标记。此元素告诉浏览器其自身是一个 HTML 文档，在它们之间是文档的头部<head>和主体<body>。
<head></head>元素出现在文档的开头部分。<head>与</head>之间的内容不会在浏览器的文档窗口显示，但是其间的元素有特殊重要的意义。
<title></title>定义网页标题，在浏览器标题栏显示。 
<body></body>之间的文本是可见的网页主体内容
```

## 四、HTML的标签格式
标签的语法：

> <标签名 属性1=“属性值1” 属性2=“属性值2”……>内容部分</标签名>
> <标签名 属性1=“属性值1” 属性2=“属性值2”…… />



 

## 五、常用标签
### 1. <!DOCTYPE>标签
<!DOCTYPE> 声明位于文档中的最前面的位置，处于 <html> 标签之前。此标签可告知浏览器文档使用哪种 HTML 或 XHTML 规范。

> 作用：声明文档的解析类型(document.compatMode)，避免浏览器的怪异模式。

document.compatMode：

* BackCompat：怪异模式，浏览器使用自己的怪异模式解析渲染页面。
* CSS1Compat：标准模式，浏览器使用W3C的标准解析渲染页面。
这个属性会被浏览器识别并使用，但是如果你的页面没有DOCTYPE的声明，那么compatMode默认就是BackCompat

 

### 2. <head>内常用标签
#### 2.1 <meta>标签

meta介绍
<meta>元素可提供有关页面的元信息（meta-information），针对搜索引擎和更新频度的描述和关键词。
<meta>标签位于文档的头部，不包含任何内容。
<meta>提供的信息是用户不可见的

meta标签的组成：meta标签共有两个属性，它们分别是http-equiv属性和name 属性，不同的属性又有不同的参数值，这些不同的参数值就实现了不同的网页功能。 

* name属性: 主要用于描述网页，与之对应的属性值为content，content中的内容主要是便于搜索引擎机器人查找信息和分类信息用的。    

```
<meta name="keywords" content="meta总结,html meta,meta属性,meta跳转">
 
<meta name="description" content="百度是个搜索引擎">
```

* http-equiv属性：相当于http的文件头作用，它可以向浏览器传回一些有用的信息，以帮助正确地显示网页内容，与之对应的属性值为content，content中的内容其实就是各个参数的变量值。

```
<meta http-equiv="Refresh" content="2;URL=https://www.baidu.com"> //(注意后面的引号，分别在秒数的前面和网址的后面)
 
<meta http-equiv="content-Type" charset=UTF8">
 
<meta http-equiv = "X-UA-Compatible" content = "IE=EmulateIE7" /> 
```

#### 2.2 非meta标签

    <title>jd</title>
    <link rel="icon" href="http://www.jd.com/favicon.ico">
    <link rel="stylesheet" href="css.css">
    <script src="hello.js"></script>
 

### 3. <body>内常用标签

#### 3.1 基本标签（块级标签和内联标签）

* 块级元素(block)特性：

> 总是独占一行，表现为另起一行开始，而且其后的元素也必须另起一行显示;
宽度(width)、高度(height)、内边距(padding)和外边距(margin)都可控制;

* 内联元素(inline)特性：

> 和相邻的内联元素在同一行;
宽度(width)、高度(height)、内边距的top/bottom(padding-top/padding-bottom)和外边距的top/bottom(margin-top/margin-bottom)都不可改变，就是里面文字或图片的大小;

```
块级元素主要有：
 address , blockquote , center , dir , div , dl , fieldset , form , h1 , h2 , h3 , h4 , h5 , h6 , hr , isindex , menu , noframes , noscript , ol , p , pre , table , ul , li
```

```
内联元素主要有：
a , abbr , acronym , b , bdo , big , br , cite , code , dfn , em , font , i , img , input , kbd , label , q , s , samp , select , small , span , strike , strong , sub , sup ,textarea , tt , u , var

可变元素(根据上下文关系确定该元素是块元素还是内联元素)：
applet ,button ,del ,iframe , ins ,map ,object , script
```
 

```
<hn>: n的取值范围是1~6; 从大到小. 用来表示标题.

<p>: 段落标签. 包裹的内容被换行.并且也上下内容之间有一行空白.

<b> <strong>: 加粗标签.

<strike>: 为文字加上一条中线.

<em>: 文字变成斜体.

<sup>和<sub>: 上角标 和 下角标.

<br>:换行.

<hr>:水平线

特殊字符：
      &lt; &gt；&quot；&copy;&reg;

```
 

#### 3.2 \<div>和\<span>

\<div>\</div>： \<div>只是一个块级元素，并无实际的意义。主要通过CSS样式为其赋予不同的表现. 
\<span>\</span>： \<span>表示了内联行(行内元素),并无实际的意义,主要通过CSS样式为其赋予不同的表现. 

块级元素与行内元素的区别
所谓块元素，是以另起一行开始渲染的元素，行内元素则不需另起一行。如果单独在网页中插入这两个元素，不会对页面产生任何的影响。
这两个元素是专门为定义CSS样式而生的。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>


<div style="background-color: gold;">yuan</div>
<span style="background-color: green;">span</span>



</body>
</html>
```
 

#### 3.3 超链接标签(锚标签): <a> </a>

什么是超级链接？ 所谓的超链接是指从一个网页指向一个目标的连接关系，这个目标可以是另一个网页，也可以是相同网页上 的不同位置，还可以是一个图片，一个电子邮件地址，一个文件，甚至是一个应用程序。

```
<a href="" target="_blank" >click</a>
```

href属性指定目标网页地址。该地址可以有几种类型：

绝对 URL - 指向另一个站点（比如 href="http://www.jd.com）
相对 URL - 指当前站点中确切的路径（href="index.htm"）
锚 URL - 指向页面中的锚（href=""）

 

**补充：**

```
什么是URL？
URL是统一资源定位器(Uniform Resource Locator)的缩写，也被称为网页地址，是因特网上标准的资源的地址。
URL举例
http://www.sohu.com/stu/intro.html
http://222.172.123.33/stu/intro.html

URL地址由4部分组成
第1部分：为协议：http://、ftp://等 
第2部分：为站点地址：可以是域名或IP地址
第3部分：为页面在站点中的目录：stu
第4部分：为页面名称，例如 index.html
各部分之间用“/”符号隔开。
```
 

#### 3.4 图形标签: <img> 

src: 要显示图片的路径.

alt: 图片没有加载成功时的提示.

title: 鼠标悬浮时的提示信息.

width: 图片的宽

height:图片的高 (宽高两个属性只用一个会自动等比缩放.)

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h2>插入图片</h2>

<img src="s.png" width="200px" height="100px" alt="美女" title="包小姐">


<a href="http://www.baidu.com" target="_blank">click</a>
<a href="index.html" target="_blank">click2</a>

<a href="http://www.baidu.com"><img src="s.png" alt=""></a>

</body>
</html>
```
 

#### 3.5 列表标签


\<ul>: 无序列表 [type属性：disc(实心圆点)(默认)、circle(空心圆圈)、square(实心方块)]
\<ol>: 有序列表
\<li>:列表中的每一项.
\<dl> 定义列表
\<dt> 列表标题
\<dd> 列表项
 
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
        <ul>
            <li>1111</li>
            <li>2222</li>
            <li>3333</li>
        </ul>

        <ol>
            <li>1111</li>
            <li>2222</li>
            <li>3333</li>
        </ol>

         <dl>
             <dt>河北省</dt>
             <dd>保定</dd>
             <dd>石家庄</dd>
             <dd>雄安</dd>
         </dl>
</body>
</html>
```
 

#### 3.6 表格标签: \<table>


表格是一个二维数据空间，一个表格由若干行组成，一个行又有若干单元格组成，单元格里可以包含文字、列表、图案、表单、数字符号、预置文本和其它的表格等内容。
表格最重要的目的是显示表格类数据。表格类数据是指最适合组织为表格格式（即按行和列组织）的数据。


* 表格的基本结构：

```
<table>
         <tr>
                <td>标题</td>
                <td>标题</td>
         </tr>
         
         <tr>
                <td>内容</td>
                <td>内容</td>
         </tr>
</table>
```

\<tr>: table row
\<th>: table head cell
\<td>: table data cell

 

* 表格的属性：

```
    border: 表格边框.

    cellpadding: 内边距

    cellspacing: 外边距.

    width: 像素 百分比.（最好通过css来设置长宽）

    rowspan:  单元格竖跨多少行

    colspan:  单元格横跨多少列（即合并单元格）
```

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
            <table border="1" cellpadding="10px" cellspacing="5px">
                <tr>
                    <th>姓名</th>
                    <th>年龄</th>
                    <th>性别</th>
                    <th>工资</th>
                </tr>
                <tr>
                    <td>111</td>
                    <td>111</td>
                    <td>111</td>
                    <td>111</td>
                </tr>
                <tr>
                    <td colspan="2">222</td>

                    <td>222</td>
                    <td rowspan="2">222</td>
                </tr>
                <tr>
                    <td>333</td>
                    <td>333</td>
                    <td>333</td>
                </tr>
            </table>

</body>
</html>
```


 

* 表单标签: <form>

      功能：表单用于向服务器传输数据，从而实现用户与Web服务器的交互

      表单能够包含input系列标签，比如文本字段、复选框、单选框、提交按钮等等。

      表单还可以包含textarea、select、fieldset和 label标签。

> 表单属性

     action: 表单提交到哪.一般指向服务器端一个程序,程序接收到表单提交过来的数据（即表单元素值）作相应处理，比如https://www.sogou.com/web

     method: 表单的提交方式 post/get默认取值就是get

> 表单元素

基本概念：
HTML表单是HTML元素中较为复杂的部分，表单往往和脚本、动态页面、数据处理等功能相结合，因此它是制作动态网站很重要的内容。
表单一般用来收集用户的输入信息
表单工作原理：
访问者在浏览有表单的网页时，可填写必需的信息，然后按某个按钮提交。这些信息通过Internet传送到服务器上。 
服务器上专门的程序对这些数据进行处理，如果有错误会返回错误信息，并要求纠正错误。当数据完整无误后，服务器反馈一个输入完成的信息

 
> \<input>系列标签

```
<1> 表单类型

type:        text 文本输入框

             password 密码输入框

             radio 单选框

             checkbox 多选框  

             submit 提交按钮            

             button 按钮(需要配合js使用.) button和submit的区别？

             file 提交文件：form表单需要加上属性enctype="multipart/form-data" 
            
            上传文件注意两点：
请求方式必须是post
enctype="multipart/form-data"

 <2> 表单属性

 name:    表单提交项的键.

           注意和id属性的区别：name属性是和服务器通信时使用的名称；
           而id属性是浏览器端使用的名称，该属性主要是为了方便客户端编程，而在css和javascript中使用的

value:    表单提交项的值.对于不同的输入类型，value 属性的用法也不同：

                type="button", "reset", "submit" - 定义按钮上的显示的文本
                 
                type="text", "password", "hidden" - 定义输入字段的初始值
                 
                type="checkbox", "radio", "image" - 定义与输入相关联的值


checked:  radio 和 checkbox 默认被选中

readonly: 只读. text 和 password

disabled: 对所用input都好使.

```
 

> select标签

```
 <select> 下拉选标签属性


          name:表单提交项的键.

          size：选项个数

          multiple：multiple 
                 <optgroup>为每一项加上分组

                 <option> 下拉选中的每一项 属性：

                       value:表单提交项的值.   
                       selected: selected下拉选默认被选中

```
 

> \<textarea> 多行文本框

```

<form id="form1" name="form1" method="post" action="">
        <textarea cols=“宽度” rows=“高度” name=“名称”>
                   默认内容
        </textarea>
</form>

```

> \<label>标签

定义：<label> 标签为 input 元素定义标注（标记）。
说明：
1 label 元素不会向用户呈现任何特殊效果。
2 <label> 标签的 for 属性值应当与相关元素的 id 属性值相同。

```

<form method="post" action="">

        <label for=“username”>用户名</label>
        <input type=“text” name=“username” id=“username” size=“20” />
</form>

```
 

> \<fieldset>标签

```

<fieldset>
    <legend>登录吧</legend>
    <input type="text">
</fieldset>
```
 

 

**例:**

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
        <h3>注册页面</h3>

        <form action="" method="post">

            <p>姓名：<input type="text" name="username" value="yaun"></p>
            <p>密码：<input type="password" name="pwd" value="123"></p>

            <p>爱好：<input type="checkbox" name="hobby" value="basktball">篮球
                     <input type="checkbox" name="hobby" value="football">足球
                     <input type="checkbox" name="hobby" value="ball">球
            </p>
            <p>
               性别：<input type="radio" name="sex" value="1">男
                     <input type="radio" name="sex" value="0">女
            </p>

            <p><input type="button" value="Submit"></p>
            <p><input type="file"></p>
            <p><input type="reset"></p>






            <p><input type="submit" value="Submit"></p>

        </form>

</body>
</html>

<!--{"username":"yuan","pwd":123,"hobby":[],"sex":1}-->
```


 

 

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
        <h3>注册页面</h3>

        <form action="" method="post">

            <p>
                <label for="user">姓名：</label>
                <input type="text" name="username"  id="user">

            </p>
            <p>
                <label for="pwd">密码：</label>
                <input type="password" name="pwd" id="pwd">
            </p>

            <p>爱好：<input type="checkbox" name="hobby" value="basktball" checked="checked">篮球
                     <input type="checkbox" name="hobby" value="football">足球
                     <input type="checkbox" name="hobby" value="ball">球
            </p>
            <p>
               性别：<input type="radio" name="sex" value="1">男
                     <input type="radio" name="sex" value="0">女
            </p>

            <select name="province" id=""  multiple >
                <option value="hebei">河北省</option>
                <option value="henan">河南省</option>
                <option value="hubei" selected>湖北省</option>
                <option value="guangdong" selected>广东省</option>
                <option value="yunnan">云南省</option>
            </select>

            <p>简介：
                 <textarea cols="23" rows="10" name="person">

                 </textarea>
            </p>

            <fieldset>
                <legend>登录吧</legend>
                <input type="text">
            </fieldset>


            <p><input type="submit" value="Submit"></p>

        </form>

</body>
</html>

<!--{"username":"yuan","pwd":123,"hobby":[],"sex":1,"province":"henan"}-->
```

