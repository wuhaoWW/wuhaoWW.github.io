---
layout:     post
author:     zjhChester
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - java
---
# JDBC(Java Database Connectivity，简称JDBC)---客户端程序如何来访问数据库

## 前言：

主流架构：

`1、BS架构(Browser  and Server) 浏览器服务器架构`--->`web、javaweb、phpweb`

`2、CS架构(Client and Server) 客户端服务器架构` --->`QQ、微信`

简言之就是用`java`程序去访问数据库

## 正文：

JDBC的连接步骤：

`加载驱动`--->`建立连接`--->`执行语句`--->`得到结果集`

1、加载驱动

加载驱动，首先是先把驱动jar包导入

~~~xml
 <!--mysql驱动-->
    <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.11</version>
    </dependency>
~~~

编写测试用例；

~~~java
 //初始化驱动
    public void init(){
        try {
            //加载mysql驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            //建立连接  参数解释：第一个参数是数据库链接地址(不同数据库会有不同的连接地址，由数据库厂商提供)，第二个参数是用户名，第三个参数是密码
            Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/zjh?serverTimezone=GMT&useSSL=false&allowPublicKeyRetrieval=true", "root", "123");
            //获取数据库预处理对象preparedStatement  并编写sql语句
            PreparedStatement preparedStatement = conn.prepareStatement("select * from user");
            //执行查询语句
            ResultSet resultSet = preparedStatement.executeQuery();
            //循环读取查询结果集（因为查询的结果可能不止一条）
            while(resultSet.next()){
                //打印查询出来的第一个字段
                System.out.println(resultSet.getString(1));
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }


    //编写主函数测试
    public static void main(String[] args) {
        new MyJdbc().init();
    }

~~~

要知道，我们每访问一次数据库，其实都是与数据库建立了一次会话，建立会话之后，你所有的增删改查，都是在本次会话中进行的，假如说，会话终止，或者在建立会话的时候建立失败，那么你所有的增删改查的动作都无效

需要注意的是，如果你执行的是查询语句，才会有结果集，如果是增加更新删除，只会有执行的条数，返回类型是型

## 优化：

再看看以上代码，其实有很大一部分咱们没有考虑到，所以他现在是烂代码，怎么把它变成好代码，降低耦合。

所谓降低耦合就是降低程序之间的依赖关系，比如修改数据库参数的时候，咱们不用改动源码，直接在配置文件上面修改就可以运行(因为改了源码的话，还需要重新编译，编译之后的文件是.class的字节码文件，不能直接修改参数，所以我们需要借助配置文件的帮助)

### 配置文件：

java中配置文件主流的分两种，一种是properties,一种是xml，这里我使用的是properties。

编写一个db.properties，存放到咱们的resource资源文件夹根目录下（里面一定不要为了格式而多大空格，空格会算入内容，以键值对的形式编写）

~~~properties
#降低程序代码间的依赖关系，使用配置文件
jdbc.url=jdbc:mysql://localhost:3306/zjh?serverTimezone=GMT&useSSL=false&allowPublicKeyRetrieval=true
jdbc.username=root
jdbc.password=123
~~~

接下来就是读取配置文件让咱们的代码依赖减少：

~~~java
  //读取配置文件
            Properties properties = new Properties();
            properties.load(this.getClass().getClassLoader().getResourceAsStream("db.properties"));
            conn = DriverManager.getConnection(properties.getProperty("jdbc.url"),properties.getProperty("jdbc.username") ,properties.getProperty("jdbc.password") );
~~~

这样几遍改动数据库密码账号，我都不需要改动源码文件，只需要修改配置文件就可以了。

### 注意事项：

在一次会话结束之后，一定要注意，打开的资源要关闭，咱们jvm的内存和mysql的连接数是有限的，不能把资源一直开着，执行结束以后就关闭资源，养成良好的代码风格

## 源码展示：

MyJdbc.java

~~~java
package cn.tfs.jdbc;


import java.io.IOException;
import java.sql.*;
import java.util.Properties;

public class MyJdbc {
    private Connection conn;
    private PreparedStatement preparedStatement;
    private ResultSet resultSet;
    //初始化驱动
    public void init(){
        try {
            //加载mysql驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            //建立连接  参数解释：第一个参数是数据库链接地址(不同数据库会有不同的连接地址，由数据库厂商提供)，第二个参数是用户名，第三个参数是密码
            //读取配置文件
            Properties properties = new Properties();
            properties.load(this.getClass().getClassLoader().getResourceAsStream("db.properties"));
            conn = DriverManager.getConnection(properties.getProperty("jdbc.url"),properties.getProperty("jdbc.username") ,properties.getProperty("jdbc.password") );
            //获取数据库预处理对象preparedStatement  并编写sql语句
            preparedStatement = conn.prepareStatement("select * from user");
            //执行查询语句并将查询结果拿给结果集
            resultSet = preparedStatement.executeQuery();
            //循环读取查询结果集（因为查询的结果可能不止一条）
            while(resultSet.next()){
                //打印查询出来的第一个字段
                System.out.print(resultSet.getString("id")+"\t");
                System.out.print(resultSet.getString("name")+"\t");
                System.out.print(resultSet.getString("age")+"\t");
                System.out.println();
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            //释放资源 关闭连接  (必要的)
            try {
                resultSet.close();
                preparedStatement.close();
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }

        }
    }

    public String query(){
        return "";
    }
    public int delete(){
        return 0;
    }
    public int upDate(){
        return 0;
    }
    public int add(){
        return 0;
    }

    //编写主函数测试
    public static void main(String[] args) {
        new MyJdbc().init();
    }
}

~~~

db.properties

~~~properties
#降低程序代码间的依赖关系，使用配置文件
jdbc.url=jdbc:mysql://localhost:3306/zjh?serverTimezone=GMT&useSSL=false&allowPublicKeyRetrieval=true
jdbc.username=root
jdbc.password=123
~~~

需要帮助的同学欢迎留言或者联系邮箱：zjhChester@gmail.com

![1580540814070](https://zjhchester.github.io/img/apple-touch-icon.png)