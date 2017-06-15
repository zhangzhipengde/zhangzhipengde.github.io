---
layout: post
title: springmvc进行json交互
date: 2016-01-20 
tag: Springmvc
---

### 为什么要进行json数据交互

json数据格式在接口调用中、html页面中较常用，json格式比较简单，解析还比较方便。<br/>
比如：webservice接口，传输json数据.

### springmvc进行json交互

1. 请求json、输出json，要求请求的是json串，所以在前端页面中需要将请求的内容转成json，不太方便。

2. 请求key/value、输出json。此方法比较常用。


### 环境准备

**加载json转的jar包**

springmvc中使用jackson的包进行json转换（@requestBody和@responseBody使用下边的包进行json转），如下：

```
<!-- https://mvnrepository.com/artifact/org.codehaus.jackson/jackson-core-asl -->
           <dependency>
                <groupId>org.codehaus.jackson</groupId>
                <artifactId>jackson-core-asl</artifactId>
                <version>1.9.11</version>
           </dependency>
           <!-- https://mvnrepository.com/artifact/org.codehaus.jackson/jackson-mapper-asl -->
           <dependency>
                <groupId>org.codehaus.jackson</groupId>
                <artifactId>jackson-mapper-asl</artifactId>
                <version>1.9.11</version>
           </dependency>

```

### 配置json转换器

在注解适配器中加入messageConverters

```
<!--注解适配器 -->
      <bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
            <property name="messageConverters">
            <list>
            <bean class="org.springframework.http.converter.json.MappingJacksonHttpMessageConverter"></bean>
            </list>
            </property>
      </bean>

```

**注意：如果使用<mvc:annotation-driven /> 则不用定义上边的内容。**

### json交互测试

**输入json串，输出是json串**

jsp页面

```
//请求json，输出是json
function requestJson(){

    $.ajax({
        type:'post',
        url:'${pageContext.request.contextPath }/requestJson.action',
        contentType:'application/json;charset=utf-8',
        //数据格式是json串，商品信息
        data:'{"name":"手机","price":999,"detail":"1"}',
        success:function(data){//返回json结果
            console.log(data);
        }

    });


}

```

controller

```
 //请求json串(商品信息)，输出json(商品信息)
     //@RequestBody将请求的商品信息的json串转成itemsCustom对象
     //@ResponseBody将itemsCustom转成json输出
     @RequestMapping("/requestJson")
     public @ResponseBody ItemsCustom requestJson(@RequestBody ItemsCustom itemsCustom){

           //@ResponseBody将itemsCustom转成json输出
           return itemsCustom;
     }
```

测试结果
![](/images/posts/springmvc/input.png)

### 输入key/value，输出是json串

jsp页面

```
//请求key/value，输出是json
function responseJson(){

    $.ajax({
        type:'post',
        url:'${pageContext.request.contextPath }/responseJson.action',
        //请求是key/value这里不需要指定contentType，因为默认就 是key/value类型
        //contentType:'application/json;charset=utf-8',
        //数据格式是json串，商品信息
        data:'name=手机&price=999',
        success:function(data){//返回json结果
            console.log(data);
        }

    });

}

```

controller

```
     //请求key/value，输出json
     @RequestMapping("/responseJson")
     public @ResponseBody ItemsCustom responseJson(ItemsCustom itemsCustom){

           //@ResponseBody将itemsCustom转成json输出
           return itemsCustom;
     }

```


测试结果
![](/images/posts/springmvc/out.png)

引入js不好使时添加


```
     <!-- 静态资源处理 -->
     <mvc:default-servlet-handler/>
```

