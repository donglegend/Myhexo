---
title: javascript数据结构-栈
date: 2016-11-07 11:30:19
categories: javascript
tags: 数据结构
---
栈（stack）又名堆栈，它是一种运算受限的线性表。遵循后进先出原则，像垃圾桶似的。
功能实现依然按照增删改查来进行，内部数据存储可以借用语言原生支持的数组。

## 栈类
```
function Stack(){
	this.data = [];
}
```
## 添加数据
数据添加到末尾
```
push: function (element){
	this.data.push(element);
}
```

## 删除数据
从末尾删除
```
pop: function (){
	this.data.pop();
}
```

## 获取数据
返回最后一个添加的
```
peek: function (){
	return this.data[this.size() - 1];
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
function Stack(){
	this.data = [];
}

Stack.prototype = {
	push: function (element){
		this.data.push(element);
	},
	pop: function (){
		this.data.pop();
	},
	peek: function (){
		return this.data[this.data.length - 1];
	},
	isEmpty: function (){
		return this.data.length == 0;
	},
	clear: function (){
		this.data= [];
	},
	size: function (){
		return this.data.length;
	},
	print: function (){
		console.log(this.data.toString());
	}
}
```
下篇看看队列