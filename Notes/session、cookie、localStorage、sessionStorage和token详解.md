## session

## cookie

**1.token的生成方式**
（1）浏览器第一次访问服务器时，服务器根据传过来的唯一标识userId，通过算法，加一个密钥，生成一个token；
（2）接着通过base64编码将token返回给客户端。
（3）客户端将token保存（保存在cookie或者localStorage中）起来，下次请求时需要带着token，服务器收到请求后，用相同的算法和密钥去验证token。
**2.token的作用**
（1）防止表单重复提交；  
（2）用来作身份验证。

## localStorage

## sessionStorage

## token
