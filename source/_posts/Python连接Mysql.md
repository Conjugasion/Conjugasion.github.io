---
title: Python连接Mysql
tag: [Python,Mysql]
category: Python
excerpt: 第三方库pymysql使用教程
cover: /image1.jpg
---
### 连接方式
```
conn = pymysql.connect(host="10.18.2.160", port=14000, user="root", password="csTC-8888", database="djl", charset="utf8")

cursor = conn.cursor(cursor=pymysql.cursors.DictCursor)

data = cursor.fetchall()        #获取一条数据 获取所有数据fetchall()

conn.commit()     #新增、修改、删除操作需要commit()

print(data)     #打印数据

cursor.close()  #关闭游标

con.close()     #关闭数据库连接
```
### SQL占位符
```
sql = "replace into tdf_SKYEYE values(%s, %s, %s, %s)"
curs.execute(sql, (变量1, 变量2, 变量3, 变量4))
conn.commit()     #新增、修改、删除操作需要commit()
```