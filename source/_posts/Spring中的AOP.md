---
title: Spring中的AOP
tag: [Spring]
category: Spring
excerpt: 切面、通知，目标方法
cover: /image5.jpg
---
### Spring AOP里的名词概念
切面类：就是要执行的增强的方法所在的类，比如例子里的MyTransaction类

通知：切面类里的方法，比如例子的beginTransaction( )方法和commit( )

目标方法：要执行的目标方法，比如例子里的savePerson( )方法

### 代码使用Spring AOP
```bash
package com.cj.study.spring.aop;
 
public interface PersonService {
	
	public String savePerson();
	
	public void updatePerson();
	
	public void deletePerson();
	
}
```
```bash
package com.cj.study.spring.aop;
 
public class PersonServiceImpl implements PersonService{
 
	@Override
	public String savePerson() {
		System.out.println("添加");
		return "保存成功！";
	}
 
	@Override
	public void updatePerson() {
		System.out.println("修改");
	}
 
	@Override
	public void deletePerson() {
		System.out.println("删除");
	}
 
}
```
```bash
package com.cj.study.spring.aop;
 
//切面类
public class MyTransaction {
	//切面里的通知方法
	public void beginTransaction(){
		System.out.println("开启事务 ");
	}
	//切面里的通知方法
	public void commit(){
		System.out.println("提交事务");
	}
}
```
```bash
package com.cj.study.spring.aop;
 
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
 
public class AopTest {
	@Test
	public void test(){
		ApplicationContext context = new ClassPathXmlApplicationContext("com/cj/study/spring/aop/applicationContext.xml");
		PersonService proxyPersonService = (PersonService) context.getBean("personService");
		String returnValue = proxyPersonService.savePerson();
		System.out.println(returnValue);
	}
}
```
xml配置
```bash
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:aop="http://www.springframework.org/schema/aop" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-2.5.xsd">
 
	<!-- 导入目标类 -->   
	<bean id="personService" class="com.cj.study.spring.aop.PersonServiceImpl"></bean>
	<!-- 导入切面 -->
	<bean id="myTransaction" class="com.cj.study.spring.aop.MyTransaction"></bean>
	
	<aop:config>
		<aop:pointcut expression="execution(* com.cj.study.spring.aop.*.*(..))" id="perform"/>
		<aop:aspect ref="myTransaction">
			<aop:before method="beginTransaction" pointcut-ref="perform"/>
		</aop:aspect>
		<aop:aspect ref="myTransaction">
			<aop:after method="commit" pointcut-ref="perform"/>
		</aop:aspect>
	</aop:config>
	
</beans>
```
可以断点看一下
![运行过程](/Spring中的AOP/image1.jpg)
发现返回的对象是$Proxy4，说明返回的是代理类的对象
最后的运行结果
![运行结果](/Spring中的AOP/image2.jpg)

### AOP配置文件
![运行结果](/Spring中的AOP/image3.jpg)
这是上述例子里的配置文件
	1、导入目标类和切面类，就是把目标类和切面类放入Spring容器里，让Spring帮你生成

	2、aop:pointcut 是指切入点

	3、expression 是指切入点表达式

	4、aop:aspect 是指上边讲的切面类

	5、aop:before、aop:after 是指上边讲的通知，通知有很多种，前置通知、后置通知、环绕通知、最终通知、异常通知，等下会详细讲

