---
title: Python连接Oracle
tag: [Python,Oracle]
category: Python
excerpt: 第三方库cx_Oracle，踩坑...
cover: /EVA1.jpg
---
### 安装模块
使用第三方模块 `cx_Oracle`

### 连接方式
``` bash
import cx_Oracle as cx
 
#第一种 账号 密码 ip：端口号/SID
conn = cx.connect('csbc', 'csbc', '10.18.2.89:1521/cbgl')

#第二种
conn = cx.connect('csbc/csbc@10.18.2.89:1521/cbgl')

#第三种
dsn = cx.makedsn('10.18.2.89', '1521', 'cbgl')

conn = cx.connect('csbc', 'csbc', dsn)
```
### 简单使用
``` 
import cx_Oracle as cx
 
con = cx.connect('csbc', 'csbc', '10.18.2.89:1521/cbgl')

cursor = con.cursor()       #创建游标

cursor.execute("select * from TDER where ID='28'")  #执行sql语句

sql = "replace into tdf_SKYEYE values(%s, %s, %s, %s)"    #SQL占位符
cursor.execute(sql, (变量1, 变量2, 变量3, 变量4))
conn.commit()     #新增、修改、删除操作需要commit()

data = cursor.fetchone()        #获取一条数据 获取所有数据fetchall()

print(data)     #打印数据

cursor.close()  #关闭游标

con.close()     #关闭数据库连接
```
### windows下使用注意
windows下需要安装Oracle客户端`Instant Client`，要选择适配Oracle版本的instantclient
``` 
select * from v$version
```
1) 下载链接：[instantclient](https://www.oracle.com/technetwork/cn/database/features/instant-client/index-092699-zhs.html)
2) 解压到一个目录，并添加到环境变量

<div style="text-align:center">
<img src="/Python-Oracle/image1.png">
</div>
<br />

<div style="text-align:center">
<img src="/Python-Oracle/image2.png">
</div>
<br />

3) 将instantclient文件夹下的`oci.dll`、`oraocci11.dll`、`oraociei11.dll`拷贝到`Python主目录\Lib\site-packages`

### 环境变量NLS_LANG
如果不设置`NLS_LANG`，可能会导致Python程序读取的中文数据变成？？？？
先看一下Oracle数据库当前的编码方式
```
select userenv('language') from dual
```
方法1：在系统环境变量设置NLS_LANG，值等于查询出来的结果，比如`SIMPLIFIED CHINESE_CHINA.UTF8`、`SIMPLIFIED CHINESE_CHINA.ZHS16GBK`
方法2: 在Python程序中设定
```
import os

os.environ['NLS_LANG'] = 'SIMPLIFIED CHINESE_CHINA.ZHS16GBK'
```