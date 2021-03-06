
>  BCMSN（Build  cisco  multi  switch  network）构建思科多层交换网络

## 1. 模拟器介绍
* I：PT （支持部分交换实验、使用直观。缺点：IOS命令行特性支持有限）
* Ii：dynamics （小凡）
* Iii：GNS3 （也支持部分交换实验，通过在路由器上加交换模块来实现，命令行、特性支持有限）
* Iv：cisco IOU ：cisco  IOS  over  unix/linux  思科iOS在unix/liux之上，对特性和命令行支持良好。缺点：操作复杂。


> 关闭路由器的路由功能：PC2(config)#no ip routing
> 如何给交换机配置管理ip地址：（config）#interface  vlan  1
>                              （config-if）#ip  address  ip地址   子网掩码
>                               （config-if）#no  shutdown
> 如何给交换机配置默认网关：sw(config)#ip default-gateway  网关地址

## 2. 交换机基本介绍
交换机工作在osi的第二个层次数据链路层，通过MAC地址对数据进行转发，转发数据单位：数据帧。

## 3. MAC地址
MAC地址由48bit构成的，6字节，位于网卡的rom芯片里边。通常用十六进制数表示。
| 24 bit | 24 bit |
|:------:|:------:|
前24bit代表生产厂商，表明该块网卡是由哪一个生产厂商所生产的；
后24bit代表产品的编号，每一个厂商生产的每一块网卡都有一个唯一的序列标识。
因此48bit的mac地址具有全球唯一性，是不可以更改的。

## 4. 以太网数据帧的帧结构


前导符：一字节长度的固定8个bit，主要用于区分数据帧的起始和结束。
DA：目标地址，代表数据帧要去往的目的地；
SA：源地址，代表数据帧的来源；
Length/type：长度，代表数据帧的大小。类型：代表上层所使用的协议；
DATA：真正要传递的内容；
FCS：主要进行差错检测；

## 5. 交换机的功能
I：保证同一网段设备进行通信；
ii：隔离冲突域；（广播域）
iii：学习功能；（其实就是mac地址表的形成过程，学习数据帧中的源mac地址）
iv：转发；

## 6. 交换机的工作原理
交换机mac地址表的形成：通过记录接收到数据帧的源mac地址和对应端口的映射关系。
I：首先学习数据帧中源mac地址形成mac地址表；
Ii：根据数据帧中的目标mac地址对数据帧进行转发；
Iii：当交换机收到一个未知目标mac地址，则将该数据泛洪至除源端口以外的所有端口；
Iv：当交换机收到一个广播帧或者组播帧，将该数泛洪至除源端口以外的所有端口；
交换机mac地址表的老化时间：默认300S，可以进行修改。

## 7. 交换机的三种内部转发方式？（了解）
I：直通转发  转发效率高，但是安全性不够
Ii：存储转发  转发效率低，但是安全性高
Iii：碎片转发  特点：收到64字节后立刻转发，可以有效的避免冲突。

## 8. cisco网络部署的层次模型（cisco 三层）
I：核心层    负责高速转发
ii：分布层   主要用于部署策略
iii：接入层   为用户提供网络接入

## 9. 常见的交换机类型？
1、2900系列  2950、2960  （主要用于部署接入层）
2、3000系列  3550、3560、3750  （主要用于分布层）
3、4000系列  4503、4506、4507
6、6000系列  6506、6509
7、7000系列  7609、7613
8、GSR12000系列  全球路由交换
