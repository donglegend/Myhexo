---
title: javascript数据结构-数组
date: 2016-11-06 15:28:08
categories: javascript
tags: 数据结构
---
## 简介
数组是最简单的数据结构，javascript语言也很早就原声支持了数组Array数据类型，和其他语言略微不同的是：<b style="color: red;">js中的数组可以存储不同类型的值</b>,看起来很厉害的样子，但是推荐不要这么做。

由于javascript语言本身已经实现了数组数据类型，而且实现了很多方便实用的方法功能，所以这里只做复习和解释。

## 创建数组
创建 Array 对象的语法：
```
new Array(); //创建空数组，length 为 0
new Array(size); // size表示 期望的数组元素个数
new Array(element0, element1, ..., elementn); //element 参数列表，初始化为数组值
```
**返回值**
返回新创建并被初始化了的数组。

## [方法功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
| 方法名        | 描述           |
| ------------- |---------------|
| concat()     | 连接两个或更多的数组，并返回结果。 |
| join()      | 把数组的所有元素放入一个字符串。元素通过指定的分隔符进行分隔。      |
| pop() | 删除并返回数组的最后一个元素|
| push() | 向数组的末尾添加一个或更多元素，并返回新的长度。|
| reverse() | 颠倒数组中元素的顺序。|
| shift() | 删除并返回数组的第一个元素|
| slice() | 从某个已有的数组返回选定的元素|
| sort() | 对数组的元素进行排序|
| splice() | 删除元素，并向数组添加新元素。|
| unshift() | 向数组的开头添加一个或更多元素，并返回新的长度。|
| every() | 测试数组的所有元素是否都通过了指定函数的测试。|
| some() | 测试数组中的某些元素是否通过了指定函数的测试。|
| filter() | 使用指定的函数测试所有元素，并创建一个包含所有通过测试的元素的新数组|
| forEach() | 让数组的每一项都执行一次给定的函数。|
| map() | 返回一个由原数组中的每个元素调用一个指定方法后的返回值组成的新数组|
| indexOf() | 返回给定元素能找在数组中找到的第一个索引值，否则返回-1。|
| ... | ...|
[还有很多，请参考官方文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
