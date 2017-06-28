---
title: javascript数据结构-队列
date: 2016-11-07 16:32:48
categories: javascript
tags: 数据结构
---

队列（Queue）是一种特殊的线性表，特殊之处在于它只允许在表的前端（front）进行删除操作，而在表的后端（rear）进行插入操作，和栈一样，队列是一种操作受限制的线性表。进行插入操作的端称为队尾，进行删除操作的端称为队头。

## 队列类
```
function Queue(){
	this.data = [];
}
```
## 添加数据
数据添加到末尾
```
enqueue: function(element) {
	this.data.push(element);
}
```

## 删除数据
从头部删除
```
dequeue: function() {
	this.data.shift();
}
```

## 获取数据
返回第一个
```
front: function() {
	return this.data[0];
}
```
## 是否为空
```
isEmpty: function (){
	return this.data.length == 0;
}
```

## 清空数据
```
clear: function (){
	this.data= [];
}
```

## 数据长度
```
size: function (){
	return this.data.length;
}
```

## 辅助函数，打印数据
```
print: function (){
	console.log(this.data.toString());
}
```
## 完整代码
```
function Queue() {
	this.data = [];
}

Queue.prototype = {
	enqueue: function(element) {
		this.data.push(element);
	},
	dequeue: function() {
		this.data.shift();
	},
	front: function() {
		return this.data[0];
	},
	isEmpty: function() {
		return this.data.length == 0;
	},
	clear: function() {
		this.data = [];
	},
	size: function() {
		return this.data.length;
	},
	print: function() {
		console.log(this.data.toString());
	}
}
```
下面再扩充研究一下  优先队列