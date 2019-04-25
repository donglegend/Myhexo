---
title: babel7使用分析
date: 2019-04-25 10:41:00
categories: javascript
tags: babel
---

babel7 重新理解

<!-- more -->

## 测试环境

node 10.14.1
Babel 7.4.3

## Babel 是什么？

Babel 是一个工具链，主要用于将 ECMAScript2015+版本的代码转换为向后兼容的 Javascript 代码，以便能够运行在当前和旧版本的浏览器或其他环境中。

## Babel 主要功能点：

- 语法转换
- 垫片兼容处理，通过 Polyfill 方式在目标环境中添加缺失的特性
- 源码转换
- 其他...

## 使用理念

1. Babel 主要通过 插件 plugins 的形式 达到转换代码的目的。
2. Babel 本身内置了部分环境预设 preset-env,当然项目中 需要根据实际 进行配置。
3. 为了方便书写参数，一般通过 配置文件的方式 babel.config.js 或者.babelrc.js(以编程的方式创建配置)或者.bablerc

## Babel 编译两大核心

    - 语法转换
    - 垫片支持

## Bable 核心模块

### @babel/core

Babel 语法转换核心功能，内置 helpers 插件模块，是语法转换的主要辅助工具

### @babel/preset-env

这是一个非常智能的环境预设模块，根据 env 配置自动 分析查找对应的 helper 和 plugins 进行代码编译转换
基本使用：

```js
presets: [
  [
    '@babel/preset-env',
    {
      targets: {
        chrome: '77',
        android: '2',
      },
      debug: true,
      useBuiltIns: false,
    },
  ],
],
```

其他参数参考文档配置就好了，这里重点分析一下 useBuiltIns 属性.
targets 可根据文档自行配置，这个比较简单。
debug 调试模式，建议开启，开启之后可以看到 那些 target 使用了那些 plugins 和 polyfill

```js
// 开启debug 模式的构建信息
Using targets:
{
  "android": "2",
  "chrome": "77"
}

Using modules transform: auto

Using plugins:
  transform-template-literals { "android":"2" }
  transform-literals { "android":"2" }
  transform-function-name { "android":"2" }
  transform-arrow-functions { "android":"2" }
  ......
  ......

Using polyfills with `usage` option:

[/Users/weng/Documents/dongsheng/projects/bable/src/app.js] Added following core-js polyfills:
  es6.object.set-prototype-of { "android":"2" }
  es6.object.create { "android":"2" }
  es6.symbol { "android":"2" }
  es7.symbol.async-iterator { "android":"2" }

[/Users/weng/Documents/dongsheng/projects/bable/src/app.js] Added following core-js polyfills:
  es6.string.includes { "android":"2" }
  es7.array.includes { "android":"2" }
  es6.array.of { "android":"2" }
```

useBuiltIns 属性使用：

- false 不开启 polyfill 处理，只做语法代码转换。
- usage 开启 polyfill 处理（依赖@babel/polyfill 模块）
- entry 开启 polyfill 处理（依赖@babel/polyfill 模块）
  useBuiltIns 的值只要不为 false,就不启动 polyfill 兼容，其他值都会引入 polyfill，存在全量变量污染的特性。

### @babel/plugin-transform-runtime & @babel/runtime & @babel/runtime-corejs2

正常情况下，每个文件都有局部引入自身的 helpers 函数实现，打包后放置在文件的最顶部。
所以存在一个情况，多个文件使用了同样的语法，那么同样的 helpers 会多次引入。
transform-runtime 就是解决这个问题的。
transform-runtime 为了减少代码量，引入的 helpers 只保留一份
看个栗子：
源代码:

```js
//app.js
import { add } from './add';
function app() {
  add(1, 2);
  console.log(Object.assign({}, { ...{ name: 'd' } }));
  const _a = Array.of(3, 11, 8);
}
app();
```

```js
export function add(a, b) {
  console.log('加法:');
  console.log(Object.assign({}, { ...a }));
}
```

```
// 不启动 transform-runtime 构建后：
// 包含了多个同样的 helpers
```

![不启动transform-runtime](/images/babel/babel_no_transform_runtime.png)

```js
plugins: ['@babel/plugin-transform-runtime'];
// 同样的helpers只加载一次,但是没有 方法api的实现
```

![启动transform-runtime](/images/babel/babel_transform_runtime.png)

```js
// corejs设置版本号：2或者3，都会引入非实例方法api的兼容实现
plugins: [
  [
    '@babel/plugin-transform-runtime',
    {
      corejs: 2,
    },
  ],
];
```

![启动transform-runtime-corejs2](/images/babel/babel_transform_runtime_corejs.png)

transform-runtime 也是做兼容的:

- @babel/runtime 只做 语法转换的(helpers 和 regenerator),只做语法转换, 没有新 api 的实现
- @balel/runtime-corejs2 除了 helpers 和 regenarator ，还有 core-js 垫片兼容实现,替换 非实例方法

prest-env 影响 语法转换 和 垫片兼容
而且自身还兼容了 默认的 代码转换功能，根据具体 env 分析要使用的 plugins

语法转换 和 垫片兼容 是两个 独立功能，只不过都是 根据 env 来 实现目标转换.
