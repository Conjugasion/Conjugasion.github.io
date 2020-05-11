---
title: Mysql中int(n)和varchar(n)含义
tag: [Sql]
category: Sql
excerpt: int类型和varchar类型
cover: /image12.jpg
---
<div style="text-align:center">
<img src="/int(n)和varchar(n)/image4.png">
</div>
<br />

<div style="text-align:center">
<img src="/int(n)和varchar(n)/image5.png">
</div>

### **int(n)**
● 不管n是多少，int(n)是长度为32位二进制整数，在没有勾选“无符号”的情况下，能表示的整数范围是`-2^31-1`到`2^31-1(2147483647)`。在勾选“无符号”的情况下，整数范围是`0`到`2^32-1(4294967295)`；
● n的定义与存储空间没有任何关系，底层存储还是4个字节。
● int(n)主要体现在勾选“填充零”之后的区别。若指定数据类型为int(4)，存储`18`(十进制)，查询数据库会显示`0018`(十进制)；若存储了18888，查询数据库依然显示18888，原样存储；
● 如果没有给它指定显式长度n，MySQL会为它指定一个默认值11，即int的最大十进制长度。

<div style="text-align:center">
<img src="/int(n)和varchar(n)/image1.png">
</div>

### **varchar(n)**
● MySQL表 **所有列占用空间总和<=65535字节**
● varchar能占用的最大空间为65535字节，最大可存储汉字数 = `(65535-1-2)/3 = 21844`，减1是因为实际存储从第2个字节开始，减2是因为要在列表长度存储实际字符长度，除3是因为采用utf8编码；
如果当前表又设置了另一int字段，int字段占用4字节，所以此时varchar字段最大能设置的长度为`(65535-1-2-4)/3 = 21842`
● MySQL4.0版本以下，varchar(20)，指的是20`字节`，如果存放UTF8汉字时，只能存6个(每个汉字3字节)，最大可以设定varchar(65535)；
● MySQL5.0版本以上，varchar(20)，指的是20`字符`，无论存放的是数字、字母还是UTF8汉字(每个汉字3字节)，最大可以设定varchar(21845)；

### **Mysql表主键长度约束**
varchar格式的主键长度不超过767字节，所以在"utf8"格式下，主键长度最多767/3=255，在"utf8mb4"格式下，主键长度最多767/4=191；
int格式的主键长度表示的是"显示长度"，不代表占用空间大小，所以理论上长度没限制，但是Mysql规定最多显示长度255。

<div style="text-align:center">
<img src="/int(n)和varchar(n)/image2.png">
</div>
<br />

<div style="text-align:center">
<img src="/int(n)和varchar(n)/image3.png">
</div>
