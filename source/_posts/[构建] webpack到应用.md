---
title: webpack 到应用
date: 2018-10-18 00:39:31
tags: [webpack]
categories: 构建
toc: true
---

[ES6项目](#ES6项目)
[TypeScript项目](#TypeScript项目)
[Flow 检查器](#Flow检查器)
[SCSS](#SCSS)
[PostCSS](#PostCSS)
[Vue框架](#Vue框架)

### ES6项目
可以使用.bablerc对ES6、ES7进行配置
```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: ['babel-loader'],
      },
    ]
  },
  // 输出 source-map 方便直接调试 ES6 源码
  devtool: 'source-map'
};

// 安装依赖
// Webpack 接入 Babel 必须依赖的模块
npm i -D babel-core babel-loader 
// 根据你的需求选择不同的 Plugins 或 Presets
npm i -D babel-preset-env
```

### TypeScript项目
Ts 是 Js 的一个超集，主要提供了类型检查系统和对 ES6 语法的支持，但不支持新的 API。
方法一：建立配置编译选项的`tsconfig.json`文件
方法二：集成Webpack
需要解决以下2个问题：
1、通过 Loader 把 Ts 转换成 Js。 推荐 `awesome-typescript-loader`。
  安装：npm i -D typescript awesome-typescript-loader
2、Webpack 在寻找模块对应的文件时需要尝试 ts 后缀，解决办法：修改默认的 `resolve.extensions` 配置项
```js
// tsconfig.json
{
  "compilerOptions": {
    "module": "commonjs", // 编译出的代码采用的模块规范
    "target": "es5", // 编译出的代码采用 ES 的哪个版本
    "sourceMap": true, // 输出 Source Map 方便调试
    "importHelpers": true   // 避免代码冗余
  },
  "exclude": [ // 不编译这些目录里的文件
    "node_modules"
  ]
}

// 集成 Webpack
module.exports = {
  // 执行入口文件
  entry: './main',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, './dist'),
  },
  resolve: {
    extensions: ['.ts', '.js']  // 先尝试 ts 后缀的 TypeScript 源码文件
  },
  module: {
    rules: [
      {
        test: /\.ts$/,
        loader: 'awesome-typescript-loader'
      }
    ]
  },
  devtool: 'source-map',  // 输出 Source Map 方便在浏览器里调试 TypeScript 代码
};
```

### Flow检查器
Flow 是一个 Facebook 开源的 JS 静态类型检测器，它是 JS 语言的超集，在需要的地方加上类型检查。
采用了 Flow 静态类型语法的 JS 是无法直接在目前已有的 JS 引擎中运行，要让代码可以运行需要把这些静态类型语法去掉。
有两种方式可以做到这点：
1、`flow-remove-types` 可单独使用，速度快。
2、`babel-preset-flow` 与 `Babel` 集成。
方法二：采用webpack
```js
// 修改 .babelrc 配置文件，加入 Flow Preset
// 安装 npm i -D babel-preset-flow 依赖到项目
"presets": [
  ...[],
  "flow"
]
```

### SCSS
SCSS 可以让你用更灵活的方式写 CSS。 它是一种 CSS 预处理器，语法和 CSS 相似，但加入了变量、逻辑、等编程元素。
好处：可以方便地管理代码，抽离公共的部分，通过逻辑写出更灵活的代码。
SCSS 又叫 SASS，区别在于 SASS 语法类似 Ruby，而 SCSS 语法类似 CSS。
#### 方法一：
通过 node-sass 编译 .scss 文件  `node-sass main.scss main.css`
#### 方法二：
接入 Webpack：
```js
module.exports = {
  module: {
    rules: [
      {
        // 增加对 SCSS 文件的支持
        test: /\.scss/,
        // SCSS 文件的处理顺序为先 sass-loader 再 css-loader 再 style-loader
        use: ['style-loader', 'css-loader', 'sass-loader'],
      },
    ]
  },
};
```
1、通过 sass-loader 把 SCSS 源码转换为 CSS 代码，再把 CSS 代码交给 css-loader 去处理。
2、css-loader 会找出 CSS 代码中的 @import 和 url() 这样的导入语句，告诉 Webpack 依赖这些资源。同时还支持 CSS Modules、压缩 CSS 等功能。处理完后再把结果交给 style-loader 去处理。
3、style-loader 会把 CSS 代码转换成字符串后，注入到 JS 代码中去，通过 JS 去给 DOM 增加样式。
如果你想把 CSS 代码提取到一个单独的文件而不是和 JS 混在一起，可以使用 `ExtractTextPlugin。`
4、注意：`sass-loader 依赖 node-sass`

### PostCSS
`PostCSS` 是一个 CSS 处理工具，和 SCSS 不同的地方在于它通过插件机制可以灵活的扩展其支持的特性，而不是像 SCSS 那样语法是固定的。
`PostCSS` 的用处非常多，包括**给 CSS 自动加前缀**、**使用下一代 CSS 语法**等
#### 方法一：`postcss.config.js`
```js
module.exports = {
  plugins: [
    // 需要使用的插件列表
    require('postcss-cssnext')    // postcss-cssnext 插件: 使用下一代 CSS 语法编写代码
  ]
}
```
#### 方法一：接入 Webpack
```js
module.exports = {
  module: {
    rules: [
      {
        // 使用 PostCSS 处理 CSS 文件
        test: /\.css/,
        use: ['style-loader', 'css-loader', 'postcss-loader'],
      },
    ]
  },
};
```

### Vue框架
```js
module: {
  rules: [
    {
      test: /\.vue$/,
      use: ['vue-loader'],
    },
  ]
}
// 
```
安装依赖：npm i -D vue-loader css-loader vue-template-compiler。它们的作用分别是：
1、`vue-loader`：解析和转换 .vue 文件，提取出其中的逻辑代码 script、样式代码 style、以及 HTML 模版 template，再分别把它们交给对应的 Loader 去处理。
2、`css-loader`：加载由 vue-loader 提取出的 CSS 代码。
3、`vue-template-compiler`：把 vue-loader 提取出的 HTML 模版编译成对应的可执行的 JS 代码，这和 React 中的 JSX 语法被编译成 JS 代码类似。预先编译好 HTML 模版相对于在浏览器中再去编译 HTML 模版的好处在于性能更好。
