---
layout: post
title: springmvc入门程序
date: 2016-01-20 
tag: Springmvc
---

### web.xml 配置springmvc前端控制器

```
<!-- springmvc前端控制器 -->
     <servlet>
           <servlet-name>springmvc</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <!-- contextConfigLocation配置springmvc加载的配置文件（配置处理器映射器、适配器等等） 如果不配置contextConfigLocation，默认加载的是/WEB-INF/servlet名称-serlvet.xml（springmvc-servlet.xml） -->
           <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>classpath:springmvc.xml</param-value>
           </init-param>
           <!-- load-on-startup：表示启动容器时初始化该Servlet； -->
           <load-on-startup>1</load-on-startup>
     </servlet>

     <servlet-mapping>
           <servlet-name>springmvc</servlet-name>
           <!--第一种：*.action，访问以.action结尾 由DispatcherServlet进行解析
                第二种：/，所以访问的地址都由DispatcherServlet进行解析，对于静态文件的解析需要配置不让DispatcherServlet进行解析  使用此种方式可以实现 RESTful风格的url
                第三种：/*，这样配置不对，使用这种配置，最终要转发到一个jsp页面时， 仍然会由DispatcherServlet解析jsp地址，  不能根据jsp页面找到handler，会报错。
           -->
           <url-pattern>*.action</url-pattern>
     </servlet-mapping>

```

### springmvc.xml 配置 handler 处理器映射器  处理器适配器   视图解析器等;

**首先配置handler  一般都是基于注解配置**

```
<!-- 配置Handler -->
     <bean id="itemsController1" name="/queryItems_test.action"
           class="com.zhangzp.ssm.controller.ItemsController1" />
 <!-- 配置另外一个Handler -->
     <bean id="itemsController2" class="com.zhangzp.ssm.controller.ItemsController2" />

     <!-- 对于注解的Handler可以单个配置
           实际开发中建议使用组件扫描
      -->
     <!-- <bean class="com.zhangzp.ssm.controller.ItemsController3" /> -->
     <!-- 可以扫描controller、service、...
             这里让扫描controller，指定controller的包
      -->
     <context:component-scan base-package="com.zhangzp.ssm.controller"></context:component-scan>

```

### 配置处理器映射器和处理器适配器 

```
  <!--非注解的两种配置方法 一般是不用的 都用注解的 -->
     <!-- 配置处理器映射器2种方法 -->

     <!-- 处理器映射器 将bean的name作为url进行查找 ，需要在配置Handler时指定beanname（就是url） 所有的映射器都实现
           HandlerMapping接口。 -->
     <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping" />
     <!--简单url映射  -->
     <bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
           <property name="mappings">
                <props>
                     <!-- 对itemsController1进行url映射，url是/queryItems1.action -->
                     <prop key="/queryItems1.action">itemsController1</prop>
                     <prop key="/queryItems2.action">itemsController1</prop>
                     <prop key="/queryItems3.action">itemsController2</prop>
                </props>
           </property>
     </bean>
     <!--  -->


     <!-- 配置处理器适配器2种方法 -->

     <!-- 处理器适配器 所有处理器适配器都实现 Controller接口 -->
     <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter" />
     <!-- 另一个非注解的适配器  要求编写的Handler实现 HttpRequestHandler接口。-->
     <bean class="org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter"/>
     <!--  -->

```

```
<!--注解的两种配置方法 -->
     <!--注解映射器 -->
     <!-- <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerMapping"/> -->
     <!--注解适配器 -->
     <!-- <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter"/> -->

     <!-- 使用 mvc:annotation-driven代替上边注解映射器和注解适配器配置
           mvc:annotation-driven默认加载很多的参数绑定方法，
     比如json转换解析器就默认加载了，如果使用mvc:annotation-driven不用配置上边的RequestMappingHandlerMapping和RequestMappingHandlerAdapter
     实际开发时使用mvc:annotation-driven
      -->
     <mvc:annotation-driven></mvc:annotation-driven>

```

### 配置视图解析器

```
<!-- 视图解析器 解析jsp解析，默认使用jstl标签，classpath下的得有jstl的包 -->
           <bean
          class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <!-- 配置jsp路径的前缀 -->
           <property name="prefix" value="/WEB-INF/jsp/"/>
           <!-- 配置jsp路径的后缀 -->
           <property name="suffix" value=".jsp"/>
     </bean>

```

### 实现

**ItemsController1.Java** 
`实现controller接口的处理器 当处理器适配器用SimpleControllerHandlerAdapter 配置是用这个 但是一个类只能写一个方法`

**ItemsController2.Java**

`实现HttpRequestHandler接口的处理器 当处理器适配器用HttpRequestHandlerAdapter 配置是用这个 但是一个类只能写一个方法`

**ItemsController3.Java**

`注解开发Handler`