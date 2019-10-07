# 基于注解的IoC配置

## 1、用于创建对象的注解

### 1、作用：

与在xml配置文件中编写一个<bean>标签实现功能是一样的

### 2、注解：@Component

#### 作用：

用于把当前类对象存入spring容器

#### 属性：value

用于指定bean的id，当我们不写时，默认值是当前默认类名，首字母小写

#### 衍生：spring框架为我们提供明确的三层使用的注解，使三层对象更清晰

#### @Controller  一般用于表现层

#### @Service  一般用于业务层

~~~java
@Service("Service")
~~~



####  @Repository 一般用于持久层

~~~java
@Repository("Dao")
~~~





## 2、用于注入数据的注解

### 1、作用：

与在xml配置文件的bean标签中编写一个<property>标签实现功能是一样的

### 2、注解：@Autowired

~~~java
 @Autowired
    private  Dao dao;
~~~

#### 原理：

通过变量类型去IoC容器中去找**对应的类型**或者**接口实现类**

![1569744150315](%E5%9F%BA%E4%BA%8E%E6%B3%A8%E8%A7%A3%E7%9A%84IoC%E9%85%8D%E7%BD%AE.assets/1569744150315.png)

#### 作用：

自动按照类型注入。只要容器中有位移一个bean对象类型和要注入的变量类型匹配，就可以注入成功

#### δ、注意事项：如果多个bean类型是一样的/IoC容器中有多个匹配

首先按照类型匹配，如果有多个对象，再次按照变量名去IoC容器中找对应的对象

##### 解决方法：

###### 1、变量名与注解value一致

![1569744580844](%E5%9F%BA%E4%BA%8E%E6%B3%A8%E8%A7%A3%E7%9A%84IoC%E9%85%8D%E7%BD%AE.assets/1569744580844.png)

###### 2、@Qulifier

![1569744741107](%E5%9F%BA%E4%BA%8E%E6%B3%A8%E8%A7%A3%E7%9A%84IoC%E9%85%8D%E7%BD%AE.assets/1569744741107.png)

作用：

首先按照类型匹配，如果有多个对象，再次按照变量名去IoC容器中找对应的对象。给类成员变量不能单独使用，但是在给方法参数注入的时候可以单独使用

###### 3、@Resource

作用：直接按照bean的id注入，他可以独立使用

属性：name

用于指定bean的id

![1569744982037](%E5%9F%BA%E4%BA%8E%E6%B3%A8%E8%A7%A3%E7%9A%84IoC%E9%85%8D%E7%BD%AE.assets/1569744982037.png)

#### 出现位置：

1、变量

2、方法

### 3、注解：@Value

#### 作用：

用于注入基本类型和String类型的数据

#### 属性：value

用于指定数据的值。他可以使用spring中的el表达式${表达式}

3、用于改变作用范围的注解

~~~java
@Value(value = "123")
    private Integer id;
~~~

## 3、用于改变作用范围的注解

### 1、作用：

与在xml配置文件的bean标签中使用scope属性实现功能是一样的

### 2、注解@Scope

```java
@Scope("prototype")
```

### 3、作用范围：

类（bean对象）

## 4、与生命周期相关的注解

### 作用：

与在xml配置文件的bean标签中使用init-method和destroy-method属性实现功能是一样的