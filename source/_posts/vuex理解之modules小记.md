---
title: vuex理解之modules小记
date: 2017-05-11 14:31:00
categories: javascript
tags: vue
---

好记性不如烂笔头

<!-- more -->

## [demo预览](http://donglegend.com/effects/vuex-study/dist/) 
## [源代码](https://github.com/donglegend/vuex-study)

## 前情提要
关于vuex，其实很久以前就研究使用过，还研究过 flux，redux之类的体系，当时感觉对于 state，action，dispatch，views等这套体系流程已经很了解，结果呢近一年不用，再次证明了人类大脑的局限性。这里不做任何文档教程，因为官网文档已经很详细，这里只谈理解和注意点。

## vuex核心
五大核心属性：
- State	
- Getters
- Mutations
- Actions
- Modules

## 四大辅助函数
- mapState
- mapGetters
- mapActions
- mapMutations

## 运行流程

- State是数据data的存储仓库，可划分为子模块module，每个module有自立的上下文对象context，有自己的state、mutation、action、getters、modules等等体系

- Getters 主要用来从State中获取数据，优点是具有通用性

- Mutations 是用来更新state状态仓库里的数据的，唯一更改数据地方，实时同步修改，<b style="color: red">不能异步,不能异步,不能异步</b>

- Actions 主要补充增加 异步更新 state仓库数据的功能，内部依然是触发Mutation来实现

- Modules 为了划分state模块，便于管理数据仓库

注意：<b style="color: red">Getters，Mutations，Actions无论是不是在module块中，都会直接挂载到 vuex的实例 store上，只有state保持链式命名空间，比如，store.state.module[key]等等,所以如果是子模块的state，直接用辅助函数mapState是获取不到的.</b>

