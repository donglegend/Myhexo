---
title: TS基础类型篇
date: 2020-07-23 17:56:10
categories: javascript
tags: TypeScript
---


TypeScript 基础类型
<!-- more -->

## Boolean
```ts
let isDone: boolean = false;
```

## Number
```ts
let num: number = 10;
```

## String
```ts
let name: string = "bob";
```

## Symbol
自ECMAScript 2015起，symbol成为了一种新的原生类型，就像number和string一样

特点：不可变且唯一

用途：声明对象属性或类成员

```ts
let sym2 = Symbol("key");
let sym3 = Symbol("key");
sym2 === sym3; // false, symbols是唯一的
```
内置Symbols

## Array
两种写法：
```ts
let list: number[] = [1, 2, 3];
```
```ts
// 数组泛型
let list: Array<number> = [1, 2, 3];
```

## Tuple

```ts
// Declare a tuple type
let x: [string, number];
// Initialize it
x = ['hello', 10];
```

## Enum
数字枚举
```ts
enum Color { 
    Red = 1, 
    Green, 
    Blue 
}
// 编译结果
var Color;
(function (Color) {
    Color[Color["Red"] = 1] = "Red";
    Color[Color["Green"] = 2] = "Green";
    Color[Color["Blue"] = 3] = "Blue";
})(Color || (Color = {}));
```

字符串枚举
```ts
enum Color {
  Red = "red", 
  Green = "green", 
  Blue = 'blue'
}
// 编译结果
var Color;
(function (Color) {
    Color["Red"] = "red";
    Color["Green"] = "green";
    Color["Blue"] = "blue";
})(Color || (Color = {}));
```

## Any
类型逃逸仓 顶级类型
```ts
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false;
```

## Void
没有任何类型
```ts
function warnUser(): void {
    console.log("This is my warning message");
}
```

## Null & Undefined
默认情况下null和undefined是所有类型的子类型。 就是说你可以把 null和undefined赋值给number类型的变量。

然而，当你指定了 **--strictNullChecks** 标记，null和undefined只能赋值给void和它们各自。

## Never
never类型表示的是那些永不存在的值的类型。 
```ts
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}
// 返回never的函数必须存在无法达到的终点
function infiniteLoop(): never {
    while (true) {
    }
}
```
一般没什么用，大部分时候用这个特性来做全面的属性检查
```ts
function foo(x: string | number): boolean {
  if (typeof x === 'string') {
    return true;
  } else if (typeof x === 'number') {
    return false;
  }
  return fail('Unexhaustive')
}
function fail(message: string): never {
  throw new Error(message);
}
```