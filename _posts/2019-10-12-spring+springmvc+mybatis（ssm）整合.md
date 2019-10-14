# spring+springmvc+mybatis（ssm）整合

## 1.前期准备

1.构建maven项目，勾选骨架构建，勾选web-app

![1570857284829](/mdImg/spring+springmvc+mybatis%EF%BC%88ssm%EF%BC%89%E6%95%B4%E5%90%88.assets/1570857284829.png)

2.快速构建项目在maven引入的地方加上一组键值对：archetypeCatalog     internal

![1570857482777](/mdImg/spring+springmvc+mybatis%EF%BC%88ssm%EF%BC%89%E6%95%B4%E5%90%88.assets/1570857482777.png)

​	3.构建pom坐标依赖

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
        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.6</version> 
        </dependency>
        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.11</version>
        </dependency>
 <!-- https://mvnrepository.com/artifact/c3p0/c3p0 -->
        <dependency>
            <groupId>c3p0</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.1.2</version>
        </dependency>
~~~

## 2.spring spring-mvc mybatis 配置文件构建

### 2.1.项目结构

![1570858617777](/mdImg/spring+springmvc+mybatis%EF%BC%88ssm%EF%BC%89%E6%95%B4%E5%90%88.assets/1570858617777.png)

### 2.2.spring

1.名称空间：

~~~xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
~~~

2.注解扫描：

需要注意的是咱们整合三大框架，如果需要用spring的注解开发，咱们应该规避mvc框架中的Controller注解，所以咱们在xml配置文件中应该加入对应的不扫描的配置，如下

applicationContext.xml

~~~xml
 <!--开启注解扫描-->
        <context:component-scan base-package="cn.tfs">
            <!--不扫描的包-->
            <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"></context:exclude-filter>
        </context:component-scan>
~~~

### 2.3.spring-mvc

1.注解扫描：

与上面一样springmvc需要扫描的注解只包括@Controller的注解，所以我们在扫描中添加过滤器

spring-mvc.xml

~~~xml
<!--扫描注解-->
    <context:component-scan base-package="cn.tfs">
        <!--配置spring-mvc需要扫描的注解-->
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"></context:include-filter>
    </context:component-scan>
~~~

2.视图解析器：

~~~xml
<!--jsp视图解析器-->
    <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass" value="org.springframework.web.servlet.view.InternalResourceView"/>
        <property name="prefix" value="/WEB-INF/pages/"></property>
        <property name="suffix" value=".jsp"></property>
        <property name="contentType" value="text/html;charset=UTF-8"/>
        <property name="order" value="0" />
    </bean>
~~~

3.注解支持:

因为有时候我们需要用@Response注解去给请求返回字符串内容，那么有可能会遇到中文的问题，所以这里我们在开启注解支持的时候加入对应的编码转化的bean

~~~xml
 <mvc:annotation-driven>
        <mvc:message-converters register-defaults="true">
            <!--配置@Response注解编码问题-->
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <property name="supportedMediaTypes" value="text/html;charset=utf-8"></property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
~~~

4.静态资源访问映射：

