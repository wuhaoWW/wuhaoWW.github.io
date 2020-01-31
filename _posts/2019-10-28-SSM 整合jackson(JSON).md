---
layout:     post
author:     zjhChester
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - JSON
---

## SSM 整合jackson(JSON)

## 前言：

以前使用的jsonarray包需要依赖的包太多，分别是：

1、commons-beanutils-1.7.0.jar

2、commons-collections-3.1.jar

3、commons-lang-2.5.jar

4、commons-logging.jar

5、ezmorph-1.0.3.jar

6、json-lib-2.1-jdk15.jar

jackson可以很好地解决依赖的这个问题，使咱们使用json更简洁和容易

## SSM使用：

#### 1、导入maven坐标

```xml
<dependency>  <groupId>com.fasterxml.jackson.core</groupId>  <artifactId>jackson-databind</artifactId>  <version>2.9.8</version></dependency>
```

#### 2、使用：控制器加上@ResponseBody，并且返回类型两种情况：一种是实体类型，一种是实体集合，从而使得返回数据直接被封装成json格式

返回值使用实体类或实体集合，就可以直接转换成json字符串

```java
@ResponseBody@RequestMapping("/equalFaces")
public User equalFaces(String base64, HttpServletRequest request)
```



## 番外：

## spring4.0+内置tomcat 基于@EnableWebMvc注解的字符转换（这里使用alibaba的fastjson为例）

上述讲的jackson也可以用同样的方式去转换，

~~~xml
 <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.58</version>
        </dependency>
~~~



~~~java
import com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter;
public class MvcConf implements WebMvcConfigurer {
    @Override
    public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
//        字符转换  包括解决中文乱码
        FastJsonHttpMessageConverter fastJsonHttpMessageConverter = new FastJsonHttpMessageConverter();
        fastJsonHttpMessageConverter.setSupportedMediaTypes(MediaType.parseMediaTypes("text/html;charset=utf-8"));
        converters.add(fastJsonHttpMessageConverter);
    }
~~~

这里是演示如何用javaConfiguration去代替xml中的springmvc配置