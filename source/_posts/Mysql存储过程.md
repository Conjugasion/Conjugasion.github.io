---
title: Mysql的存储过程
tag: [Mysql,存储过程]
category: Mysql
excerpt: 存储过程入门
cover: /image11.jpg
---
### **定义**
`存储过程`(Stored Procedure)是在大型数据库系统中，一组为了完成特定功能的SQL语句集，存储在数据库中，经过第一次编译后调用不需要再次编译，用户通过指定存储过程的名字并给出参数(如果该存储过程带有参数)来执行它。存储过程是数据库中的一个重要对象。

### **存储过程的特点**
1、能完成较复杂的判断和运算
2、可编程行强，灵活
3、SQL编程的代码可重复使用
4、执行的速度相对快一些
5、减少网络之间的数据传输，节省开销   

### **创建存储过程的语法**

```bash
create procedure 名称()
begin
.........
end;
```
**示例**

```bash
create procedure test1()
begin
    select * from users;
    select * from orders;
end;
```
**调用存储过程**

```bash
call test1();
```
### **存储过程的变量**
**变量声明和赋值**

```bash
create procedure test2()
begin
  -- 使用 declare语句声明一个变量
  declare userName varchar(32) default '';
  -- 使用set语句给变量赋值
  set userName='xiaoxiao';
  -- 将users表中id=1的名称赋值给userName
  select name into userName from users where id=1;
  -- 返回变量
  select userName;
end;
```
**总结**

(1) **变量一定要声明在begin-end块内的最前面，不然会报错！**
(2) 变量的声明使用declare,一句declare只声明一个变量，变量必须先声明后使用;
(3) 变量具有数据类型和长度，与mysql的SQL数据类型保持一致，因此甚至还能设定默认值、字符集和排序规则;
(4) 变量可以通过set来赋值，也可以通过select into的方式赋值;
(5) 变量需要返回，可以使用select语句，如：select 变量名。

### **变量作用域**
**说明**
(1) 存储过程中变量是有作用域的，作用范围在begin和end块之间，end结束变量的作用范围即结束。
(2) 需要多个块之间传值，可以使用全局变量，即放在所有代码块之前
(3) 传参变量是全局的，可以在多个块之间起作用

**示例**

```bash
create procedure test3()
begin
  begin
    declare userscount int default 0; -- 用户表中的数量
    declare ordercount int default 0; -- 订单表中的数量
    select count(*) into userscount from users;
    select count(*) into ordercount from orders;
    select userscount,ordercount; -- 返回用户表中的数量、订单表中的数量
  end;
  begin 
    declare maxmoney int default 0; -- 最大金额
    declare minmoney int default 0; -- 最小金额
    select max(money) into maxmoney from orders;
    select min(money) into minmoney from orders;
    select maxmoney,minmoney; -- 返回最金额、最小金额
   end;
end;
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image1.png">
</div>
<br />

<div style="text-align:center">
<img src="/Mysql_stored_procedure/image2.png">
</div>
<br />
若test3()改为如下：

```bash
create procedure test3()
    begin
      begin
        declare userscount int default 0; -- 用户表中的数量
        declare ordercount int default 0; -- 订单表中的数量
        select count(*) into userscount from users;
        select count(*) into ordercount from orders;
        select userscount,ordercount; -- 返回用户表中的数量、订单表中的数量
      end;
      begin 
        declare maxmoney int default 0; -- 最大金额
        declare minmoney int default 0; -- 最小金额
        select max(money) into maxmoney from orders;
        select min(money) into minmoney from orders;
        select userscount,ordercount，maxmoney,minmoney; -- 返回最金额、最小金额
       end;
    end;
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image3.png">
</div>
<br />
将userscount,ordercount改为全局变量，再次验证：

```bash
create procedure test3()
    begin

        declare userscount int default 0; -- 用户表中的数量
        declare ordercount int default 0; -- 订单表中的数量
        begin
            select count(*) into userscount from users;
            select count(*) into ordercount from orders;
            select userscount,ordercount; -- 返回用户表中的数量、订单表中的数量
      end;
      begin 
        declare maxmoney int default 0; -- 最大金额
        declare minmoney int default 0; -- 最小金额
        select max(money) into maxmoney from orders;
        select min(money) into minmoney from orders;
        select userscount,ordercount，maxmoney,minmoney; -- 返回最金额、最小金额
       end;
    end;
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image4.png">
</div>
<br />

<div style="text-align:center">
<img src="/Mysql_stored_procedure/image5.png">
</div>

**存储过程中变量的作用域，作用范围在begin和end块之间，end结束变量的作用范围即结束**
### **存储过程参数**
存储过程的参数类型有：`IN`,`OUT`,`INOUT`
**基本语法**

```bash
create procedure 名称([IN|OUT|INOUT] 参数名 参数数据类型)
begin
.........
end
```
**传入参数IN**
● 传入参数IN必须在调用存储过程事指定，如果不显示指定为in,那么默认就是in类型。
● in类型参数一般只用于传入，在调用过程中一般不作为修改和返回；
● 如果调用存储过程中需要修改和返回值，可以使用OUT类型参数。
**示例**

```bash
 create procedure test4(userId int)
    begin
            declare userName varchar(32) default '';
            declare ordercount int default 0;
            select name into userName from users where id=userId;
            select userName;
    end;
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image6.png">
</div>

