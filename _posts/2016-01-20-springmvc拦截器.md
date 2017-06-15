---
layout: post
title: springmvc拦截器
date: 2016-01-20 
tag: Springmvc
---

### spring.xml文件里配置mvc拦截器

```
<!--拦截器 -->
     <mvc:interceptors>
           <!--多个拦截器,顺序执行 -->
           <!-- 登陆认证拦截器 -->
           <mvc:interceptor>
                <mvc:mapping path="/**" />  <!-- 如果不配置mapping则默认所有请求均被该拦截器拦截 -->
                <bean class="com.zhangzp.ssm.interceptor.LoginInterceptor"></bean>
           </mvc:interceptor>
           <mvc:interceptor>
                <!-- /**表示所有url包括子url路径 -->
                <mvc:mapping path="/**" />
                <bean class="com.zhangzp.ssm.interceptor.HandlerInterceptor1"></bean>
           </mvc:interceptor>
           <mvc:interceptor>
                <mvc:mapping path="/**" />
                <bean class="com.zhangzp.ssm.interceptor.HandlerInterceptor2"></bean>
           </mvc:interceptor>
     </mvc:interceptors>

```

### 登陆controller方法

```
@Controller
public class LoginController {

     // 登陆
     @RequestMapping("/login")
     public String login(HttpSession session, String username, String password)
                throws Exception {

           // 调用service进行用户身份验证
           // ...

           // 在session中保存用户身份信息
           session.setAttribute("username", username);
           // 重定向到商品列表页面
           return "redirect:/items/queryItems";
     }

     // 退出
     @RequestMapping("/logout")
     public String logout(HttpSession session) throws Exception {

           // 清除session
           session.invalidate();

           // 重定向到商品列表页面
           return "redirect:/items/queryItems";
     }

}

```

### 登陆认证拦截实现

```
public class LoginInterceptor implements HandlerInterceptor {


     //进入 Handler方法之前执行
     //用于身份认证、身份授权
     //比如身份认证，如果认证通过表示当前用户没有登陆，需要此方法拦截不再向下执行
     @Override
     public boolean preHandle(HttpServletRequest request,
                HttpServletResponse response, Object handler) throws Exception {

           //获取请求的url
           String url = request.getRequestURI();
           //判断url是否是公开 地址（实际使用时将公开 地址配置配置文件中）
           //这里公开地址是登陆提交的地址
           if(url.indexOf("login")>=0){
                //如果进行登陆提交，放行
                return true;
           }

           //判断session
           HttpSession session  = request.getSession();
           //从session中取出用户身份信息
           String username = (String) session.getAttribute("username");

           if(username != null){
                //身份存在，放行
                return true;
           }

           //执行这里表示用户身份需要认证，跳转登陆页面
          request.getRequestDispatcher("/WEB-INF/jsp/login.jsp").forward(request, response);

           //return false表示拦截，不向下执行
           //return true表示放行
           return false;
     }

     //进入Handler方法之后，返回modelAndView之前执行
     //应用场景从modelAndView出发：将公用的模型数据(比如菜单导航)在这里传到视图，也可以在这里统一指定视图
     @Override
     public void postHandle(HttpServletRequest request,
                HttpServletResponse response, Object handler,
                ModelAndView modelAndView) throws Exception {

          System.out.println("HandlerInterceptor1...postHandle");

     }

     //执行Handler完成执行此方法
     //应用场景：统一异常处理，统一日志处理
     @Override
     public void afterCompletion(HttpServletRequest request,
                HttpServletResponse response, Object handler, Exception ex)
                throws Exception {

          System.out.println("HandlerInterceptor1...afterCompletion");
     }

}

```

### 页面

```
当前用户：${username }，
<c:if test="${username!=null }">
 <a href="${pageContext.request.contextPath }/logout">退出</a>
</c:if>

```

```
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>系统登陆</title>
</head>
<body>
<form action="${pageContext.request.contextPath }/login" method="post">
用户账号：<input type="text" name="username" /><br/>
用户密码 ：<input type="password" name="password" /><br/>
<input type="submit" value="登陆"/>
</form>
</body>
</html>

```