~~~xml
<mvc:default-servlet-handler/>
    <!--把符合/js/**匹配规则的请求,映射到目录/WEB-INF/js/下-->
    <mvc:resources mapping="/js/**" location="WEB-INF/js/"/>
    <mvc:resources mapping="/img/**" location="WEB-INF/img/"/>
    <mvc:resources mapping="/css/**" location="WEB-INF/css/"/>
~~~

5.web.xml配置：

web-app的名称空间

~~~xml
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0"
~~~



**注意：如果有报红，咱们就把过滤器丢在控制器前面**

另外我们还需要解决前端传输过来的数据中文乱码的问题---->过滤器

~~~xml
<!--过滤器-->
  <filter>
    <filter-name>characterEncodingFilter</filter-name>
    <!--字符编码过滤器   解决中文乱码的问题-->
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
~~~

以及web.xml的前端控制器

~~~xml
 <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <!--读取配置文件读取-->
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath*:spring-mvc.xml</param-value>
    </init-param>
    <!--在服务器开启的时候装载-->
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
~~~



### 2.4.mybatis

使用了properties导入参数的方法，使代码可读性更高

db.properties

~~~properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/blog?javaee=GMT&useSSL=false
username=root
password=123
~~~

mybatisConfig.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 1、mybatis使用properties来引入外部properties配置文件的内容
    resource 引入类路径下资源
    url 引入网络路径或磁盘路径下资源 -->
    <properties resource="db.properties"></properties>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC" />
            <!-- 配置数据库连接信息 -->
            <dataSource type="POOLED">
                <property name="driver" value="${driver}" />
                <property name="url" value="${url}" />
                <property name="username" value="${username}" />
                <property name="password" value="${password}" />
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="DaoMapper.xml"></mapper>
        <!--扫描包的形式装配mapper-->
        <!--<package name="cn.tfs.yunge.dao"></package>-->
    </mappers>
</configuration>
~~~

## 3.整合

### 3.1.确保每个框架都可以独立正常运行

（写测试程序！！！）

### 3.2.整合spring,springmvc

1.首先确保sping的配置文件applicationContext.xml里面的扫描包确实是排除了@Controller的

2.确保spring-mvc的配置文件spring-mvc.xml里面的扫描包确实是只包含@Controller的

3.（重要）在web项目中，我们如果发起请求，那么肯定web.xml只会解析到spring-mvc.xml的配置文件

~~~xml
<!--读取配置文件读取-->
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath*:spring-mvc.xml</param-value>
    </init-param>
~~~



那么就解析不到spring的配置文件，所以service也不会被注入到IoC容器中，所以我们需要在web.xml配置一个spring的监听器，在服务器开启的时候就把spring的配置文件applicationContext.xml给加载了：

~~~xml
  <!--spring监听器   默认加载WEB-INF/下的applicationContext.xml文件-->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
  <!--设置配置文件路径-->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath*:applicationContext.xml</param-value>
  </context-param>
~~~



4.实现：

UserServiceImpl.java

~~~java
@Service("userService")
public class UserServiceImpl implements UserService{
     public List<User> findUser(User user) {
        System.out.println("Service findUser 执行...");
        return  dao.findUser(user);

    }
}
~~~

HelloController.java

~~~java
 @Controller
public class HelloController {
    @Autowired
    private UserService userService;

    @RequestMapping("/1")
    public String index(){
        User user = new User();
        userService.findUser(user);
        return "index";
    }
}
~~~

测试结果

![1570863946084](/mdImg/spring+springmvc+mybatis%EF%BC%88ssm%EF%BC%89%E6%95%B4%E5%90%88.assets/1570863946084.png)

### 3.3.整合spring mybatis

1.在业务层调用dao的原理也是一样的，唯一不同的是这里我们使用的是代理Dao，他是个接口，所以我们要想办法把代理下实现类也给装配到IoC容器中

话不多说咱们看spring的配置文件applicationContext.xml

~~~xml
<!--整合mybatis-->
    <!--配置连接池-->
    <!--引入db.properties数据库配置信息-->
    <context:property-placeholder location="classpath*:db.properties"></context:property-placeholder>
    <bean id="pooledDataSource" class="org.apache.ibatis.datasource.pooled.PooledDataSource">
        <property name="driver" value="${jdbc.driver}" />
        <property name="url" value="${jdbc.url}" />
        <property name="username" value="${jdbc.username}" />
        <property name="password" value="${jdbc.password}" />
        <!--自动提交事务-->
        <property name="defaultAutoCommit" value="true"></property>
    </bean>
    <!--配置SQLSessionFactory工厂-->
    <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--将上面的连接池注入到该工厂-->
        <property name="dataSource" ref="pooledDataSource"></property>
        <!--扫描mapper所在的包-->
        <property name="mapperLocations">
            <list>
                <value>classpath*:cn/tfs/yunge/dao/*.xml</value>
            </list>
        </property>
    </bean>
    <!--配置Dao接口的所在包-->
    <bean id="mapperScannerConfigurer" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="cn.tfs.yunge.dao"></property>
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactoryBean"></property>
    </bean>
~~~

编写了上述连接池等信息之后 咱们的mybatis全局配置文件就可以删除了，之前导入在mybatisConfig.xml文件的db.properties文件以spring-context的方式引入applicationContext.xml

~~~xml
<context:property-placeholder location="classpath*:db.properties"></context:property-placeholder>
~~~



2.上述连接池和配置SQLSessionFactory工厂需要引入两个之前spring-mvc没有用到过的坐标依赖（jar包）

2.1.连接池：事务包tx和jdbc

~~~xml

<!--事务管理核心-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
 	    <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.0.2.RELEASE</version>
        </dependency>
~~~

2.2.配置SQLSessionFactory工厂和配置Mapper的所在包：org.mybatis的包org.apache.ibatis.datasource.pooled.PooledDataSource mybatis的连接池包

~~~xml
 <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.2.2</version>
        </dependency>
~~~

3.UserServiceImpl.java

~~~java
@Service("userService")
public class UserServiceImpl implements UserService {
    @Autowired
    private Dao dao;
    @Override
    public List<User> findUser(User user) {
        System.out.println("Service findUser 执行...");
        return dao.findUser(user);
    }
    @Override
    public int insertUser(User user) {
        System.out.println("Service insertUser 执行...");
        return dao.insertUser(user);
    }
~~~

HelloController.java

~~~java
@Controller
public class HelloController {
    @Autowired
    private UserService userService;

    @RequestMapping("/1")
    public String findUser(Model model){
        User user = new User();
        user.setUsername("zjh");
        model.addAttribute("result", userService.findUser(user));
        return "index";
    }
    @RequestMapping("/2")
    public String insertUser(Model model){
        User user = new User();
        user.setUsername("zjh");
        user.setPassword("123");
        model.addAttribute("result",userService.insertUser(user));
        return "index";
    }
}
~~~

测试：

![1570873585507](/mdImg/spring+springmvc+mybatis%EF%BC%88ssm%EF%BC%89%E6%95%B4%E5%90%88.assets/1570873585507.png)

# Δ踩坑：

1.pom.xml坐标:

spring-tx  spring-jdbc事务管理导入，mybatis-spring包的导入

2.db.properties的键最好加个jdbc.xxx

~~~properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/javaee?serverTimezone=GMT&useSSL=false&allowPublicKeyRetrieval=true
jdbc.username=root
jdbc.password=123
~~~

3.最坑的问题就是java.lang.AbstractMethodError: org.mybatis.spring.transaction.SpringManagedTransaction.getTimeout()Ljava/lang/Integer;异常

![1570873725282](/mdImg/spring+springmvc+mybatis%EF%BC%88ssm%EF%BC%89%E6%95%B4%E5%90%88.assets/1570873725282.png)

原因是适配版本问题，这个问题搞了我一个半小小时。。。手动沮丧

4.最后一个就是需要加入pom.xml的静态资源

~~~xml
<resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
~~~

不然会找不到*mapper.xml或者.properties ，这个是解决nested exception is org.apache.ibatis.binding.BindingExceptio这个异常的