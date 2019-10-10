# Spring-mvc拦截器HandlerInterceptor

## 1、概述：

### 1.1与过滤器Filter区别：

过滤器是可以过滤所有得请求包括静态资源，而拦截器是只能拦截Controller内部得方法，总而言之，拦截器做的事情，过滤器都能做，过滤器做的事情，拦截器不一定能做。

### 1.2实现自定义拦截器：

#### 第一步：实现HandlerInterceptor接口

~~~java
public class LoginHandlerInterceptor implements HandlerInterceptor
~~~

#### 第二步：重写预处理得方法

~~~java
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {}
public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }

    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {

    }
~~~

#### 第三步：在spring-mvc.xml配置文件中配置拦截器

~~~java
<mvc:interceptors>
        <mvc:interceptor>
            <!--拦截路径-->
            <mvc:mapping path="/userInfo"/>
            <!--拦截器注册-->
            <bean class="cn.tfs.interceptor.LoginHandlerInterceptor"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
~~~

#### 第四步：根据需求编写自定义拦截器得预处理方法

ps.该方法是我用来拦截未登录情况访问主页得信息

这代码是我用来获取拦截之后得路径得，用来作为登录成功之后得预处理路径：

```java
String requestURI = request.getRequestURI();request.getSession().setAttribute("Interceptor",requestURI);
```

源码：

~~~java
 public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
       Object user = request.getSession().getAttribute("loginUser");
       if(user == null){
           //未登录
           String requestURI = request.getRequestURI();
           request.getSession().setAttribute("Interceptor",requestURI);
           request.getSession().setMaxInactiveInterval(10);
           request.setAttribute("login_msg","没有权限  请先登录！");
//           response.sendRedirect("/to_login");
           request.getRequestDispatcher("/to_login").forward(request,response);
           return false;
       }else{
           //已登录
            return true;
       }
    }
~~~

#### 第五步：测试

![1570689442154](/mdImg/2019-10-10-Spring-mvc%E6%8B%A6%E6%88%AA%E5%99%A8.assets/1570689442154.png)

![1570689490834](2019-10-10-Spring-mvc%E6%8B%A6%E6%88%AA%E5%99%A8.assets/1570689490834.png)

