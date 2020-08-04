### 1. 缓存

> 缓存是可以自动保存常见文档副本的 HTTP 设备。当 Web 请求抵达缓存时，如果本地有“已缓存的”副本，就可以从本地存储设备而不是原始服务器中提取这个文档。

##### 1.1 缓存的好处

* 减少了冗余的数据传输；
* 缓解了网络瓶颈的问题；
* 降低了对原始服务器的要求；
* 降低了距离时延。

##### 1.2 命中和未命中的

> 缓存命中：可以用已有的副本为某些到达缓存的请求提供服务。

> 缓存未命中：其他一些到达缓存的请求可能会由于没有副本可用，而被转发给原始服务器。

> HTTP 再验证：原始服务器的内容可能会发生改变，缓存要时不时对其进行检测，看看它们保存的副本是否仍然是服务器上最新的副本。这些“新鲜度检测”被称为 HTTP 再验证。

> 缓存命中率：由缓存提供服务的请求所占的比例被称为缓存命中率。

##### 1.3 强制缓存和协商缓存

> 根据是否需要向服务器重新发起 HTTP 请求将缓存过程分为强制缓存和协商缓存。

###### 1.3.1 强制缓存

> 向缓存查找该请求结果，并根据该结果的缓存规则来决定是否使用该缓存结果的过程。

强制缓存主要分为三种情况：

* 不存在该缓存结果和缓存标识，强制缓存失效，则直接向服务器发起请求；

* 存在该缓存结果和缓存标识，但该结果已经失效，强制缓存失效，则使用协商缓存；

* 存在该缓存结果和缓存标识，且该结果尚未失效，强制缓存生效，直接返回该结果。

> 控制强制缓存的字段：Cache-Control 和 Expires。前者优先级高于后者。

* Cache-Control：使用的是相对时间，max-age 值定义了文档的最大使用期，从第一次生成文档到文档不再新鲜、无法使用为止，最大的合法生存时间。

* Expires：使用的是绝对时间，依赖于计算机时钟的正确设置。

###### 1.3.2 协商缓存

> 强制缓存失效，但是并不意味着它与原始服务器上目前处于活跃状态的文档有实际的区别，意味着到了核对的时间了。

协商缓存分为两种情况：

* 协商缓存生效，需要再验证的内容没有发生变化，返回 304；

* 协商缓存失效，缓存会获取一分新的文档副本，并将其存储在旧文档的位置上（返回200和请求结果）。

> 控制协商缓存的字段：Last-Modified / If-Modified-Since 和 Etag / If-None-Match。

* Last-Modified：服务器请求响应时，返回该资源文件在服务器最后被修改的时间；

* If-Modified-Since：客户端再次发送请求时，携带上次请求返回的 Last-Modified 值，通过此字段值告诉原始服务器该资源上次请求返回的最后修改时间。服务器收到该请求，发现 If-Modified-Since 字段，根据其字段值与该资源在原始服务器的最后被修改时间做对比。

* Etag：是服务器响应请求时，返回当前资源文件的一个唯一标识（由服务器生成）。

* If-None-Match：客户端再次发送请求时，携带上次请求返回的唯一标识值 Etag。

### 2. cookie、session、localStorage、sessionStorage、token

##### 2.1 cookie

##### 2.2 session

##### 2.3 localStorage

##### 2.4 sessionStorage

##### 2.5 token

###### 2.5.1 token的生成方式

> （1）浏览器第一次访问服务器时，服务器根据传过来的唯一标识userId，通过算法，加一个密钥，生成一个token；
> （2）接着通过base64编码将token返回给客户端。
> （3）客户端将token保存（保存在cookie或者localStorage中）起来，下次请求时需要带着token，服务器收到请求后，用相同的算法和密钥去验证token。

###### 2.5.2 token的作用

> （1）防止表单重复提交；  
> （2）用来作身份验证。

### 三次握手协议

### 跨域

### RESTful 架构

* 每一个 URI 代表一种资源；
* 客户端与服务器之间，传递这种资源的某种表现层；
* 客户端通过 HTTP 的四个动词，对服务器端资源进行操作，实现“表现层状态转化”

### 3. HTTP 协议和 HTTPS 协议

##### 3.1 HTTP 协议

##### 3.2 HTTPS 协议