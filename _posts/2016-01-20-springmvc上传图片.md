---
layout: post
title: springmvc上传图片
date: 2016-01-20 
tag: Springmvc
---

### springmvc中对多部件类型解析
在 页面form中提交**enctype="multipart/form-data**的数据时，需要springmvc对multipart类型的数据进行解析。

```
<form id="itemForm" action="${pageContext.request.contextPath }/items/editItemsSubmit" method="post"  enctype="multipart/form-data">
```

### 在springmvc.xml中配置multipart类型解析器

```
     <!-- 文件上传 -->
     <bean id="multipartResolver"
          class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
           <!-- 设置上传文件的最大尺寸为5MB -->
           <property name="maxUploadSize">
                <value>5242880</value>
           </property>
     </bean>

```

### 加入上传图片的jar

```
 <!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
           <dependency>
                <groupId>commons-fileupload</groupId>
                <artifactId>commons-fileupload</artifactId>
                <version>1.2.2</version>
           </dependency>
           <!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
           <dependency>
                <groupId>commons-io</groupId>
                <artifactId>commons-io</artifactId>
                <version>2.4</version>
           </dependency>

```

### 创建图片虚拟 目录 存储图片
直接修改tomcat的配置： 在conf/server.xml文件，添加虚拟 目录 ：

```
<Context docBase="D:\workspace\upload\temp" path="/pic" reloadable="false" />
```

### 上传图片代码

**页面**

```
<tr>
    <td>商品图片</td>
    <td>
        <c:if test="${itemsCustom.pic !=null}">
            <img src="/pic/${itemsCustom.pic}" width=100 height=100/>
            <br/>
        </c:if>
        <input type="file"  name="items_pic"/>
    </td>
</tr>

```

**controller方法**

```
   @RequestMapping("/editItemsSubmit")
    public String editItemsSubmit(HttpServletRequest request, Integer id,
            Model model,
            @Validated(value = {ValidGroup1.class }) ItemsCustom itemsCustom,
            BindingResult bindingResult,
            MultipartFile items_pic)//接收商品图片)
            throws Exception {

                    ...
      //原始名称
        String originalFilename = items_pic.getOriginalFilename();
        //上传图片
        if(items_pic!=null && originalFilename!=null && originalFilename.length()>0){

            //存储图片的物理路径
            String pic_path = "D:\\workspace\\upload\\temp\\";


            //新的图片名称
            String newFileName = UUID.randomUUID() + originalFilename.substring(originalFilename.lastIndexOf("."));
            //新图片
            File newFile = new File(pic_path+newFileName);

            //将内存中的数据写入磁盘
            items_pic.transferTo(newFile);

            //将新图片名称写到itemsCustom中
            itemsCustom.setPic(newFileName);

        }

          ...

}

```