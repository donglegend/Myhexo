---
title: web cache
date: 2017-12-11 14:06:52
categories: javascript
tags: http
---

一知半解的 cache 缓存，我的烂笔头

<!-- more -->

## 什么是缓存

简单说 就是缓存是一种保存资源副本并在下次请求时直接使用该副本的技术。当 web 缓存发现请求的资源已经被存储，它会拦截请求，返回该资源的拷贝，而不会去源服务器重新下载

## 为什么要用缓存

缓解服务器端压力，提升性能(获取资源的耗时更短了)，一切都是为了速度和重用。

## 缓存分类

* 数据库缓存 为了避免重复查询计算，造成数据库负荷，比如 memcached

* CDN 缓存 动态分发请求至合适的服务区，传说中的负载均衡等

* 代理服务器缓存 浏览器和服务器中间的服务器，与浏览器缓存相比，服务用户更多更广

* 浏览器缓存 每个浏览器都实现了 HTTP 缓存，我们通过浏览器使用 HTTP 协议与服务器交互的时候，浏览器就会根据一套与服务器约定的规则进行缓存工作。

* 应用层缓存 应用层缓存是指我们在代码层面上做的缓存。通过代码逻辑，把曾经请求过的数据或资源等，缓存起来，再次需要数据时通过逻辑上的处理选择可用的缓存的数据。

<b style="color: red;">着重介绍浏览器缓存，以下字段设置工作原理，浏览器都是自动完成的，无须前端开发者做任何操作,服务器端设置响应字段即可</b>

## 浏览器缓存控制 Cache-Control

根据 http 发展时间线开始叙述

### Expires

最初的那些年，大概在 http/1.0 时代，缓存技术比较简单，服务器直接告诉浏览器我要缓存某个资源文件，缓存时间是多久，浏览器就会照做这个字段表示 要过期的时间，专业术语也有叫做 "新鲜度", 过了这个时间就会重新发起请求

缺点：

* 如果客户端和服务器时间不同步，将会导致缓存的内容提前过期或者到期资源没有正常更新
* 如果设置一个绝对过期时间，过期来临时将会出现浪涌现象

补充：这个时代，还有一个字段 [Pragma 来禁用缓存](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Pragma)

```
Pragma: no-cache
```

### Cache-Control (max-age)

HTTP/1.1 定义的 Cache-Control 头用来区分对缓存机制的支持情况， 请求头和响应头都支持这个属性。通过它提供的不同的值来定义缓存策略。

1. no-cache: 每次有请求发出时，缓存会将此请求发到服务器（请求中会带有 Etag,稍后讲解），服务器端会验证请求中所描述的缓存是否过期，若未过期（实际就是返回 304），则缓存才使用本地缓存副本
2. no-store: 禁止缓存任何响应，也就是说每次用户请求资源时，都会向服务器发送一个请求，每次都会下载完整的响应。
3. public: 表示该响应可以被任何中间人（比如中间代理、CDN 等）缓存。若指定了"public"，则一些通常不被中间人缓存的页面（因为默认是 private）（比如 带有 HTTP 验证信息（帐号密码）的页面 或 某些特定影响状态码的页面），将会被其缓存。
4. private： 表示该响应是专用于某单个用户的，中间人不能缓存此响应，该响应只能应用于浏览器私有缓存中。
5. max-age: 用来设置资源被缓存的最长时间(单位是秒)，<b style="color: red">开始时间是请求获取资源的时候</b>，所以各个用户是不会相同的。
6. must-revalidate 必须先验证它的状态，已过期的缓存将不被使用
7. [...more](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control)

<b style="color: red">因为向下兼容，大部分浏览器都支持 Cache-Control， max-age 优先级大于 Expires</b>

demo

```
Cache-Control:public, max-age=600
```

缺点：试想一下，上述 demo 中，这是失效时长到期之后，再次请求相同的资源，浏览器发现缓存过期，就会重新发起一个完整的资源请求，但是资源并没有改变，是不是很浪费呢？

* 过期就会重新请求，依然浪费

既然浪费，那么必然有解决方案，怎么解决呢，聪明的你很快想到了进行 缓存有效性验证，下面会说明。

### Last-Modified

为了解决缓存到期，但是资源并没有更改的问题，引入了 缓存验证机制，顾名思义，最后修改时间

```
Last-Modified:Tue, 12 Dec 2017 08:31:02 GMT
```

原理说明：

