---
title: webpack进阶
date: 2018-09-28 14:09:21
tags: [webpack]
categories: 构建
toc: true
---

## 配置分离
在大型项目中，webpack.config.js 会变得越来越臃肿，因此可以利用 webpack-merge 插件。将配置定义在一个目录下面的不同文件中，然后通过 webpack-merge 来合并成最终的配置。

## code splitting 异步加载