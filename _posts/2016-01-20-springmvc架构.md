---
layout: post
title: springmvc架构
date: 2016-01-20 
tag: Springmvc
---

### 架构图

![](/images/posts/springmvc/jiagou.png)

### 架构流程

第一步：发起请求到前端控制器(DispatcherServlet) <br/>
第二步：前端控制器请求处理器映射器 HandlerMapping查找 Handler
                可以根据xml配置、注解进行查找<br/>
第三步：处理器映射器HandlerMapping向前端控制器返回Handler<br/>
第四步：前端控制器调用处理器适配器去执行Handler<br/>
第五步：处理器适配器去执行Handler<br/>
第六步：Handler执行完成给适配器返回ModelAndView<br/>
第七步：处理器适配器向前端控制器返回ModelAndView<br/>
                ModelAndView是springmvc框架的一个底层对象，包括 Model和view<br/>
第八步：前端控制器请求视图解析器去进行视图解析<br/>
                根据逻辑视图名解析成真正的视图(jsp)<br/>
第九步：视图解析器向前端控制器返回View<br/>
第十步：前端控制器进行视图渲染<br/>
                视图渲染将模型数据(在ModelAndView对象中)填充到request域<br/>
第十一步：前端控制器向用户响应结果<br/>

### 架构流程

- 用户发送请求至前端控制器DispatcherServlet
- DispatcherServlet收到请求调用HandlerMapping处理器映射器。
- 处理器映射器根据请求url找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。
- DispatcherServlet通过HandlerAdapter处理器适配器调用处理器
- 执行处理器(Controller，也叫后端控制器)。
- Controller执行完成返回ModelAndView
- HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet
- DispatcherServlet将ModelAndView传给ViewReslover视图解析器
- ViewReslover解析后返回具体View
- DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）。
- DispatcherServlet响应用户

### 组件说明

以下组件通常使用框架提供实现：

* **DispatcherServlet**：前端控制器

用户请求到达前端控制器，它就相当于mvc模式中的c，dispatcherServlet是整个流程控制的中心，由它调用其它组件处理用户的请求，dispatcherServlet的存在降低了组件之间的耦合性。

* **HandlerMapping**：处理器映射器

HandlerMapping负责根据用户请求找到Handler即处理器，springmvc提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等。

* **Handler**：处理器 (需要程序员开发)

Handler 是继DispatcherServlet前端控制器的后端控制器，在DispatcherServlet的控制下Handler对具体的用户请求进行处理。
<font color="red">由于Handler涉及到具体的用户业务请求，所以一般情况需要程序员根据业务需求开发Handler。</ font>

* **HandlAdapter**：处理器适配器

通过HandlerAdapter对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。

* **View Resolver**：视图解析器

View Resolver负责将处理结果生成View视图，View Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。 springmvc框架提供了很多的View视图类型，包括：jstlView、freemarkerView、pdfView等。
<font color="red">一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，需要由程序员根据业务需求开发具体的页面。</font>


