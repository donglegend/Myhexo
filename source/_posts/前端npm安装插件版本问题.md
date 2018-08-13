---
title: 前端npm安装插件版本问题
date: 2018-06-25 14:35:38
categories: javascript
tags: tool
---

版本带来的混乱

<!-- more -->

## 1. 明确 node 中模块解析路径逻辑

<b>已知模块解析逻辑的可直接跳过</b>
[nodeJs 模块机制 官方文档](http://nodejs.cn/api/modules.html), 这里不多做解释和本文无关的 api 和文档。
为了接下来引发项目中问题的 使用方式，着重说一下 从 node_modules 目录加载 的情况

> 如果传递给 require() 的模块标识符不是一个核心模块，也没有以 '/' 、 '../' 或 './' 开头，则 Node.js 会从当前模块的父目录开始，尝试从它的 /node_modules 目录里加载模块。 Node.js 不会附加 node_modules 到一个已经以 node_modules 结尾的路径上。

> 如果还是没有找到，则移动到再上一层父目录，直到文件系统的根目录。

> 例子，如果在 '/home/ry/projects/foo.js' 文件里调用了 require('bar.js')，则 Node.js 会按以下顺序查找：
>
> /home/ry/projects/node_modules/bar.js
> /home/ry/node_modules/bar.js
> /home/node_modules/bar.js
> /node_modules/bar.js
> 这使得程序本地化它们的依赖，避免它们产生冲突。
>
> 通过在模块名后包含一个路径后缀，可以请求特定的文件或分布式的子模块。 例如，require('example-module/path/to/file') 会把 path/to/file 解析成相对于 example-module 的位置。 后缀路径同样遵循模块的解析语法。

## 2. 明确一下 重复模块的定义

重复模块：名称相同 且 semver 规则兼容
[semver 是什么？？？，请点击这里](https://semver.org/lang/zh-CN/)
版本格式：主版本号.次版本号.修订号，版本号递增规则如下：

主版本号：当你做了不兼容的 API 修改，
次版本号：当你做了向下兼容的功能性新增，
修订号：当你做了向下兼容的问题修正。
先行版本号及版本编译信息可以加到“主版本号.次版本号.修订号”的后面，作为延伸。

## 2. 了解一下 npm install 算法逻辑

### preinstall 前置钩子逻辑处理

### npm install

![npm i 算法逻辑](/images/npmversion/domtree.jpeg)
大概意思如下:

- 加载本地磁盘 node_modules 树，即 依赖树
- 克隆依赖树
- 确定首层一级依赖子树，即 dependencies 和 devDependencies 属性直接指定的模块为一级子树模块
- 循环递归获取模块
- 获取模块安装到本地目录结构，这里有几个注意点
  从 npm 3 版本之后，对于子树依赖的模块版本，如果没有相同的模块产生冲突，那么统统放置到 一级子树中去，
  如果 发生了 <b style="color: red;">模块重复</b>冲突，那么后续的会放置到子树的依赖中，成为嵌套子树。
  这个过程叫做 [npm-dedupe](https://docs.npmjs.com/cli/dedupe),主要用来解决模块冗余，删除多余重复模块的

### 安装模块及运行各个生命周期钩子函数

## 关于 npm config 中的 peerDependencies 字段

没搞明白怎么使用的，好像已经不推荐使用了

> NOTE: npm versions 1 and 2 will automatically install peerDependencies if they are not explicitly depended upon higher in the dependency tree. In the next major version of npm (npm@3), this will no longer be the case. You will receive a warning that the peerDependency is not installed instead. The behavior in npms 1 & 2 was frequently confusing and could easily put you into dependency hell, a situation that npm is designed to avoid as much as possible.

## 结论

尽量使用 npm3 以上版本推荐的 dedupe 原则，结合 semver 使用即可

## 附录

![package.json模块版本](/images/npmversion/version.png)
