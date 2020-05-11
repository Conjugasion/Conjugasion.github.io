---
title: maven的scope属性
tag: [Maven]
category: Maven
excerpt: scope：compile、provided、runtime
cover: /image18.jpg
---
### **maven scope**
compile、runtime和provided的区别，需要在执行mvn package命令，且打包格式是war之类（而不是默认的jar）的时候才能看出来。
通过compile和provided引入的jar包里面的类，你在项目中可以直接import进来用，编译没问题，但是runtime引入的jar包中的类，项目代码里不能直接用，用了无法通过编译，只能通过反射之类的方式来用。
通过compile和runtime引入的jar包，会出现在你的项目war包里，而provided引入的jar包则不会，provided表示该类由容器提供，比如servlet-api.jar由tomcat容器提供。
