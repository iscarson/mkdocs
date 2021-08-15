

###  整型（int）

* 在32位机器上，整数的位数为32位，取值范围为-2**31～2**31-1，即-2147483648～2147483647
* 在64位系统上，整数的位数为64位，取值范围为-2**63～2**63-1，即-9223372036854775808～9223372036854775807

> bit_length 当前整数的二进制表示，最少位数

```
age = 18
print(age.bit_length())
```

> to_bytes 获取当前数据的字节表示

```
age = 18
v1 = age.to_bytes(10,byteorder='big')
v2 = age.to_bytes(10,byteorder='little')
print(v1)
print(v2)
```

###  布尔值 （bool）

* 真或假 
* 1 或 0
* False or True
