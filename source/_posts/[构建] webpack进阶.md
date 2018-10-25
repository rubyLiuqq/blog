---
title: webpack 进阶
date: 2018-09-28 14:09:21
tags: [webpack]
categories: 构建
toc: true
---

在使用 webpack 构建的典型应用程序或站点中，有三种主要的代码类型：
1、团队编写的源码。
2、源码会依赖的任何第三方的 library 或 "vendor" 代码。
3、webpack 的 runtime 和 manifest，管理所有模块的交互。
runtime 包含：在模块交互时，连接模块所需的加载和解析逻辑。包括浏览器中的已加载模块的连接，以及懒加载模块的执行逻辑。
通过使用 manifest 中的数据，runtime 将能够查询模块标识符，检索出背后对应的模块。
import 或 require 语句现在都已经转换为 `__webpack_require__` 方法，此方法指向模块标识符(module identifier)

## 配置分离
在大型项目中，webpack.config.js 会变得越来越臃肿，因此可以利用 webpack-merge 插件。将配置定义在一个目录下面的不同文件中，然后通过 webpack-merge 来合并成最终的配置。

## code splitting 异步加载