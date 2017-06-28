---
title: promise原理探究
date: 2016-09-11 10:29:36
categories: javascript
tags: promise
---

Promise是个什么玩意，大家都知道，度娘告诉我，以同步方式书写异步，解决回调地狱。。。

<!--more-->

## 状态机
早闻Promise的大名，简单介绍，根据状态改变来执行相应处理函数。
Promise的状态极其简单，只有 "pending", "resolved", "rejected"三种状态
然后就是如何实现的问题，最关键的当然是监听到状态的更新，然后才能做出回应，那么如何知道状态变了呢？
最初单纯的我开了一个脑洞,有以下想法：

- 写一个定时器不断查询状态值，如果有变化，那么执行回调
- 利用ES5的 [Object.defineProperty(obj, prop, descriptor)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)来追踪状态变化。
- 直接修改状态为指定值，然后接着执行回调

呜呜呜，有时候总会犯傻不是，上面第一种维护一个定时器，效率低下；第二种把状态变得很麻烦和复杂；
其实很多事情之所以复杂，是因为我们想的太多。
Promise根本没那么复杂，就三个状态，搞毛线定时器，状态跟踪。
利用观察者模式，只需要通过特定书写方式注册对应状态的事件处理函数，然后更新状态，调用注册过的处理函数即可。这个特定方式就是 then ,done ,fail, always...等方法；更新状态触发时机就是resolve, reject方法。

## 简单实现
理论分析： 
* 写一个类对象，维护一个 state，值有3种："pending", "resolved", "rejected"
* 通过then done fail always方法注册回调处理函数
* 通过resolve reject分别更新对应状态，并且调用注册函数

代码如下:

```
/**
 * [3种状态]
 * @type {String}
 */
var PENDING = "pending";
var RESOLVED = "resolved";
var REJECTED = "rejected";

/**
 * [Promise类实现]
 * 构造函数传入一个fn，有两个参数，resolve：成功回调; reject：失败回调;
 * state: 状态存储
 * doneList: 成功处理函数列表
 * failList: 失败处理函数列表
 * done: 注册成功处理函数
 * fail: 注册失败处理函数
 * then: 同时注册成功和失败处理函数
 * always: 一个处理注册到成功和失败，都会调用
 * resolve: 更新state为：RESOLVED，并且执行成功处理队列
 * reject: 更新state为：REJECTED，并且执行失败处理队列
 */
var Promise = (function (){
	function Promise(fn){
		this.state = PENDING;
		this.doneList = [];
		this.failList = [];
		this.fn = fn;
		this.fn(this.resolve.bind(this), this.reject.bind(this))
	}

	var p = {
		done: function (cb){
			if(typeof cb == "function")
				this.doneList.push(cb)
			return this;
		},
		fail: function(cb){
			if(typeof cb == "function")
				this.failList.push(cb);
			return this;
		},
		then: function(success, fail){
			this.done(success || noop).fail(fail || noop)
			return this;
		},
		always: function(cb){
			this.done(cb).fail(cb)
			return this;
		},
		resolve: function(){
			this.state = RESOLVED;
			var lists = this.doneList;
			for(var i = 0, len = lists.length; i<len; i++){
				lists[0].apply(this, arguments);
				lists.shift();
			}
			return this;
		},
		reject: function(){
			this.state = REJECTED;
			var lists = this.failList;
			for(var i = 0, len = lists.length; i<len; i++){
				lists[0].apply(this, arguments);
				lists.shift();
			}
			return this;
		}
	}
	for(var k in p){
		Promise.prototype[k] = p[k]
	}

	return Promise;
})();

function noop(){}
```

使用方式，请到[https://github.com/donglegend/MyPromise](https://github.com/donglegend/MyPromise)下载使用

## [源码下载](https://github.com/donglegend/MyPromise)
