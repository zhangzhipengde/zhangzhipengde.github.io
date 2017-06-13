---
layout: post
title: 用Jekyll 和 github page搭建一个简单的博客
date: 2015-10-03 
tag: Linux
---

用Jekyll 和 github page搭建一个简单的博客 , 搭建好之后只需要本地写markdown文件，然后push到github，就成了博客
为了避免不必要的麻烦和我本身向学习Linux系统, 我是在window上搭建了<a href="" target="_black">Linux虚拟机</a>来进行搭建jekyll环境;

### 环境

>* <a href="" target="_black">Git 环境</a>
>* <a href="" target="_black">Ruby 环境</a>（Jekyll 是基于 Ruby 开发的）

### 创建github帐号和项目

登录github站点，如果你的用户名是123，那么你要创建一个名为123.github.io的项目

### Jekyll 环境配置

安装 jekyll

`gem install jekyll `

创建博客

`jekyll new myBlog `

进入博客目录

`cd myBlog `

启动本地服务

`jekyll serve`

`open http://127.0.0.1:4000/`

搞定

### 目录结构

一个基本的 Jekyll 网站的目录结构一般是像这样的：

```
├── _config.yml
├── _includes
|   ├── footer.html
|   └── header.html
├── _layouts
|   ├── default.html
|   ├── post.html
|   └── page.html
├── _posts
|   └── 2016-10-08-welcome-to-jekyll.md
├── _sass
|   ├── _base.scss
|   ├── _layout.scss
|   └── _syntax-highlighting.scss
├── about.md
├── css
|   └── main.scss
├── feed.xml
└── index.html
```
这些目录结构以及具体的作用可以参考 <a href="http://jekyll.com.cn/docs/structure/" target="_black">官网文档</a>

### 提交到github

```
git init
git add .
git commit -m "init blog"
git remote add origin xxxxxxx（拷贝github的地址）xxxxxxx.git
git push origin master
```

### 写文章

>* 文章必须新建在./_posts文件夹中
>* 文章名称必须是yyyy-mm-dd-xxxxx-xxx-xxx格式

一开始要这样写，下面的内容中，layout: post不能修改，其他的可自行修改

```
---
layout: post
title:  "使用 github + jekyll 搭建个人博客"
date:   2016-07-24 21:41:45 +0800
categories: share
---

```

待文章写完并查看效果没问题之后，即可push到github上，这样就OK了