**传出参数OUT**

```bash
调用存储过程时，传入userId返回该用户的name
        create procedure test5(in userId int,out userName varchar(32))
        begin
            select name into userName from users where id=userId;
        end;
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image8.png">
</div>

**总结**
● 传出参数OUT在调用存储过程中，可以改变其值，并可返回；
● out是传出参数，不能用于传入参数值；
● 调用存储过程时，out参数也需要指定，但必须是变量，不能是常量；
● 如果既需要传入，同时又需要传出，则可以使用INOUT类型参数。

**可变参数INOUT**

```bash
调用存储过程时，传入参数userId，userName既是传入参数，也是传出参数。
create procedure test6(inout userId int,inout userName varchar(32))
begin
    set userId=2;
    set userName='';
    select id,name into userId,userName from users where id=userId;
end;
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image9.png">
</div>

**总结**
● 可变变量INOUT调用时可传入值，在调用过程中，可修改其值，同时也可返回值；
● INOUT参数集合了IN和OUT类型的参数功能；
● INOUT调用时传入的是变量，而不是常量。
### **存储过程条件语句**
**基本语法**

```bash
if(条件) then
  ......
elseif(条件) then
  ......
else
  ......
end if;
```

**示例1**
编写存储过程，如果用户userId是偶数则返回userName,否则返回userId

```bash
create procedure test7(in userId int)
begin
   declare userName varchar(32) default '';
   if(userId%2=0)
   then 
      select name into userName from users where id=userId;
      select userName;
   else
      select userId;
      end if;
end;
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image10.png">
</div>
<br />

<div style="text-align:center">
<img src="/Mysql_stored_procedure/image11.png">
</div>

**示例2**
根据用户传入的uid参数判断,如果用户状态status为1，则给用户score加10分；如果用户状态status为2，则给用户score加20分；其他情况加30分

```bash
create procedure test8(in userid int)
begin
   declare my_status int default 0;
   select status into my_status from users where id=userid;
   if(my_status=1)
   then 
       update users set score=score+10 where id=userid;
    elseif(my_status=2)
    then 
       update users set score=score+20 where id=userid;
    else 
       update users set score=score+30 where id=userid;
    end if;
end;
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image12.png">
</div>
<br />

<div style="text-align:center">
<img src="/Mysql_stored_procedure/image13.png">
</div>

### **流程控制**
**case分支**

```
基本语法结构
case ...
	when ... then....
	when.... then....
	else ... 
end case;
```
**示例**
users表中，根据userid获取status值，如果status为1，则修改score为10；如果status为2，则修改为20，如果status3，则修改为30；否则修改为40。

```
 create procedure testcate(userid int)
    begin 
        declare my_status int default 0;
        select status into my_status from users where id=userid;
 
        case my_status
            when 1 then update users set score=10 where id=userid;
            when 2 then update users set score=20 where id=userid;
            when 3 then update users set score=30 where id=userid;
            else update users set score=40 where id=userid;
        end case;
    end;
```

<div style="text-align:center">
<img src="/Mysql_stored_procedure/image19.png">
</div>



### **存储过程循环语句**
**while语句**

```bash
基本结构
while(表达式) do 
   ......  
end while;
```
**示例**
使用循环语句，向表test1(id)中插入10条连续的记录

```bash
create procedure test9()
begin
  declare i int default 0;
  while(i<10) do 
    begin 
        select i;
        set i=i+1;
        insert into test1(id) values(i);
     end;
  end while;
end;
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image14.png">
</div>
<br />

<div style="text-align:center">
<img src="/Mysql_stored_procedure/image15.png">
</div>

**repeat语句**

```bash
基本结构
repeat
  ......
until ...... end repeat;
```
**示例**
给test1表中的id字段插入数据，从1到10

```bash
create procedure test10()
begin
    declare i int default 0;
    repeat 
    begin 
        select i;
        set i=i+1;
        insert into test1(id) values(i);
    end;
    until i>=10 -- 如果i>=10,则跳出循环
    end repeat;
