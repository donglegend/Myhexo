---
title: fetch 资源请求
date: 2016-09-28 15:32:43
categories: javascript
tags: ajax
---
fetch() 方法用于发起获取资源的请求。它返回一个 promise，这个 promise 会在请求响应后被 resolve，并传回 Response 对象。
<!-- more -->
##  XMLHttpRequest的痛 
我们使用ajax久矣，最熟悉的莫过于经过jQuery封装的ajax对象，因为简单好用。
经过封装打扮的ajax固然漂亮，可是我们都明白底层是怎么回事。对于主流浏览器是 XMLHttpRequest，对于低版本IE是 ActiveXObject
所以，对于传统ajax使用，我们首先得统一 请求对象：
```
<script type="text/javascript">

function ajaxFunction()
 {
 var xmlHttp;
 
 try
    {
   // Firefox, Opera 8.0+, Safari
    return xmlHttp=new XMLHttpRequest();
    }
 catch (e)
    {

  // Internet Explorer
   try
      {
      return xmlHttp=new ActiveXObject("Msxml2.XMLHTTP");
      }
   catch (e)
      {

      try
         {
         return xmlHttp=new ActiveXObject("Microsoft.XMLHTTP");
         }
      catch (e)
         {
         alert("您的浏览器不支持AJAX！");
         return false;
         }
      }
    }
 }
</script>
```
然后使用依然隐隐作痛：
```
var xhr = ajaxFunction();
var url = "http://xxxx.xxx/api/xxxx"
xhr.open('GET', url);
xhr.responseType = 'json';

xhr.onload = function() {
  console.log(xhr.response);
};

xhr.onerror = function() {
  console.log("Oops, error");
};

xhr.send();
```
诚如是，XMLHttpRequest 的设计确实有点粗糙难用，配置和调用方式非常混乱，而且异步回调，没有Promise对象的模式。

## fetch简介
### 官方解释：
- fetch() 方法用于发起获取资源的请求。它返回一个 promise，这个 promise 会在请求响应后被 resolve，并传回 Response 对象。

- Window 和 WorkerGlobalScope 都实现了 GlobalFetch — 这意味着基本在任何场景下只要你想获取资源，都可以使用 fetch() 方法。

- 当遇到网络错误时，fetch() 返回的 promise 会被 reject，并传回 TypeError，虽然这也可能因为权限或其它问题导致。成功的 fetch() 检查不仅要包括 promise 被 resolve，还要包括 Response.ok 属性为 true。HTTP 404 状态并不被认为是网络错误。

### 语法：

	fetch(input, init).then(function(response) { ... });

#### 参数
- input
定义要获取的资源。这可能是：
	* 一个 USVString 字符串，包含要获取资源的 URL。
	* 一个 Request 对象。
- init 可选
一个配置项对象，包括所有对请求的设置。可选的参数有：
	* method: 请求使用的方法，如 GET、POST。
	* headers: 请求的头信息，形式为 Headers 对象或 ByteString。
	* body: 请求的 body 信息：可能是一个 Blob、BufferSource、FormData、URLSearchParams * 或者 USVString 对象。注意 GET 或 HEAD 方法的请求不能包含 body 信息。
	* mode: 请求的模式，如 cors、 no-cors 或者 same-origin。
	* credentials: 请求的 credentials，如 omit、same-origin 或者 include。
	* cache:  请求的 cache 模式: default, no-store, reload, no-cache, force-cache, or only-if-cached.
- 返回值
一个 Promise，resolve 时回传 Response 对象。


### 兼容性：
![浏览器兼容](/images/fetch/use.png)

## fetch基本使用
```
var url = "xxxxxx";
fetch(url).then(function(response) {
	console.log(response)
	if (response.status !== 200) {
		console.log("request " + url + "error! status: " + response.status);
		return;
	}
	return response.json();
}).then(function(data) {
	console.log(data);
}).catch(function(e) {
	console.log("error");
});

```
如果你有做测试的话，可以看到 console.log(response) 打印出的信息，包含请求状态，请求头，请求体等等。
另外调用 response.json()方法返回依然是一个 Promise对象，所以，可以如下使用:
```
response.json().then(function (data){
      // do something
})
```
另外还有 第一个参数是  Request对象的使用方法，这里不做阐述。

##  参考资料
[fetch mdn](https://developer.mozilla.org/zh-CN/docs/Web/API/GlobalFetch/fetch)


