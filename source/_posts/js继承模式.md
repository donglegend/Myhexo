---
title: js继承模式
date: 2017-04-01 11:49:21
categories: javascript
tags: 继承
---

一切的继承都只是为了复用，为了解放劳动力！
然而遗忘是我们的天性，曾经懂得！
<!-- more -->
## 开篇废话
其实很不想写这篇，关于js继承的文章遍地皆是，继承应该是一种思想模式，很多时候我们并不会去使用它，然而我们却必须懂它。我也很少用它，因为大部分直接扩展属性，$.extend()就直接解决了。可能在大型架构，尤其是游戏中，继承是一个很重要的模式，比如精灵类Sprite,说到底，<b style="color: red">一切都是提高代码复用率</b>。

废话还没结束，申明一下，这里针对的 javascript 的继承，假如我们已经理解了js中的 对象  的概念，还知道一个叫做 prototype 的属性的东西。

## \#模式1. 设置原型
```javascript
// 父构造函数
function Parent(name){
	this.name = name || "parent";
}
Parent.prototype.say = function (){
	return this.name;
}
// 子构造函数
function Child(name){}
// 调用继承
extend(Child, Parent);
// 继承实现
function extend(C, P){
	C.prototype  = new P();
}
var c = new Child();
c.say() // parent
```
可以看到，这个模式的继承只有一行代码，<b style="color: red">把父类的实例 赋值 给子类的prototype</b>,然后子类 就可以获取到父类的 属性和方法，<b style="color: green">这是为什么呢？这是什么原理呢？</b>这里需要画一张图：
![](/images/extend/extend1.png)
根据上图，我们来分析：
(1) Child构造函数为null,除了一个隐式属性__proto__
(2) c.say()调用，可是c并没有say方法，但是有原型链
(3) 根据图中的原型链向上找，发现 父类的实例也没有say方法，然后接着向上，找到父类的原型中的say
(4) say中调用了this.name，然后依照上面的顺序重新开始解析name属性
ok，是不是已经明白了，那么接下来我们给子类添加一个name属性：
```
c.name = 'child';
c.say(); //child
```
是不是很好理解了呢。
## \#模式1. 缺点
- 此模式完全继承了父类的 私有属性(this.) 和 原型属性(prototype)
- 无法在子类中传参数
- 如果父类的属性是一个引用类型的，那么子类的修改会影响到父类的对象,demo
```
function Parent(name){
	this.name = name || "parent";
	this.info = ['a', 'b', 'c'];
}
Parent.prototype.say = function (){
	return this.name;
}
var p = new Parent();
function Child(name){}
Child.prototype = p;
var c = new Child();
c.info.push('d');
console.log(p.info); // ["a", "b", "c", "d"]
```

## \#模式2. 借用构造函数
```
function Child(){
	Parent.apply(this, arguments);
}
```
熟悉 apply 和 call的同学都明白上面那行代码发生了什么，子类直接获取了父类对象的属性<b style="color: red;">副本</b>。
记住，副本这个概念很重要。所以只要你愿意，你可以在子类的构造函数中借用n多父类都可以，类似于多重继承。
![原理图](/images/extend/extend2.png)
## \#模式2. 缺点
此种模式有一个致命的缺陷，就是子类和父类相对独立，子类无法获取父类 prototype中的方法和属性，然而一般公用的方法都会放置在 prototype中，不是吗？


## \#模式3. 借用和设置原型
相信聪明的你看到这里，瞬间就明白要做什么了，对，就是模式1和模式2的合体。
```
function Parent(name){
	this.name = name || 'parent';
}
Parent.prototype.say = function (){
	return this.name;
}
function Child(name){
	Parent.apply(this, arguments);
}
Child.prototype = new Parent();
```
## \#模式3. 缺点
父构造函数被调用两次

## \#模式4. 直接共享原型
```
function extend(C, P){
	C.prototype = P.prototype;
}
```
经过上面几种模式的分析，相信聪明的你一眼就能看出此种模式的优劣。造成父类和子类原型强引用，互相影响。

## \#模式5. 借用临时构造函数
我们真正想继承的可能只是prototype里面的方法和属相，而不需要添加到 this的，这里还是用模式1和4的合体.
```
function extend(C, P){
	function F(){};
	F.prototpe = P.prototype;
	C.prototype = new F();
	C.prototype.constructor = C;// 修改构造函数指向
	C.super = P.prototype; //一般没什么用，存储一下父类
}
```

## \#终极模式. 直接拷贝复制