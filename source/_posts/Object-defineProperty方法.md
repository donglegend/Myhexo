title: Object.defineProperty方法
date: 2016-01-18 18:02:11
categories: javascript
tags: property
---
偶然发现有人在用 Object.defineProperty方法对js对象进行扩展属性，就简单研究了一下，发现这个是ECMAScript5.1（ECMA-262）中定义了标准属性定义的标准属性，在此简单做下笔录。
<!--more-->
## *本文旨在说明如何用 Object.defineProperty方法 获取和设置属性*

### property属性详解
 javascript中对象的property有三个属性：
 1. writable。该property是否可写。
 2. enumerable。当使用for/in语句时，该property是否会被枚举。
 3. configurable。该property的属性是否可以修改，property是否可以删除。

在ECMAScript 3标准中，上面三个属性的值均为true且不可改：新建对象的property是可写的、可被枚举的、可删除的；而在ECMAScript 5标准中，可通过property的描述对象(property descriptor)来对这些属性进行配置和修改。

如果将property的值信息也作为property的属性来看的话，对象中的property拥有四个属性：value、writable、enumerable和configurable。


### 获取property的属性信息
 ECMAScript 5标准中，可以通过[Object.getOwnPropertyDescriptor()][1]来获取对象自身某个property的属性信息
 ``` javascript
var obj = {
	"name": "donglegend"
}
console.log(Object.getOwnPropertyDescriptor(obj, "name"));
// configurable: true  enumerable: true  value: "donglegend"  writable: true
 ```

### 设置property的属性信息
 ECMAScript 5标准中，可以通过[Object.defineProperty()][2]来设置对象自身某个property的属性：
Object.defineProperty(obj, prop, descriptor)
- obj, 待修改的对象
- prop, 待修改的属性名称
- descriptor, 待续改属性的相关描述
```
/**
 * @{param} descriptor
 */
 
{
    configurable: false,
    enumerable: false,
    writable: false,
    value: null,
    set: undefined,
    get: undefined
}
```
1. configurable ，属性是否可配置。可配置的含义包括：是否可以删除属性（delete ），是否可以修改属性的 writable 、 enumerable 、configurable 属性。
2. enumerable ，属性是否可枚举。可枚举的含义包括：是否可以通过for...in 遍历到，是否可以通过 Object.keys() 方法获取属性名称。
3. writable ，属性是否可重写。可重写的含义包括：是否可以对属性进行重新赋值。
4. value ，属性的默认值。
5. set ，属性的重写器（暂且这么叫）。一旦属性被重新赋值，此方法被自动调用。
6. get ，属性的读取器（暂且这么叫）。一旦属性被访问读取，此方法被自动调用。

简单赋值就不列举了，在此说下常用的 通过set和get方法设置的用法：
```
Object.defineProperty(Array.prototype, "top", {
	get: function (){
		return this[this.length -1];
	},
	set: function (val){
		this[this.length -1] = val;
	}
});
var arr = [1,2,3,4,5];
console.log(arr.top);  // 5
arr.top = 10;
console.log(arr.top);  // 10
```
**注意**:  [Object.defineProperty()][2] 方法设置属性时，属性不能同时声明访问器属性（ set 和 get ）和 writable 或者 value 属性。 意思就是，某个属性设置了 writable 或者 value 属性，那么这个属性就不能声明 get 和 set 了，反之亦然。简单说就是不允许同一个属性出现两种以上存取访问控制


通过封装set，get访问器属性的特性可以用来进行数据双向绑定的开发，比如avalon.js内部实现方式的本质是设置 属性访问器 


[1]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyDescriptor
[2]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty