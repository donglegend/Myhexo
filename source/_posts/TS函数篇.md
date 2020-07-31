---
title: TS函数篇
date: 2020-07-23 18:00:04
categories: javascript
tags: TypeScript
---

TypeScript 函数
<!-- more -->

## 函数

基本用法和 es6的 函数并没有什么不同，只是 约束了 参数类型 和 返回值类型。

着重了解一下函数重载

特点：根据不同的参数返回不同类型的数据

```ts

function sum(a: number, b: number): number;
function sum(a: string, b: string): string;
function sum(a: string, b: number): string;
function sum(a: number, b: string): string;
function sum(a: number | string, b: number | string) {
  if (typeof a === "string" || typeof b === "string") {
    return a.toString() + b.toString();
  }
  return a + b
}
```