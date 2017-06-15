---
layout: post
title: springmvc自定义日期
date: 2016-01-20 
tag: Springmvc
---

### 自定义转换类

```
public class CustomDateConverter implements Converter<String,Date>{

     @Override
     public Date convert(String source) {

           //实现 将日期串转成日期类型(格式是yyyy-MM-dd HH:mm:ss)

           SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

           try {
                //转成直接返回
                return simpleDateFormat.parse(source);
           } catch (ParseException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
           }
           //如果参数绑定失败返回null
           return null;
     }

}

```

###  配置方式

```
     <!-- 自定义参数绑定 -->
     <bean id="conversionService"
          class="org.springframework.format.support.FormattingConversionServiceFactoryBean">
           <!-- 转换器 -->
           <property name="converters">
                <list>
                     <!-- 日期类型转换 -->
                     <bean class="com.zhangzp.ssm.controller.converter.CustomDateConverter" />
                </list>
           </property>
     </bean>

```

###  第二种方式

```
//the parameter was converted in initBinder
@RequestMapping("/date")
public String date(Date date){
    System.out.println(date);
    return "hello";
}


//At the time of initialization,convert the type "String" to type "date"
@InitBinder
public void initBinder(ServletRequestDataBinder binder){
    binder.registerCustomEditor(Date.class, new CustomDateEditor(new SimpleDateFormat("yyyy-MM-dd"),
            true));
}

```