# SpringMvc第一节

## 1、概述

### 1.1、spring structs2的区别

![1569846420913](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569846420913.png)

## 2、入门搭建：

### 2.1、构建maven项目，骨架选择webapp打勾

#### 2.1.1、图解：

![1569924815965](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569924815965.png)

![1569924823626](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569924823626.png)

### 2.2、设置java源码目录和资源文件

#### 2.2.1、图解：

![1569924889186](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569924889186.png)

![1569924953836](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569924953836.png)

### 2.3、配置pom.xml 

#### 2.3.1、注意事项：

需要注意spring的版本号一致

#### 2.3.2、源码：

~~~xml
 		<!--spring框架核心-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
        <!--spring web核心-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
        <!--spring mvc 核心-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
~~~

### 2.4、编写spring-mvc.xml配置文件：

#### 2.4.1、图解：

![1569925605633](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569925605633.png)

#### 2.4.2、源码：

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd
    ">
    <!--告知spring在创建容器时要,扫描的包，配置所需要的标签，不是在beans的约束中，而是一个名称为context名称空间和约束中-->
    <!--开启注解扫描-->
    <context:component-scan base-package="cn.tfs"></context:component-scan>
    <!--配置视图解析器-->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--文件所在目录-->
        <property name="prefix" value="/WEB-INF/pages/"></property>
        <!--后缀名-->
        <property name="suffix" value=".jsp"></property>
    </bean>
    <!--开启spring-mvc框架注解支持-->
    <mvc:annotation-driven></mvc:annotation-driven>

</beans>
~~~

#### 2.4.3、注意事项：

![1569925739449](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569925739449.png)

### 2.5、编写web.xml配置文件

#### 2.5.1、图解：

![1569926084987](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569926084987.png)

#### 2.5.2、源码：

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
         http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--beans.xml配置文件的引入加载-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath*:beans.xml</param-value>
        </init-param>
        <!--服务器启动时创建servlet类 并加载beans.xml配置文件-->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
~~~

### 2.6、编写Controller.java测试

#### 2.6.1、源码

~~~java
package cn.tfs.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;
@Controller
public class HelloController {
    @GetMapping("/2")
    @ResponseBody
    public String index(){
        return "index";
    }
    @GetMapping("/1")
    public String success(){
        return "index";
    }
}

~~~

#### 2.6.2、图解

![1569926831065](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569926831065.png)

![1569926846593](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569926846593.png)

### 2.7、tomcat配置

#### 2.7.1、图解：

![1569926973000](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569926973000.png)

![1569927011204](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569927011204.png)

![1569927068646](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569927068646.png)

![1569927096162](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569927096162.png)

### 2.8、测试

2.8.1、http://localhost:8080/1

![1569927155050](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569927155050.png)

2.8.2、http://localhost:8080/2

![1569927196965](/mdImg/SpringMvc%E7%AC%AC%E4%B8%80%E8%8A%82.assets/1569927196965.png)