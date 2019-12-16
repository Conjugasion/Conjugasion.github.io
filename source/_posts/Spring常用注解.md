---
title: Spring常用注解
tag: Spring
category: Spring
excerpt: 常用注解释义
cover: /image25.jpg
---
### 声明bean的注解
1. @Component 组件，没有明确的角色
@component和@configuration作为配置类时的区别
>@Configuration 注解的 bean 类都会被代理增强，因此调用该方法返回的都是**同一个实例**。
>@Configuration 注解本质上还是 @Component，因此 \<context:component-scan/\> 或者 @ComponentScan 都能处理@Configuration 注解的类。
```bash
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {
    String value() default "";
}
```
案例描述
@Bean 注解方法执行策略
```bash
@Configuration
public class MyBeanConfig {
 
    @Bean
    public Country country(){
        return new Country();
    }
 
    @Bean
    public UserInfo userInfo(){
        return new UserInfo(country());
    }
 
}
```
但是如果是变成@Component之后，此时返回的就不是一个实例了，每次都会创建一个实例
@Component 注解并没有通过 cglib 来代理@Bean 方法的调用
```bash
@Component
public class MyBeanConfig {
 
    @Bean
    public Country country(){
        return new Country();
    }
 
    @Bean
    public UserInfo userInfo(){
        return new UserInfo(country());
    }
 
}
```
修正版本：用@Autowired 注入,可以保证使用的同一个 Country 实例。
```bash
@Component
public class MyBeanConfig {
 
    @Autowired
    private Country country;
 
    @Bean
    public Country country(){
        return new Country();
    }
 
    @Bean
    public UserInfo userInfo(){
        return new UserInfo(country);
    }
 
}
```

2. @Service 在业务逻辑层使用（service层）

3. @Repository 在数据访问层使用（dao层）

4. @Controller 在展现层使用，控制器的声明（Controller）

### 注入bean的注解
1. @Autowired：由Spring提供
@Autowired注解是按照类型（byType）装配依赖对象，默认情况下它要求依赖对象必须存在，如果允许null值，可以设置它的required属性为false。如果我们想使用按照名称（byName）来装配，可以结合@Qualifier注解一起使用。
```bash
public class TestServiceImpl {
    @Autowired
    @Qualifier("userDao")
    private UserDao userDao; 
}
```
2. @Inject：由JSR-330提供

3. @Resource：由JSR-250提供
```bash
public class TestServiceImpl {
    // 下面两种@Resource只要使用一种即可
    @Resource(name="userDao", type="UserDao")
    private UserDao userDao; // 用于字段上
    
    @Resource(name="userDao", type="UserDao")
    public void setUserDao(UserDao userDao) { // 用于属性的setter方法上
        this.userDao = userDao;
    }
}
```
① 如果同时指定了name和type，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常。

② 如果指定了name，则从上下文中查找名称（id）匹配的bean进行装配，找不到则抛出异常。

③ 如果指定了type，则从上下文中找到类似匹配的唯一bean进行装配，找不到或是找到多个，都会抛出异常。

④ 如果既没有指定name，又没有指定type，则自动按照byName方式进行装配；如果没有匹配，则回退为一个原始类型进行匹配，如果匹配则自动装配。

### java配置类相关注解
1. @Configuration 声明当前类为配置类，相当于xml形式的Spring配置（类上）
>@Configuration用于定义配置类，可替换xml配置文件，被注解的类内部包含有一个或多个被@Bean注解的方法，这些方法将会被AnnotationConfigApplicationContext或AnnotationConfigWebApplicationContext类进行扫描，并用于构建bean定义，初始化Spring容器。
>注意：@Configuration注解的配置类有如下要求：
>>@Configuration不可以是final类型；
>>@Configuration不可以是匿名类；
>>嵌套的configuration必须是静态类。

**示例1**
@Configuration配置spring并启动spring容器
@Configuration标注在类上，相当于把该类作为spring的xml配置文件中的<beans>，作用为：配置spring容器(应用上下文)
```bash
package com.dxz.demo.configuration;

import org.springframework.context.annotation.Configuration;

@Configuration
public class TestConfiguration {
    public TestConfiguration() {
        System.out.println("TestConfiguration容器启动初始化。。。");
    }
}
```
相当于：
```bash
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context" xmlns:jdbc="http://www.springframework.org/schema/jdbc"  
    xmlns:jee="http://www.springframework.org/schema/jee" xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:util="http://www.springframework.org/schema/util" xmlns:task="http://www.springframework.org/schema/task" xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
        http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-4.0.xsd
        http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.0.xsd
        http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.0.xsd
        http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.0.xsd
        http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.0.xsd" default-lazy-init="false">

</beans>
```
主方法进行测试：
```bash
package com.dxz.demo.configuration;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class TestMain {
    public static void main(String[] args) {

        /* @Configuration注解的spring容器加载方式，用AnnotationConfigApplicationContext替换ClassPathXmlApplicationContext */
        ApplicationContext context = new AnnotationConfigApplicationContext(TestConfiguration.class);

        // 如果加载spring-context.xml文件：
        // ApplicationContext context = new
        // ClassPathXmlApplicationContext("spring-context.xml");
    }
}
```
结果：
![运行结果](/Spring常用注解/image1.jpg)

