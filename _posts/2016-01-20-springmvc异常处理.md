---
layout: post
title: springmvc异常处理
date: 2016-01-20 
tag: Springmvc
---

springmvc提供全局异常处理器（一个系统只有一个异常处理器）进行统一异常处理。

### 首先自定义异常类

```
public class CustomException extends Exception {

     //异常信息
     public String message;

     public CustomException(String message){
           super(message);
           this.message = message;
     }
     public String getMessage() {
           return message;
     }

     public void setMessage(String message) {
           this.message = message;
     }


}

```

### 全局异常处理器 

思路：系统遇到异常，在程序中手动抛出，dao抛给service、service给controller、controller抛给前端控制器，前端控制器调用全局异常处理器。
 全局异常处理器处理思路：<br/>
  > * 解析出异常类型<br/>
  > * 如果该 异常类型是系统 自定义的异常，直接取出异常信息，在错误页面展示<br/>
  > * 如果该 异常类型不是系统 自定义的异常，构造一个自定义的异常类型（信息为“未知错误”）

```
//springmvc提供一个HandlerExceptionResolver接口
public class CustomExceptionResolver implements HandlerExceptionResolver {

     /**
      * （非 Javadoc）
      * <p>Title: resolveException</p>
      * <p>Description: </p>
      * @param request
      * @param response
      * @param handler
      * @param ex 系统 抛出的异常
      * @return
      * @see org.springframework.web.servlet.HandlerExceptionResolver#resolveException(javax.servlet.http.HttpServletRequest, javax.servlet.http.HttpServletResponse, java.lang.Object, java.lang.Exception)
      */
     @Override
     public ModelAndView resolveException(HttpServletRequest request,
                HttpServletResponse response, Object handler, Exception ex) {
           //handler就是处理器适配器要执行Handler对象（只有method）

           CustomException customException = null;
           if(ex instanceof CustomException){
                customException = (CustomException)ex;
           }else{
                customException = new CustomException("未知错误");
           }

           //错误信息
           String message = customException.getMessage();


           ModelAndView modelAndView = new ModelAndView();

           //将错误信息传到页面
           modelAndView.addObject("message", message);

           //指向错误页面
           modelAndView.setViewName("error");


           return modelAndView;
     }

}

```

### 错误页面

```
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>错误提示</title>
</head>
<body>
${message }
</body>
</html>

```

### 在springmvc.xml配置全局异常处理器

```
<!-- 全局异常处理器 只要实现HandlerExceptionResolver接口就是全局异常处理器 -->
     <bean class="com.zhangzp.ssm.exception.CustomExceptionResolver"></bean>

```

### 异常测试

```
        Items items = itemsMapper.selectByPrimaryKey(id);
        if(items==null){
            throw new CustomException("修改的商品信息不存在!");
        }

```