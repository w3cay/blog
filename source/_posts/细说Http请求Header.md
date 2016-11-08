---
title: 细说HTTP请求Header
date: 2016-09-21 17:53:55
tags: [HTTP]
---

HTTP 协议是做技术的基本功，不论是开发客户端还是服务端。在日常的开发过程中，HTTP协议应用的十分广泛，数据的传输主要靠HTTP来进行。如果说HTTP是因特网的信使，那么HTTP报文就是它用来搬东西的包裹了，HTTP报文分为请求报文和响应报文，这篇文章主要说明请求报文。

下面是访问我的博客首页的请求报文

```
GET / HTTP/1.1
Host: w3cay.com
Connection: keep-alive
Pragma: no-cache
Cache-Control: no-cache
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.116 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Encoding: gzip, deflate, sdch
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6,zh-TW;q=0.4
Cookie: Hm_lvt_c45eb0f0...
```

请求报文的首行是请求行，包含请求方法、请求URL和HTTP协议版本号。上面请求行表示通过 GET 方法来获取根目录 / 路径下的资源，HTTP版本为1.1

**Host** 表示接收请求的服务器的主机名和端口号，默认端口为80

**Connection** 指定客户端和服务器请求/响应连接有关的选项，当值为close 时，告诉WEB服务器或者代理服务器，在完成本次请求的响应后，断开连接，不要等待本次连接的后续请求了。当值为keepalive时，告诉WEB服务器或者代理服务器，在完成本次请求的响应后，保持连接，等待本次连接的后续请求。 

**Pragma** 和 **Cache-Control** 都用来传送缓存指示，其中，Pragma 是 HTTP 1.0的实现，而 Cache-Control 是 HTTP1.1 的实现。两者有所区别，Pragma 一般只用于请求头部，而 Cache-Control 同时用于服务端和客户端。no-cache 表示请求的资源不是从缓存中获得的，而是最新的资源。

**Upgrade-Insecure-Requests** 作用就是让浏览器自动升级请求，如果在HTTPS的页面中出现HTTP,就会出现警告提示不允许在HTTPS的页面中使用HTTP协议请求资源，当请求头声明Upgrade-Insecure-Requests之后，页面一旦发现存在上述响应头，会在加载 http 资源时自动替换成 https 请求。同时也可以在页面中声明，具体参考 https://www.w3.org/TR/2015/CR-upgrade-insecure-requests-20151008/#preference

```
<meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests" />
```

**User-Agent** 该属性是一个只读的字符串，声明了浏览器用于 HTTP 请求的用户代理头的值。

**Accept** 告诉服务器能够发送哪些媒体类型，下面是几个常见的 Content-Type:

1. text/html
2. text/plain
3. text/css
4. text/javascript
5. application/x-www-form-urlencoded
6. multipart/form-data
7. application/json
8. application/xml

**Accept-Encoding** 告诉服务器能够发送哪些编码方式,本站支持采用 gzip, deflate, sdch压缩过的资源。

**Accept-Language** 告诉服务器能够发送哪些语言，并按照q的权重值排序，默认 q 值为1，zh-CN 权重最高，优先返回，其次是 zh、en、zh-TW。详情参考 https://imququ.com/post/vary-header-in-http.html

**Cookie** 由于HTTP是无状态的，为了区分用户和记录一些数据，采用Cookie来进行用户的标识。