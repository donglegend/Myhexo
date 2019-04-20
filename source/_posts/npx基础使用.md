---
title: npx基础使用
date: 2019-04-12 11:19:36
categories: javascript
tags: tool
---

npx: 快速运行包内模块命令

<!-- more -->

[npx 官方链接](https://www.npmjs.com/package/npx)

## npx 是什么

npx 是 npm(5.2 版本后引入)提供的一个工具，目的是为了解决 执行包内安装（局部安装）的模块的命令行工具.
官方解释：npx(1) -- execute npm package binaries。

仔细想想，局部安装了一个模块，想调用命令怎么办呢？常用的两种方法：

- 在 package.json 的 scripts 字段里面执行
- 在脚本里 import 引入，执行

还有一种就是手动找到 bin 文件执行路径：

```javascript
node_modules/.bin/xxx --version
```

npx 就是为了解决这个问题产生的。
可以这么用：

```javascript
npx xxx --version
```

## npx 工作原理

npx 实现机制比较简单，就是自动寻找可执行命令程序，大概步骤：

1. 到 node_modules/.bin 里查找
2. 如果上一步没找到，就到系统变量\$PATH 里查找
3. 如果上一步还没招到，就会到远程查找下载。（备注：这个比较有趣，自动下载，使用完毕后会自动删除，不会污染全局环境）
   其他功能及其使用方法，请参考文档。

## 总结

- 自动执行依赖包的命令，操作简单
- 如有必要，临时下载安装，完成使用后，自动删除。
