---
layout: post
title: springmvc输入校验
date: 2016-01-20 
tag: Springmvc
---

springmvc使用hibernate的校验框架validation(和hibernate没有任何关系)。

### 环境准备

hibernate的校验框架validation所需要jar包：

```
 <!-- springmvc使用hibernate的校验框架 -->
           <dependency>
                <groupId>org.hibernate</groupId>
                <artifactId>hibernate-validator</artifactId>
                <version>4.3.1.Final</version>
           </dependency>

           <dependency>
                <groupId>org.jboss.logging</groupId>
                <artifactId>jboss-logging</artifactId>
                <version>3.1.0.CR2</version>
           </dependency>
           <!-- https://mvnrepository.com/artifact/javax.validation/validation-api -->
           <dependency>
                <groupId>javax.validation</groupId>
                <artifactId>validation-api</artifactId>
                <version>1.0.0.GA</version>
           </dependency>

```

### 配置校验器

```
     <!-- 校验器 -->
     <bean id="validator"
          class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
           <!-- hibernate校验器 -->
           <property name="providerClass" value="org.hibernate.validator.HibernateValidator" />
           <!-- 指定校验使用的资源文件，在文件中配置校验错误信息，如果不指定则默认使用classpath下的ValidationMessages.properties -->
           <property name="validationMessageSource" ref="messageSource" />
     </bean>
     <!-- 校验错误信息配置文件 -->
     <bean id="messageSource"
          class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
           <!-- 资源文件名 -->
           <property name="basenames">
                <list>
                     <value>classpath:CustomValidationMessages</value>
                </list>
           </property>
           <!-- 资源文件编码格式 -->
           <property name="fileEncodings" value="utf-8" />
           <!-- 对资源文件内容缓存时间，单位秒 -->
           <property name="cacheSeconds" value="120" />
     </bean>

```

### 校验器注入到处理器适配器中

```
     <!-- 使用 mvc:annotation-driven代替上边注解映射器和注解适配器配置 mvc:annotation-driven默认加载很多的参数绑定方法，
           比如json转换解析器就默认加载了，如果使用mvc:annotation-driven不用配置上边的RequestMappingHandlerMapping和RequestMappingHandlerAdapter
           实际开发时使用mvc:annotation-driven -->
     <mvc:annotation-driven conversion-service="conversionService"
           validator="validator"></mvc:annotation-driven>

```

### 在pojo中添加校验规则

```
public class Items {
    private Integer id;

    //校验名称在1到30字符中间
    //message是提示校验出错显示的信息
    @Size(min=1,max=30,message="{items.name.length.error}")
    private String name;

    private Float price;

    private String pic;
    //非空校验
    @NotNull(message="{items.createtime.isNUll}")
    private Date createtime;

```

### 配置CustomValidationMessages.properties

```
#\u6dfb\u52a0\u6821\u9a8c\u9519\u8bef\u63d0\u4ea4\u4fe1\u606f
items.name.length.error=请输入1到30的数字
items.createtime.isNUll=生产日期不能为空

```

### 捕获校验错误信息

```
// 商品信息修改提交
    // 在需要校验的pojo前边添加@Validated，在需要校验的pojo后边添加BindingResult
    // bindingResult接收校验出错信息
    // 注意：@Validated和BindingResult bindingResult是配对出现，并且形参顺序是固定的（一前一后）。
    @RequestMapping("/editItemsSubmit")
    public String editItemsSubmit(HttpServletRequest request, Integer id,Model model,
            @Validated ItemsCustom itemsCustom,BindingResult bindingResult) throws Exception {

```

### 在页面显示校验错误信息

```
// 获取校验错误信息
        if (bindingResult.hasErrors()) {
            // 输出错误信息
            List<ObjectError> allErrors = bindingResult.getAllErrors();

            for (ObjectError objectError : allErrors) {
                // 输出错误信息
                System.out.println(objectError.getDefaultMessage());

            }
            // 将错误信息传到页面
            model.addAttribute("allErrors", allErrors);

            // 可以直接使用model将提交pojo回显到页面
            model.addAttribute("items", itemsCustom);

            // 出错重新到商品修改页面
            return "items/editItems";
        }


-----------------------页面信息---------------------------------
<!-- 显示错误信息 -->
<c:if test="${allErrors!=null }">
    <c:forEach items="${allErrors }" var="error">
    ${ error.defaultMessage}<br/>
</c:forEach>
</c:if>

```

### 校验分组
定义多个校验分组（其实是一个java接口），分组中定义有哪些规则. 每个controller方法使用不同的校验分组

```
public interface ValidGroup1 {
     //接口中不需要定义任何方法，仅是对不同的校验规则进行分组
     //此分组只校验商品名称长度

}

```

### 在校验规则中添加分组

```
    //校验名称在1到30字符中间
    //message是提示校验出错显示的信息
    //groups：此校验属于哪个分组，groups可以定义多个分组
    @Size(min=1,max=30,message="{items.name.length.error}",groups={ValidGroup1.class})
    private String name;

```

### 在controller方法使用指定分组的校验

```
   // 商品信息修改提交
    // 在需要校验的pojo前边添加@Validated，在需要校验的pojo后边添加BindingResult
    // bindingResult接收校验出错信息
    // 注意：@Validated和BindingResult bindingResult是配对出现，并且形参顺序是固定的（一前一后）。
    // value={ValidGroup1.class}指定使用ValidGroup1分组的 校验
    @RequestMapping("/editItemsSubmit")
    public String editItemsSubmit(HttpServletRequest request, Integer id, Model model,
            @Validated(value = {ValidGroup1.class }) ItemsCustom itemsCustom, BindingResult bindingResult)
            throws Exception {}

```