end;
```
### **存储过程游标的使用**
**游标是保存查询结果的临时区域**
**示例**
编写存储过程，使用游标，把users表中id为偶数的记录逐一更新用户名

```bash
create procedure test11()
    begin
        declare stopflag int default 0;
        declare username VARCHAR(32);
        -- 创建一个游标变量，格式: declare 变量名 cursor ......
        declare username_cur cursor for select name from users where id%2=0;
        -- 游标是保存查询结果的临时区域
        -- 游标变量username_cur保存了查询的临时结果，实际上就是结果集
        -- 当游标变量中保存的结果都查询一遍(遍历),到达结尾，将变量stopflag设置为1，用于循环中判断是否结束
        declare continue handler for not found set stopflag=1;
 
        open username_cur; -- 打开游标
        fetch username_cur into username; -- 游标向前走一步，取出一条记录放到变量username中
        while(stopflag=0) do -- 如果游标还没有结尾，就继续
            begin 
                -- 在用户名前门拼接 '_cur' 字符串
                update users set name=CONCAT(username,'_cur') where name=username;
                fetch username_cur into username;
            end;
        end while; -- 结束循环
        close username_cur; -- 关闭游标
    end;
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image16.png">
</div>

### **自定义函数**
函数与存储过程最大的区别是函数必须有返回值，否则会报错
**示例1**

```bash
create function getusername(userid int) returns varchar(32)
    reads sql data  -- 从数据库中读取数据，但不修改数据
    begin
        declare username varchar(32) default '';
        select name into username from users where id=userid;
        return username;
    end;
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image17.png">
</div>

**示例2**
根据userid，获取accoutid,id,name组合成UUID作为用户的唯一标识

```bash
  create function getuuid(userid int) returns varchar(64)
    reads sql data  -- 从数据库中读取数据，但不修改数据
    begin
        declare uuid varchar(64) default '';
        select concat(accontid,'_',id,'_',name) into uuid from users where id=userid;
        return uuid;
    end;
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image18.png">
</div>

**总结**
● 创建函数使用create function 函数名(参数) returns 返回类型；
● 函数体放在begin和end之间；
● returns指定函数的返回值；
● 函数调用使用select getusername()。

### **触发器**
触发器是一种对象，它能根据对表的操作触发一些相应的动作，这些动作可以是insert,update,delete等操作。
**示例1**
出于审计目的，当有人往表users插入一条记录时，把插入的userid、username、插入动作和操作时间记录下来。

```bash
create trigger tr_users_insert after insert on users
    for each row 
    begin 
        insert into oplog(userid,username,action,optime)
        values(NEW.id,NEW.name,'insert',now());
    end;
```
创建成功后，给uses表中插入一条记录：

```bash
insert into users(id,name,age,status,score,accontid) values(6,'小周',23,1,'60','10001')；
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image20.png">
</div>

**示例2**
出于审计目的，当删除users表时，记录删除前该记录的主要字段值

```bash
create trigger tr_users_delete before delete on users
    for each row 
    begin 
        insert into oplog(userid,username,action,optime)
        values(OLD.id,OLD.name,'delete',now());
    end;
```
删除users表中的一条记录

```bash
delete from users where id=6;
```
<div style="text-align:center">
<img src="/Mysql_stored_procedure/image21.png">
</div>

**总结**
● 创建触发器使用create trigger 触发器名 ......
● 什么时候触发？after/before insert on users,是在对表操作之前(before)或者之后(after)触发动作的。
● 对什么操作事件触发？ after insert on users,操作事件包括insert,update,delete等修改操作；
● 对什么表触发? after insert on users
● 影响的范围？for each row

### **存储过程+event(事件)**
**event的创建格式**

```bash
create event[IF NOT EXISTS]event_name -- 创建使用create event
    ON SCHEDULE schedule -- on schedule 什么时候来执行
    [ON COMPLETION [NOT] PRESERVE] -- 调度计划执行完成后是否还保留
    [ENABLE | DISABLE] -- 是否开启事件，默认开启
    [COMMENT 'comment'] -- 事件的注释
    DO sql_statement; -- 这个调度计划要做什么？
```
**执行时间说明**
● 单次计划任务示例
        在2019年2月1日4点执行一次
        on schedule at    '2019-02-01 04:00:00'

● 重复计划执行
        on schedule every 1 second 每秒执行一次
        on schedule every 1 minute 每分钟执行一次
        on schedule every 1 day 没天执行一次

● 指定时间范围的重复计划任务
        每天在20:00:00执行一次
        on schedule every 1 day starts '2019-02-01 20:00:00'

**示例**
使用存储过程+事件事件一个简单的实现福彩3D开奖，每3分钟开奖一次。
第一步：先编写一个存储过程open_lottery,产生3个随机数，生成一条开奖记录。
第二步：编写一个时间调度器，每3分钟调用一次这个过程。
```bash
create procedure open_lottery()
        begin 
            insert into lottery(num1,num2,num3,ctime)
            select FLOOR(rand()*9)+1,FLOOR(rand()*9)+1,FLOOR(rand()*9)+1,now();
        end;
```
```bash
create event if not exists lottery_event -- 创建一个事件
        on schedule every  3 minute  -- on schedule 什么时候来执行，没三分钟执行一次
        on completion preserve 
        do call open_lottery;
```

<div style="text-align:center">
<img src="/Mysql_stored_procedure/image22.png">
</div>

**如果event之一没有运行，请按照以下办法解决**：
● show variables like '%event_scheduler%'; set global event_scheduler=on;
● alert event lottery_event enable;