### 切入点表达式execution
```bash
*：匹配任何数量字符；
..：匹配任何数量字符的重复，如在类型模式中匹配任何数量子包；而在方法参数模式中匹配任何数量参数。
+：匹配指定类型的子类型；仅能作为后缀放在类型模式后边。
java.lang.String 匹配String类型；
java.*.String 匹配java包下的任何“一级子包”下的String类型；
如匹配java.lang.String，但不匹配java.lang.ss.String
java..* 匹配java包及任何子包下的任何类型;
如匹配java.lang.String、java.lang.annotation.Annotation
java.lang.*ing 匹配任何java.lang包下的以ing结尾的类型；
java.lang.Number+ 匹配java.lang包下的任何Number的自类型；
如匹配java.lang.Integer，也匹配java.math.BigInteger
```
举例说明:
```bash
任意公共方法的执行：
execution(public * *(..))
任何一个以“set”开始的方法的执行：
execution(* set*(..))
AccountService 接口的任意方法的执行：
execution(* com.xyz.service.AccountService.*(..))
定义在service包里的任意方法的执行：
execution(* com.xyz.service.*.*(..))
定义在service包和所有子包里的任意类的任意方法的执行：
execution(* com.xyz.service..*.*(..))
定义在pointcutexp包和所有子包里的JoinPointObjP2类的任意方法的执行：
execution(* com.test.spring.aop.pointcutexp..JoinPointObjP2.*(..))")
***> 最靠近(..)的为方法名,靠近.*(..))的为类名或者接口名,如上例的JoinPointObjP2.*(..))
 
pointcutexp包里的任意类.
within(com.test.spring.aop.pointcutexp.*)
pointcutexp包和所有子包里的任意类.
within(com.test.spring.aop.pointcutexp..*)
实现了Intf接口的所有类,如果Intf不是接口,限定Intf单个类.
this(com.test.spring.aop.pointcutexp.Intf)
***> 当一个实现了接口的类被AOP的时候,用getBean方法必须cast为接口类型,不能为该类的类型.
 
带有@Transactional标注的所有类的任意方法.
@within(org.springframework.transaction.annotation.Transactional)
@target(org.springframework.transaction.annotation.Transactional)
带有@Transactional标注的任意方法.
@annotation(org.springframework.transaction.annotation.Transactional)
***> @within和@target针对类的注解,@annotation是针对方法的注解
 
参数带有@Transactional标注的方法.
@args(org.springframework.transaction.annotation.Transactional)
参数为String类型(运行是决定)的方法.
```

### AOP中的各种通知
   1、前置通知
   ```bash
   在目标方法执行之前执行
   无论目标方法是否抛出异常，都执行，因为在执行前置通知的时候，目标方法还没有执行，还没有遇到异常
   ```
   2、后置通知
   ```bash
   在目标方法执行之后执行
   当目标方法遇到异常，后置通知将不再执行
   后置通知可以接受目标方法的返回值，但是必须注意：后置通知的参数的名称和配置文件中returning="var"的值是一致的
   ```
   3、最终通知
   ```bash
   在目标方法执行之后执行
   无论目标方法是否抛出异常，都执行，因为相当于finally
   ```
   4、异常通知
   ```bash
   接受目标方法抛出的异常信息
   步骤:
   在异常通知方法中有一个参数Throwable ex
   在配置文件中写<aop:after-throwing method="throwingMethod" pointcut-ref="perform" throwing="ex"/>
   ```
   5、环绕通知
   ```bash
   如果不在环绕通知中调用ProceedingJoinPoint的proceed，目标方法不会执行
   环绕通知可以控制目标方法的执行
   ```


### Spring AOP的具体加载步骤
   1、当spring容器启动的时候，加载了spring的配置文件
   2、为配置文件中所有的bean创建对象
   3、spring容器在创建对象的时候它会解析aop:config的配置 
       	    解析切入点表达式，用切入点表达式和纳入spring容器中的bean做匹配
            如果匹配成功，则会为该bean创建代理对象，代理对象的方法=目标方法+通知
            如果匹配不成功，则为该bean创建正常的对象
	 其实就是你通过表达式告诉Spring哪些bean需要它帮你生成代理对象而不是生成原有的正常对象
   4、在客户端利用context.getBean获取对象时，如果该对象有代理对象则返回代理对象，如果没有代理对象，则返回目标对象
说明：如果目标类没有实现接口，spring容器会采用cglib的方式产生代理对象，如果实现了接口，则会采用jdk的动态代理产生代理对象
