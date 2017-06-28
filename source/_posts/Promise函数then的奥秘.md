---
title: Promise函数then的奥秘
date: 2017-04-06 16:30:42
categories: javascript
tags: promise
---
Promise的then方法可以接受前一个函数的执行结果，还可以保证另一个Promise的顺序执行，这到底是怎么做到的呢？
<!-- more -->
很久以前写过一篇 [Promise原理探究](http://donglegend.com/2016/09/11/promise%E5%8E%9F%E7%90%86%E6%8E%A2%E7%A9%B6/) ，现在想来确实简单幼稚了很多，但是基本思想基于 callback 原理实现的大方向是没问题的，就是版本实现过于简单，仅仅为了理解，不投入使用。


## 原理图(先上图)
![](/images/promise/a.png)

## 问题需求 
如何保证多个 promise 顺序执行？
实例：
```
var f1 = function (){
	return new  Promise(function (resolve, reject){
		setTimeout(function (){
			console.log("f1 ok!")
			resolve("f1 ok!");
		}, 1000)
	});
}
var f2 = function (){
	return new  Promise(function (resolve, reject){
		setTimeout(function (){
			console.log("f2 ok!")
			resolve("f2 ok!");
		}, 3000)
	});
}
var f3 = function (){
	return new  Promise(function (resolve, reject){
		setTimeout(function (){
			console.log("f3 ok!")
			resolve("f3 ok!");
		}, 2000)
	});
}
```
当然如果要并行的话，我们很容易想到 Promise.all 方法:
```
Promise.all([f1(), f2(), f3()]).then(function (data){
	console.log(data)
})
// f1 ok! 
// f3 ok! 
// f2 ok! 
// ["f1 ok!", "f2 ok!", "f3 ok!"]
```

如果要顺序执行：
```
f1().then(f2).then(f3)
// f1 ok!
// f2 ok!
// f3 ok!

//或者这样

function f(all) {
    var promise = Promise.resolve();
    all.forEach((p, index) => {
        promise = promise.then(p)
    })
}
f([f1, f2, f3])

```

<b style="color: red;">那么问题来了，then是如何做到顺序执行的呢，参数既可以是一个普通函数，也可是是一个返回promise的函数？</b>

## then的奥秘
很多实现promise的库都比较复杂，如果自己实现的话，可以借鉴下面简单的代码：
```
Promise.prototype.then = function(onFulfilled, onRejected) {
	var promise = this;
	return new Promise(function(resolve, reject) {
		function handle(value) {
			var ret = typeof onFulfilled === 'function' && onFulfilled(value) || value;
			if (ret && typeof ret['then'] == 'function') {
				ret.then(function(value) {
					resolve(value);
				}, function(reason) {
					reject(reason);
				});
			} else {
				resolve(ret);
			}
		}
		function errback(reason) {
			reason = typeof onRejected === 'function' && onRejected(reason) || reason;
			reject(reason);
		}
		if (promise._status === 'PENDING') {
			promise._resolves.push(handle);
			promise._rejects.push(errback);
		} else if (promise._status === FULFILLED) { 
			callback(promise._value);
		} else if (promise._status === REJECTED) {
			errback(promise._reason);
		}
	});
}
```
重点在then的实现，看上述代码，<b style="color: red;">每个then返回的是什么，是一个新的 Promise，一个新的 Promise，一个新的 Promise</b>
第二个重点是，<b style="color: red;">在内部又处理了一个 回调函数运行结果是  一个 promise的 判断</b>，如果是那么等待这个promise运行结束才调用 resolve 更改状态，<b style="color: red;">关键是resolve的调用时机，resolve的调用时机，</b>才能够往下执行，这两步就是then函数的关键。
是不是 有点晕，请看最开始的图。