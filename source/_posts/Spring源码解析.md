---
title: Spring源码解析
tag: [Spring]
category: Spring
excerpt: 源码分析
cover: /image15.jpg
---
### **总体流程**
<div style="text-align:center">
<img src="/Spring源码解析/image1.jpg">
</div>
<br />

**核心代码--入口**
```
public ClassPathXmlApplicationContext(String[] configLocations, boolean refresh, ApplicationContext parent) throws BeansException {
	super(parent);
	setConfigLocations(configLocations);   # 读取xml形式的bean文件
	if (refresh) {
		refresh();    # 核心函数！
	}
}
```
**核心代码--refresh()方法**
```
public void refresh() throws BeansException, IllegalStateException {
synchronized (this.startupShutdownMonitor) {
	// 一些准备操作，设置参数
	prepareRefresh();

	// 重点方法！创建DefaultListableBeanFactory形式的BeanFactory，然后读取BeanDefinition，加入BeanFactory的BeanDefinitionMap
	ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

	// 对BeanFactory做一些设置操作
	prepareBeanFactory(beanFactory);

	try {
		// 钩子方法，留给子类实现
		postProcessBeanFactory(beanFactory);

		// 执行BeanFactoryPostProcessor，操作对象是尚未实例化的beandefinition对象！
		invokeBeanFactoryPostProcessors(beanFactory);

		// 提前注册BeanPostProcessor
		registerBeanPostProcessors(beanFactory);

		// 国际化
		initMessageSource();

		// 初始化广播器
		initApplicationEventMulticaster();

		// 钩子方法，留给子类实现，比如SpringBoot集成Tomcat
		onRefresh();

		// 注册监听器
		registerListeners();

		// 重点方法！实例化BeanDefinition对象，并解决循环依赖问题；执行BeanPostProcessor方法
		finishBeanFactoryInitialization(beanFactory);

		// 发布相应事件
		finishRefresh();
	}

	catch (BeansException ex) {
		if (logger.isWarnEnabled()) {
			logger.warn("Exception encountered during context initialization - " +
					"cancelling refresh attempt: " + ex);
		}

		// Destroy already created singletons to avoid dangling resources.
		destroyBeans();

		// Reset 'active' flag.
		cancelRefresh(ex);

		// Propagate exception to caller.
		throw ex;
	}

	finally {
		// Reset common introspection caches in Spring's core, since we
		// might not ever need metadata for singleton beans anymore...
		resetCommonCaches();
	}
}
}
```
### **解决循环依赖**
1.引入三级缓存：
一级缓存singletonObjects，二级缓存earlySingletonObjects，三级缓存singletonFactories
```
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object>(256);

private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<String, ObjectFactory<?>>(16);

private final Map<String, Object> earlySingletonObjects = new HashMap<String, Object>(16);
```

2.要加入二级缓存的条件必须满足：一级缓存中没命中；对象正在创建状态

3.核心思路：将Bean对象的实例化和初始化分开
将实例化完成但未初始化完成的对象提前暴露出去，使得外部可以拿到当前对象的引用

4.如果只有一级缓存，能否解决循环依赖问题？
可能获取到本成品的Bean对象

5.如果只有一级和二级缓存，能否解决循环依赖问题？
一级缓存存放成品Bean，二级缓存存放半成品Bean，能解决循环依赖问题

6.添加三级缓存的意义何在？
```
protected Object getEarlyBeanReference(String beanName, RootBeanDefinition mbd, Object bean) {
	Object exposedObject = bean;
	if (bean != null && !mbd.isSynthetic() && hasInstantiationAwareBeanPostProcessors()) {
		for (BeanPostProcessor bp : getBeanPostProcessors()) {
			if (bp instanceof SmartInstantiationAwareBeanPostProcessor) {
				SmartInstantiationAwareBeanPostProcessor ibp = (SmartInstantiationAwareBeanPostProcessor) bp;
				exposedObject = ibp.getEarlyBeanReference(exposedObject, beanName);
				if (exposedObject == null) {
					return null;
				}
			}
		}
	}
	return exposedObject;
}
```
如果创建普通类，一级和二级缓存足矣，但是如果要创建一个代理类对象，会出现问题。
getEarlyBeanReference()->wrapIfNecessary()，由于刚开始创建的是实例化好的对象，然后再进行代理类的创建工作，若没有三级缓存，用户可能前后获取到的对象不一致，一开始获取到未代理的对象，后来又获取到代理对象。三级缓存使用ObjectFactory生成一个统一的对象。