**示例2**
@Configuration启动容器+@Bean注册Bean，@Bean下管理bean的生命周期
@Bean标注在方法上(返回某个实例的方法)，等价于spring的xml配置文件中的<bean>，作用为：注册bean对象

bean类：
```bash
package com.dxz.demo.configuration;

public class TestBean {

    private String username;
    private String url;
    private String password;

    public void sayHello() {
        System.out.println("TestBean sayHello...");
    }

    public String toString() {
        return "username:" + this.username + ",url:" + this.url + ",password:" + this.password;
    }

    public void start() {
        System.out.println("TestBean 初始化。。。");
    }

    public void cleanUp() {
        System.out.println("TestBean 销毁。。。");
    }
}
```
配置类：
```bash
package com.dxz.demo.configuration;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Scope;

@Configuration
public class TestConfiguration {
    public TestConfiguration() {
        System.out.println("TestConfiguration容器启动初始化。。。");
    }

    /* @Bean注解注册bean,同时可以指定初始化和销毁方法 */
    /* @Bean(name="testBean",initMethod="start",destroyMethod="cleanUp") */
    @Bean
    @Scope("prototype")
    public TestBean testBean() {
        return new TestBean();
    }
}
```
主方法测试类：
```bash
package com.dxz.demo.configuration;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class TestMain {
    public static void main(String[] args) {

        /* @Configuration注解的spring容器加载方式，用AnnotationConfigApplicationContext替换ClassPathXmlApplicationContext */
        ApplicationContext context = new AnnotationConfigApplicationContext(TestConfiguration.class);

        /* 如果加载spring-context.xml文件：
        ApplicationContext context = new
        ClassPathXmlApplicationContext("spring-context.xml"); */
        
        /* 获取bean */
        TestBean tb = (TestBean) context.getBean("testBean");
        tb.sayHello();
    }
}
```
结果：
![运行结果](/Spring常用注解/image2.jpg)
注： 
(1)、@Bean注解在返回实例的方法上，如果未通过@Bean指定bean的名称，则默认与标注的方法名相同； 
(2)、@Bean注解默认作用域为单例singleton作用域，可通过@Scope(“prototype”)设置为原型作用域； 
(3)、既然@Bean的作用是注册bean对象，那么完全可以使用@Component、@Controller、@Service、@Ripository等注解注册bean，当然需要配置@ComponentScan注解进行自动扫描。

**示例3**
@Configuration启动容器+@Component注册Bean
bean类：
```bash
package com.dxz.demo.configuration;

import org.springframework.stereotype.Component;

//添加注册bean的注解
@Component
public class TestBean {

    private String username;
    private String url;
    private String password;

    public void sayHello() {
        System.out.println("TestBean sayHello...");
    }

    public String toString() {
        return "username:" + this.username + ",url:" + this.url + ",password:" + this.password;
    }

    public void start() {
        System.out.println("TestBean 初始化。。。");
    }

    public void cleanUp() {
        System.out.println("TestBean 销毁。。。");
    }
}
```
配置类：
```bash
package com.dxz.demo.configuration;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Scope;

@Configuration
//添加自动扫描注解，basePackages为TestBean包路径
@ComponentScan(basePackages = "com.dxz.demo.configuration")
public class TestConfiguration {
    public TestConfiguration() {
        System.out.println("TestConfiguration容器启动初始化。。。");
    }

    /* @Bean注解注册bean,同时可以指定初始化和销毁方法 */
    @Bean(name="testNean",initMethod="start",destroyMethod="cleanUp")
    @Bean
    @Scope("prototype")
    public TestBean testBean() {
        return new TestBean();
    }
}
```
主方法测试获取bean对象：
```bash
package com.dxz.demo.configuration;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class TestMain {
    public static void main(String[] args) {

        /* @Configuration注解的spring容器加载方式，用AnnotationConfigApplicationContext替换ClassPathXmlApplicationContext */
        ApplicationContext context = new AnnotationConfigApplicationContext(TestConfiguration.class);

        /* 如果加载spring-context.xml文件：
         ApplicationContext context = new
         ClassPathXmlApplicationContext("spring-context.xml");*/
        
        /* 获取bean */
        TestBean tb = (TestBean) context.getBean("testBean");
        tb.sayHello();
    }
}
```
sayHello()方法都被正常调用。
结果：
![运行结果](/Spring常用注解/image3.jpg)

2. @Bean 注解在方法上，声明当前方法的返回值为一个bean，替代xml中的方式（方法上）

3. @Configuration 声明当前类为配置类，其中内部组合了@Component注解，表明这个类是一个bean（类上）

4. @ComponentScan 用于对Component进行扫描，相当于xml中的（类上）

5. @WishlyConfiguration 为@Configuration与@ComponentScan的组合注解，可以替代这两个注解