1. 在浏览器首次请求某个资源时，服务器端返回的状态码是 200 （ok），内容是你请求的资源，同时有一个 Last-Modified 的属性标记(Reponse Header)，标识此文件在服务期端最后被修改的时间，格式：Last-Modified:Tue, 12 Dec 2017 08:31:02 GMT
2. 浏览器第二次请求该资源时，根据 HTTP 协议的规定，浏览器会向服务器传送 If-Modified-Since 报头(Request Header)，询问该文件是否在指定时间之后有被修改过，格式为：If-Modified-Since:Tue, 12 Dec 2017 08:31:02 GMT
3. 如果服务器端的资源没有变化，则服务器返回 304 状态码（Not Modified），内容为空，这样就节省了传输数据量。当服务器端代码发生改变，则服务器返回 200 状态码（ok），内容为请求的资源，和第一次请求资源时类似。从而保证在资源没有修改时不向客户端重复发出资源，也保证当服务器有变化时，客户端能够及时得到最新的资源。

<b style="color: red">注：如果 If-Modified-Since 的时间比服务器当前时间(当前的请求时间 request_time)还晚，会认为是个非法请求</b>

缺点： 这个缓存验证有什么缺点呢，试想一下，某次版本更新，很大一部分文件的内容并没有更改，但是重新打包生成了，那么 文件信息 Last-Modified 势必会发生变化，是不是校验不是很准确呢，而且这个精确度也只有秒级别。

* 校验不准确

### ETags

http/1.1 中增加的 header，HTTP 协议规格说明定义 ETag 为“被请求变量的实体值” 。另一种说法是，ETag 是一个可以与 Web 资源关联的记号（token）。典型的 Web 资源可以一个 Web 页，但也可能是 JSON 或 XML 文档。服务器单独负责判断记号是什么及其含义，并在 HTTP 响应头中将其传送到客户端。直接理解成 Etags 是一个唯一指纹标识就可以了。

```
Etag:W/"5f-16049d93d70"
```

原理说明：

1. 当浏览器首次请求资源的时候，服务器会返回 200 的状态码（ok）,内容为请求的资源，同时 response header 会有一个 ETag 标记，该标记是服务器端生成的一串唯一标识资源的字符串，Etag:W/"5f-16049d93d70"
2. 当浏览器第 2 次请求该资源时，浏览器会在传递给服务器的 request 中添加 If-None-Match 报头，询问服务器改文件在上次获取后是否修改了，报头格式：If-None-Match:W/"5f-16049d93d70"
3. 服务器在获取到浏览器的请求后，会根据请求的资源查找对应的 ETag，将当前服务器端指定资源对应的 Etag 与 request 中的 If-None-Match 进行对比，如果相同，说明资源没有修改，服务器返回 304 状态码（Not Modified），内容为空；如果对比发现不相同，则返回 200 状态码，同时将新的 Etag 添加到返回浏览器的 response 中。

### 缓存字段共存优先级

首先分两个阶段，发起请求之前 和 发起请求，毫无疑问，前者比后者靠前处理。

<b style="color: red">发起请求之前： Cache-Control > Expires</b>
如果过期了，那么发起请求，进行缓存验证（If-None-Match (Etag) 和 If-Modified-Since (Last-Modified)）如果 etag 和 last-modified 同时存在，那么两个都会比较，没有优先级

### 如何在缓存有限期间进行资源更新

<b style="color: red">
有了缓存，也就有了问题，比如在 max-age 的时效之内，如何进行资源更新呢？
</b>
答案是如果不进行 url 更改，那么是没办法进行更新的

所以我们经常看到打包工具打包资源的时候，都会加上 hash 值 或者 时间戳进行强制更新。

以上所有论点都经过验证，服务使用的的 express

```
const express = require('express')
const app = express()
var options = {
    lastModified: true,
    etag: true,
    maxAge: 200000, // 这里单位是 毫秒
    setHeaders: function(res, path, stat) {}
}
app.use(function(req, res, next) {
    console.log('资源访问路径: ' + req.path)
    next()
})
app.use(express.static(__dirname, options))
```

### 其他

勤奋的开发者，在测试 web cache 的时候，有时候发现 304 来源 是 from memory cache ,而不是 from disk cache,那是因为，在一个会话期间，浏览器直接重用了内存里的 资源，关闭浏览器，重新打开即可看到来自 disk

参考资料：
[Cache-Control MDN docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control)
[serve-static 源码实现](https://github.com/expressjs/serve-static)
