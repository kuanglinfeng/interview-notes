## 常见HTTP状态码



按第一个数字分类：1表示信息，2表示成功，3表示重定向，4表示客户端错误，5表示服务端错误



200 OK：操作成功 GET

201 Created：操作成功 POST

204 No Content：操作成功 DELETE

301 Moved Permanently: 永久重定向。请求的信息已经被移动到新的URI，会返回新的URI

302 Found: 暂时重定向。资源只是临时被移动，客户端继续使用原URI

304 Not Modified：未修改。所请求的资源未修改，服务器返回此状态码，不会返回任何资源

400 Bad Request：服务器不理解客户端的请求，未做任何处理。

401 Unauthorized：用户未提供身份验证凭据，或者没有通过身份验证。

403 Forbidden：用户通过了身份验证，但是不具有访问资源所需的权限。

404 Not Found：所请求的资源不存在，或不可用。

500 Internal Server Error：客户端请求有效，服务器处理时发生了意外。



## 关于URL和URI



URL 是 URI 的子集。任何东西，只要能够唯一地标识出来，都可以说这个标识是 URI 。如果这个标识是一个可获取到上述对象的路径，那么同时它也可以是一个 URL ；但如果这个标识不提供获取到对象的路径，那么它就必然不是 URL 。



## HTTP报文组成



1. 请求报文

- 请求行(方法、URI、HTTP 版本)

- HTTP首部字段
- 空行
- 报文主体

1. 响应报文

- 状态行(HTTP版本、状态码)
- HTTP首部字段
- 空行
- 报文主体



## HTTP首部（http headers）



通用首部：表示一些通用信息，如date表示报文创建时间

请求首部：请求报文独有，如cookie，If-Modified-Since

响应首部：响应报文独有，如set-cookie，Last-Modified

实体首部：含有与消息主体相关的附加信息，比如长度或者MIME类型。



## HTTP支持的方法



get：请求指定的页面信息并返回响应主体，一般用于数据的读取

post：向指定资源提交数据，请求服务器去处理

put：向指定资源位置上传其最新内容

delete：请求服务器删除所请求URI所标识的资源

options：请求服务器返回该资源所支持的所有HTTP请求方法，常用于客户端查看服务器的性能

head：获取服务器的响应头信息，常用于客户端查看服务器的性能

connnect：将连接改为管道方式的代理服务器，常用于SSL加密服务器与非加密的HTTP代理服务器的通信

trace：请求服务器回显其收到的请求信息，常用于HTTP请求的测试或诊断



## GET和POST有什么区别



get和post本质上都是TCP链接，除了本身的语义不同并无差别，但由于浏览器、服务器的限制，导致它们在应用过程中有一些不同：



- get是幂等的，post是不幂等的
- get参数通过URL传递，post参数放在request body中
- get请求在URL中传递的参数有长度限制；post没有（仅仅是浏览器或服务器的限制）
- get请求只能进行URL编码，post请求有多种编码方式
- get请求参数会被完整保留在浏览器历史记录里；post中的参数不会被保留
- get产生一个TCP数据包，post产生两个TCP数据包
- 对于get请求，浏览器将http header和data一并发送，服务器响应200 OK；对于post请求，浏览器先发送header，服务器响应100 Continue，浏览器再发送data，服务器响应200 OK
- 缓存方面：get请求类似于查找的过程，用户获取数据，可以不用每次都与数据库连接，所以可以使用缓存；post请求一般做的是修改和删除工作，必须与数据库交互，所以不能使用缓存



## HTTP2.0的新特点



- 基于HTTPS天然具有安全性
- 二进制分帧层：将所有传输信息分割成更小的信息或帧，并进行二进制编码（http2.0性能增强核心）
- 允许多路复用：基于二进制分帧层，在共享TCP连接的基础上，同时发送请求和响应。HTTP消息被分解为独立的帧，而不破坏消息本身的语义，交错发送出去，最后在另一端根据流ID和首部将他们重新组合起来
- 服务器推送：服务端根据客户端的请求，提前返回多个响应，推送额外的资源给客户端，支持缓存（遵循同源策略；基于客户端的请求响应来确定的）



## HTTP1.0、HTTP1.1、HTTP2.0的区别



HTTP1.0: 短连接（100张图，100次TCP握手挥手）

HTTP1.1: 长连接（100张图，1次TCP握手挥手）

HTTP2.0：长连接 + io多路复用



阮一峰的博客：https://www.ruanyifeng.com/blog/2016/08/http.html



## HTTP缓存机制



两种缓存机制，根据响应的header内容来决定



1. **强缓存**：状态码200，**浏览器不向服务器发送任何请求**，直接从本地缓存中读取文件并返回，相关响应头为：Cache-Control、Expires



- Cache-Control（新的头，max-age值，表示多久过期）

  ```js
  response.setHeader('Cache-Control', 'max-age=30') // 30s内请求资源，将直接从缓存中读取，不发请求
  ```

  

- Expires（旧的头，值为一个时间戳，代表过期的时间点，而且指的是本地时间，容易有bug）

  如果设置了Cache-Control，Expires会被忽略

  ```js
  response.setHeader('Expires', 'Wed, 21 Oct 2020 07:28:00 GMT')
  ```

  

如何更新缓存？

先给max-age设置超长时间，因为只有相同的url才会用之前的缓存，所以只要改变更新资源后的url就好啦，

实际工程中，会用md5值+随机数来确定资源名，每次更新资源时，资源名重新生成。



2. **协商缓存**：状态码304，**浏览器发送请求到服务器**，通过服务器来告知缓存是否可用，相关响应头为：Last-Modified/If-Modified-Since、Etag/If-None-Match



md5:一种摘要算法(把一个文件变成一个字符串)，对一个文件进行md5，会返回一串字符串，只要对这个文件内容进行了一点改动，进行md5得到的字符串都是不同的，该算法的特点是：文件内容差异越小，得到的字符串差异越大



**Etag**：设置Etag，下次再访问同一个资源时，请求头If-None-Match会带上这个Etag



```js
let str = fs.readFileSync('./js/main.js', 'utf-8')
response.setHeader('Content-Type', 'application/javascript;charset=utf8')
let fileMd5 = md(string) // 假设为 abcdfdklfdlfdfd
response.setHeader('ETag', fileMd5)
response.write(str)
response.end()
```



**If-None-Match**: 再次请求时会带上If-None-Match的值，如果这个值和md5值相同，说明资源没有改变，服务器只响应304，告诉浏览器直接从缓存读取就好了。反之，如果这个值和md5值不同，则说明资源改变了，浏览器就要重新下载资源了。



```js
Request Headers
...
If-None-Match: abcdfdklfdlfdfd
...
```



**Last-Modified和If-Modified-Since**：Last-Modified是该资源文件**最后一次更改时间**,服务器会在**response header**里返回，同时浏览器会将这个值保存起来，下一次发送请求时，放到**request headr**里的**If-Modified-Since**里，服务器在接收到后也会**做对比**，如果相同则命中协商缓存。



Etag/If-None-Match 和 Last-Modified/If-Modified-Since的对比：



- 在精确度上，Etag要优于Last-Modified，Last-Modified的时间单位是秒，如果某个文件在1秒内改变了多次，那么他们的Last-Modified其实并没有体现出来修改，但是Etag每次都会改变确保了精度
- 在性能上，Etag要逊于Last-Modified，毕竟Last-Modified只需要记录时间，而Etag需要服务器通过算法来计算出一个hash值。
- 在优先级上，服务器校验优先考虑Etag。



强缓存和协商缓存的过程：



![5](../images/http缓存.jpg)