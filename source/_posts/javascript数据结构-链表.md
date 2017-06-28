---
title: javascript数据结构-链表
date: 2016-11-08 12:10:18
categories: javascript
tags: 数据结构
---
链表 是一种物理存储单元上非连续、非顺序的存储结构，它既可以表示线性结构，也可以用于表示非线性结构，数据元素的逻辑顺序是通过链表中的指针链接次序实现的。链表由一系列结点（链表中每一个元素称为结点）组成，结点可以在运行时动态生成。每个结点包括两个部分：一个是存储数据元素的数据域，另一个是存储下一个结点地址的指针域。

## 链表类
```
function LinkedList() {
	function Node(element) {
		this.element = element;
		this.next = null;
	}
	var length = 0;
	var head = null;
}
```

## 添加数据(链表尾部)
```
append = function(element) {
	var node = new Node(element);
	if (!head) {
		head = node;
	} else {
		var current = head;
		while (current.next) {
			current = current.next;
		}
		current.next = node;
	}
	length++;
}
```

## 插入数据（任意位置）
```
insert = function(element, position) {
	if(!position || !element){
		return false;
	}

	var current = head,
		previous = null,
		index = 0;

	var node = new Node(element);

	if(position >= 0 && position <= length-1){
		if(position == 0){
			node.next = current;
			head = node;
		}else{
			while(index++ < position){
				previous = current;
				current = current.next;
			}
			node.next = current;
			previous.next = node;
		}
		length++;
	}
}
```

## 删除数据（某个位置）
```
removeAt = function(position) {
	var current = head,
		previous = null,
		index = 0;
	if(position >= 0 && position <= length-1){
		if(position == 0){
			head = current.next;
		}else{
			while(index++ < position){
				previous = current;
				current = current.next;
			}
			previous.next = current.next;
		}
		length--;
		return current.element;
	}
}
```

## 索引查找
```
indexOf = function(element) {
	var current = head,
		index = 0;
	while(current){
		if(element == current.element){
			return index;
		}
		index++;
		current = current.next;
	}
	return -1;

}
```

## 删除数据（按数据）
```
remove = function(element) {
	var index = this.indexOf(element);
	return this.removeAt(index);
}
```

## 是否为空
```
isEmpty = function() {
	return length == 0;
}
```

## 数据大小
```
size = function() {
	return length;
}
```

## 获取第一个节点数据
```
getHead = function() {
	return head.element;
}
```

## toString
```
toString = function() {
	var current = head;
	var s = "";
	while(current){
		s += (","+current.element);
		current = current.next;
	}
	return s.substr(1);
}
```

## 打印，辅助函数
```
print = function() {
	console.log(this.toString())
}
```

## 完整代码
```
function LinkedList() {
	function Node(element) {
		this.element = element;
		this.next = null;
	}
	var length = 0;
	var head = null;

	this.append = function(element) {
		var node = new Node(element);
		if (!head) {
			head = node;
		} else {
			var current = head;
			while (current.next) {
				current = current.next;
			}
			current.next = node;
		}
		length++;
	};
	this.insert = function(element, position) {
		if(!position || !element){
			return false;
		}

		var current = head,
			previous = null,
			index = 0;

		var node = new Node(element);

		if(position >= 0 && position <= length-1){
			if(position == 0){
				node.next = current;
				head = node;
			}else{
				while(index++ < position){
					previous = current;
					current = current.next;
				}
				node.next = current;
				previous.next = node;
			}
			length++;
		}
	};
	this.removeAt = function(position) {
		var current = head,
			previous = null,
			index = 0;
		if(position >= 0 && position <= length-1){
			if(position == 0){
				head = current.next;
			}else{
				while(index++ < position){
					previous = current;
					current = current.next;
				}
				previous.next = current.next;
			}
			length--;
			return current.element;
		}
	};
	this.remove = function(element) {
		var index = this.indexOf(element);
		return this.removeAt(index);
	};
	this.indexOf = function(element) {
		var current = head,
			index = 0;
		while(current){
			if(element == current.element){
				return index;
			}
			index++;
			current = current.next;
		}
		return -1;

	};
	this.isEmpty = function() {
		return length == 0;
	};
	this.size = function() {
		return length;
	};
	this.getHead = function() {
		return head.element;
	};
	this.toString = function() {
		var current = head;
		var s = "";
		while(current){
			s += (","+current.element);
			current = current.next;
		}
		return s.substr(1);
	};
	this.print = function() {
		console.log(this.toString())
	};
}
```