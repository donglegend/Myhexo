---
title: javascript数据结构-优先队列
date: 2016-11-07 16:54:27
categories: javascript
tags: 数据结构
---
这里之所以扩充一个 **有限队列** 是因为，生活使用中队列通常会附加优先级，比如排队买票，一般老人和军人等会有优先权限。

实现：继承上篇的 普通队列实现。这里用一种方法，入队的时候，进行排序插入到指定位置，输出不变。

## 优先队列类
```
//继承自 Queue
function PriorityQueue(){
	Queue.call(this);
}
```
## 继承原型方法
```
function base(p, c){
	var h = {}, P = p.prototype, C = c.prototype;
	for(var k in C){
		h[k] = 1;
	}
	for(var k in P){
		if(!h[k]){
			C[k] = P[k];
		}
	}
}
base(Queue, PriorityQueue);
```

## 添加数据
```
// 增加一个节点类
function Node(element, priority){
	this.element = element;
	this.priority = priority;
}
// 更新添加
PriorityQueue.prototype.enqueue = function (element, priority){
	var node = new Node(element, priority);
	if(this.isEmpty()){
		this.data.push(node);
	}else{
		var add = false;
		for(var i = 0,len=this.data.length; i<len; i++){
			if(node.priority < this.data[i].priority){
				this.data.splice(i, 0, node);
				add = true;
				break;
			}
		}
		if(!add){
			this.data.push(node);
		}
	}
}
```

## 更新print方法
```
PriorityQueue.prototype.print = function (){
	console.dir(this.data)
}
```

## 完整代码
```
function PriorityQueue(){
	Queue.call(this);
}
function base(p, c){
	var h = {}, P = p.prototype, C = c.prototype;
	for(var k in C){
		h[k] = 1;
	}
	for(var k in P){
		if(!h[k]){
			C[k] = P[k];
		}
	}
}
base(Queue, PriorityQueue);
function Node(element, priority){
	this.element = element;
	this.priority = priority;
}
PriorityQueue.prototype.enqueue = function (element, priority){
	var node = new Node(element, priority);
	if(this.isEmpty()){
		this.data.push(node);
	}else{
		var add = false;
		for(var i = 0,len=this.data.length; i<len; i++){
			if(node.priority < this.data[i].priority){
				this.data.splice(i, 0, node);
				add = true;
				break;
			}
		}
		if(!add){
			this.data.push(node);
		}
	}
}
PriorityQueue.prototype.print = function (){
	console.dir(this.data)
}
```
下面研究链表