### 1. 缓存



### 2. cookie、session、localStorage、sessionStorage、token

##### 2.1 cookie

##### 2.2 session

##### 2.3 localStorage

##### 2.4 sessionStorage

##### 2.5 token

###### 2.5.1 token的生成方式

>（1）浏览器第一次访问服务器时，服务器根据传过来的唯一标识userId，通过算法，加一个密钥，生成一个token；
>（2）接着通过base64编码将token返回给客户端。
>（3）客户端将token保存（保存在cookie或者localStorage中）起来，下次请求时需要带着token，服务器收到请求后，用相同的算法和密钥去验证token。

###### 2.5.2 token的作用

>（1）防止表单重复提交；  
>（2）用来作身份验证。

### 三次握手协议

### 跨域

### RESTful 架构

* 每一个 URI 代表一种资源；
* 客户端与服务器之间，传递这种资源的某种表现层；
* 客户端通过 HTTP 的四个动词，对服务器端资源进行操作，实现“表现层状态转化”
