# spring中的bean的作用范围

## 1、bean标签的scope属性：

### 作用：

用于指定bean的指定范围

### 取值：

#### 1、singleton:单例（默认）

#### 2、prototype:多例

#### 3、request:作用于web应用的请求范围

#### 4、session:作用于web应用的会话范围

#### 5、global-session:作用于集群环境的全局会话范围，当不是集群环境时，他就是session

##### 举例：用于集群环境的问题

![1569729322701](spring%E4%B8%AD%E7%9A%84bean%E7%9A%84%E4%BD%9C%E7%94%A8%E8%8C%83%E5%9B%B4.assets/1569729322701.png)

