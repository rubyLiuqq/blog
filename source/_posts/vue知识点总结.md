---
title: Vue知识点总结
date: 2018-09-06 10:30:23
tags: [git]
categories: 基础知识
mathjax: true
toc: true
---

### 1、Vue nextTick 使用原因
Vue批量挂起DOM更新并异步应用它们以防止由多个数据突变导致的不必要的重新呈现。
在实践中我们经常不得不Vue.nextTick等待Vue在我们触发某些状态更改之后执行实际的DOM更新。

### 2、Vue实现数据双向绑定的原理：Object.defineProperty()