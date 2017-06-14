---
layout: post
title: spring整合web项目原理
date: 2015-10-04 
tag: Spring
---

### 需加载Spring的核心配置文件
加载配置文件，需要创建加载配置文件的类对象，效率较低

```
 //加载配置文件
ApplicationContext ctx = new ClassPathXmlApplicationContext("bean.xml");
```

### 解决加载效率低的实现思想

把加载配置文件和创建对象过程，在服务器启动时完成

### 实现原理

技术的应用

```
ServletContext对象<br />
监听器
```

具体实现

```

    1.在服务器启动时，为每个项目创建一个ServletContext对象
    2.使用监听器，监听ServletContext对象的创建
    3.监听器监听到ServletContext创建时候，加载Spring核心配置文件
    4.把创建出来的对象存放到SetvletContext域对象里面(setAttribute方法)
    5.需要获取对象时，到ServletContext域得到(getAttribute方法)
```

实现过程

```
    1. 解决仅加载一次配置文件
    2. Spring已经封装了监听器
    3. 需要导入Spring整合web项目的jar包，才能配置监听器(spring-web-4.2.4.RELEASE.jar)
```

web.xml中需要配置监听器
```
<!-- 配置监听器 -->
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<!-- 指定加载Spring核心配置文件的位置 -->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:bean.xml</param-value>
</context-param>
```


