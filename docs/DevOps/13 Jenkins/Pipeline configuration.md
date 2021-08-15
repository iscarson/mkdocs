# Pipeline 配置

## 必选参数
* pipeline/node 代表整条流水线，意思为：显示和隐式脚本
* stages：stage的容器，最少包含一个stage。
* stage：阶段每个阶段必有名称。
* steps：代表stage中一个具体的步骤，每个stage只能包含一个steps
* agent：Jenkins执行客户端。

## 指令
* environment：环境变量。stage或者是pipeline部分
* tools：自动下载或者指定自定义工具。stage或者是pipeline部分
* input：定义在stage部分，会暂停pipeline，提示输入内容
* options：配置jenkins本身选项。可定义在stage和pipeline部分
    * buildDiscarder：保存最近历史构建的数量。
    * checkoutToSubdirectory：重定向工作目录
    * disableConcurrentBuilds：默认构建时可以并发的，关闭选项
    * newContainerPerStage：agent为docker或者是dockerfile时stage都分别运行在一个新的容器，而不是所有stage都运行在同一个容器中。
    * retry：指定构建失败试重试次数。
    * timeout：当pipeline执行时间长，jenkins终止pipeline执行。
        * 单位为：SENCONDS（秒）、MIUNTES（分秒）
* parallel：并行执行多个step。
* parameters：与input不同，parameters是执行pipeline前传入参数
* triggers用于定义之行pipeline的触发器
* when：当满足定义条件时，阶段才执行
* script: 执行groovy脚本。放置在steps中。

## 内置基础步骤
* deleteDir：删除当前目录，通常与dir一同使用。
* dir：切换到工作目录
* fileExists：判断文件是否存在
* isUnix：判断是否为Unix系统
* pwd：返回当前目录。
* writeFile：将内容写入文件
    * file：文件路径，可以是绝对路径，也可以是相对路径
    * text：要写入文件内容
    * encoding（可选）：目标文件的编码。
* readFile：读取文件内容
    * file：文件路径，可以是绝对路径，也可以是相对路径

## 制品相关步骤
* stash：保存临时文件。跨jenkins node使用，构建的制品临时保存供其他阶段使用。
    * name：字符串类型，保存文件集合的唯一标识。
    * allowEmpty：布尔类型，允许stash内容为空。
    * excludes：字符串类型，排除文件，多个文件逗号分隔。
    * includes：字符串类型，stash哪些文件，留空代表文件夹下所有文件。
    * useDefaultExcludes：布尔类型。如果为true代表使用Ant风格路径默认排除文件列表。
* unstash：取出之前stash文件
    * name：取出文件唯一标识。

## 命令相关步骤
***命令相关步骤组件：Node and Processes插件（jenkins自带组件）***
* sh 执行shell命令
    * script: 将要执行的shell脚本
    * encoding：脚本执行后输出日志的编码
    * returnStatus：布尔类型，默认脚本返回的是状态码。
    * returnStdout：布尔类型，如果为true，则任务的标准输出将作为步骤的返回值，而不打印到日志中（如有报错，则会打印到日志中）。
    * 除了script参数其他都是可选的
* bat、powershell

## 其他步骤
* error主动报错，终止当前pipeline。
* tool：使用预定工具
    * name：工具名称
    * type（可选）：工具类型
* timeout：
    * time：整型，超时时间
    * unit（可选）：时间单位，支持的值有NANOSECONDS、MICROSECONDS、MILLISECONDS、SECONDS、MINUTES（默认）、HOURS、DAYS
    * activity（可选）：布尔类型，如果为true，则只有当日志没有活动后，才算真正超时。
* waitUntil：等待条件满足。不断重复waitUntil块内的代码，知道条件为true，通常与timeout配合使用，避免死循环。
* retry：重复执行块。
* sleep：让pipeline休眠一段时间
    * time：整型，休眠时间。
    * unit（可选）：时间单位，支持的值有NANOSECONDS、MICROSECONDS、MILLISECONDS、SECONDS、MINUTES（默认）、HOURS、DAYS


## 环境变量
*环境变量分为内置环境变量、自定义环境变量*
* 内置环境变量。**通过一个env的全局变量暴露出来**
    * 三种方法：${env.BUILD_NUMBER}、$env.BUILD_NUMBER、${BUILD_NUMBER}
    * 注意：普通pipeline中git branch为origin/master,多分支中pipeline git branch变量为master。可以在阶段中使用sh "printenv"打印全局变量查看。
* 自定义变量。
    * environment。定义在pipeline中代表整个流程，定义到stage中代表该阶段有效。
    * 变量不可以跨pipeline
    * 如果自定义变量与env中重名，变量值会被覆盖掉。
    * 为了避免变量冲突，以下划线区分自定义变量。