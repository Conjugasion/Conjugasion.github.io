---
title: JD项目结构
tag: [JD]
category: JD
excerpt: 各模块功能
cover: /image20.jpg
---
### **api**
关键包：controller、jsf(jsf接口的具体实现类)、service(又分为接口定义和具体impl)、util
jsf包下的xxxxServiceImpl类注入service包下的抽象接口，使用其对应实现类的方法完成具体功能实现。

### **backend**
jmq消费相关

### **client**
关键包：constants(业务返回码的枚举类)、jsf(定义jsf接口和相应业务方法)、request、response

### **common**
关键包：constants(常量的定义类、业务返回码的枚举类)、util

### **dao**
关键包：model(xxxx实体类)、mapper(xxxxMapper)