---
title: Sql聚合函数
tag: Sql
category: Sql
excerpt: 当sql聚合函数count、avg、sum遇到null或者空字符时
cover: /image16.jpg
---
### **创建测试表及数据**
```bash
create table `grouptest` (
  `id` varchar(20) default null,
  `value` varchar(10) default null
);

insert  into `grouptest`(`id`,`value`) 
values ('1','10'),('2',null),
       ('3','20'),('4','30'),
       (null,null),('','');

```
<div style="text-align:center">
<img src="/Sql聚合函数/image1.png">
</div>
<br />

倒数第二行两值均为null，最后一行两值均为空字符。
### **测试Sql**
```bash
select 'count(*)' 操作,count(*) 结果 from grouptest
union all
select 'count(value)',count(value) from grouptest
union all
select 'avg(value)',avg(value) from grouptest
union all
select 'sum(value)',sum(value) from grouptest
```
<div style="text-align:center">
<img src="/Sql聚合函数/image2.png">
</div>
<br />

### **结果分析**
**count：**
count(*)时，输出的是所有记录行数，包括列全为null的行。
count具体某个字段时，会统计空字符而不会统计null。

**sum：**
null和空字符不会对sum产生影响。

**avg：**
avg(value)可以视为sum(value)/count(value),也就是说对某个字段使用avg时，其处理空值和空字符时方式是和count一样的,即忽略null不忽略空字符。
