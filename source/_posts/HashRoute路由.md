---
title: HashRoute路由
date: 2016-09-22 17:30:30
categories: javascript
tags: hash
---
无刷新页面，切换视图，用hash 实现路由切换，本身附带history记录，简单舒服。
<!--more-->
最近用vue，看到vue-route的路由，做单页应用切换视图真心易如反掌，分分钟爽到不行。为了加深理解其内涵原理，遂决意写一个最简单的hash 路由。

思路很简单，自己维护一个 hash route 的哈希表，通过注册路由及其处理事件，通过hashchange来触发对应事件处理,有点像观察者模式的思想，先注册，再派发。

## 实现方法
* maps 批量注册
* add 单条注册
* remove 单条删除
* clear 全部清空
* go 跳转指定
* redirect 重定向

## 使用
```
function h(){}
HashRoute
	.maps({
	    home: h,
	    list: h
	})
	.add('info', h)
	.remove('info')
	.clear()
	.go()
```

## [demo效果展示](http://donglegend.com/effects/hashRouter/index.html)

## [源码下载](https://github.com/donglegend/hashRouter)
