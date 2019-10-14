---
layout:     post
author:     zjhChester
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - springmvc
---

# 2019-10-12-spring-mvc解决静态文件映射的问题

在springmvc的配置文件spring-mvc.xml中加入静态请求拦截并转发

~~~xml
 <!--静态资源访问-->
    <mvc:default-servlet-handler/>
    <!--把符合/js/**匹配规则的请求,映射到目录/WEB-INF/js/下-->
    <mvc:resources mapping="/js/**" location="WEB-INF/js/"/>
    <mvc:resources mapping="/img/**" location="WEB-INF/img/"/>
    <mvc:resources mapping="/css/**" location="WEB-INF/css/"/>
~~~





