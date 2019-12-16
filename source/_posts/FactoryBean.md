---
title: Spring之FactoryBean类
tag: Spring
category: Spring
excerpt: 学习FactoryBean
cover: /image20.jpg
---
### **FactoryBean**
```bash
package org.springframework.beans.factory;

public interface FactoryBean<T> {
    T getObject() throws Exception;

    Class<?> getObjectType();

    boolean isSingleton();
}
```
`FactoryBean`接口主要用来创建一些不能直接new出来的对象，原理是实现FactoryBean接口，加入泛型，实现接口中的方法。通过getObject()方法来获得所需对象。


1.编写实体类(以JDBC中的Connection为例)，这里不需要编写。

2.编写自定义FactoryBean 实现FactoryBean接口，并且加入要创建对象的泛型,实现接口中的方法。

```bash
public class ConnFactoryBean implements FactoryBean<Connection>{
    private String driver;
    private String url;
    private String username;
    private String password;
	--此方法用来创建对象--
    @Override
    public Connection getObject() throws Exception {  
        Class.forName(driver);
        Connection con=DriverManager.getConnection(url, username, password);
        return con; 
    }
    @Override
    public Class<?> getObjectType() {
        // TODO Auto-generated method stub
        return null;
    }
	--返回值为false的话，说明对象是多例的，每次使用都会创建一个新的对象--
    @Override
    public boolean isSingleton() {    
        // TODO Auto-generated method stub
        return false;
    }
    public String getDriver() {
        return driver;
    }
    public void setDriver(String driver) {
        this.driver = driver;
    }
    public String getUrl() {
        return url;
    }
    public void setUrl(String url) {
        this.url = url;
    }
    public String getUsername() {
        return username;
    }
    public void setUsername(String username) {
        this.username = username;
    }
    public String getPassword() {
        return password;
    }
    public void setPassword(String password) {
        this.password = password;
    }
}
```
3.将编写的类在applicationContext.xml中注册。

```bash
<bean id="ConnFactoryBean" class="com.com.jd.Connection">
	<property name="driver" value="${jdbc.driver}"></property>
	<property name="url" value="${jdbc.url}"></property>
	<property name="username" value="${jdbc.username}"></property>
	<property name="password" value="${jdbc.password}"></property>
</bean>
```

4.在测试类中进行测试，获得Connection。

```bash
public static void main(String[] args) {
        
        ApplicationContext context = new ClassPathXmlApplicationContext("classpath:applicationContext.xml");
  
        Connection conn = (Connection)context.getBean("connFactoryBean");

        System.out.println(conn);

    }
```

