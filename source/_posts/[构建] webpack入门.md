---
title: webpack 入门
date: 2018-09-28 11:42:48
tags: [webpack]
categories: 构建
toc: true
---

## 论述
`CommonJS` 是一种使用广泛的 JavaScript 模块化规范，核心思想是通过 require 方法来同步地加载依赖的其他模块，通过 module.exports 导出需要暴露的接口。
`CommonJS` 的优点在于：
- 代码可复用于 Node.js 环境下并运行，例如做同构应用；
- 通过 NPM 发布的很多第三方模块都采用了 CommonJS 规范。

`CommonJS` 的缺点在于这样的代码无法直接运行在浏览器环境下，必须通过工具转换成标准的 ES5。

`AMD` 也是一种 JavaScript 模块化规范，与 CommonJS 最大的不同在于它采用异步的方式去加载依赖的模块。 AMD 规范主要是为了解决针对浏览器环境的模块化问题，最具代表性的实现是 requirejs。
`AMD` 的优点在于：
- 可在不转换代码的情况下直接在浏览器中运行；
- 可异步加载依赖；
- 可并行加载多个依赖；
- 代码可运行在浏览器环境和 Node.js 环境下。

`AMD` 的缺点在于JavaScript 运行环境没有原生支持 AMD，需要先导入实现了 AMD 的库后才能正常使用。

`TypeScript` 的缺点在于语法相对于 JavaScript 更加啰嗦，并且无法直接运行在浏览器或 Node.js 环境下。
`Flow` 也是 JavaScript 的一个超集，它的主要特点是为 JavaScript 提供静态类型检查，和 TypeScript 相似但更灵活，可以让你只在需要的地方加上类型检查。

## 构建工具
构建就是做这件事情，把源代码转换成发布到线上的可执行 JavaScrip、CSS、HTML 代码，包括如下内容。
- 代码转换：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等。
- 文件优化：压缩 JavaScript、CSS、HTML 代码，压缩合并图片等。
- 代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载。
- 模块合并：在采用模块化的项目里会有很多个模块和文件，需要构建功能把模块分类合并成一个文件。
- 自动刷新：监听本地源代码的变化，自动重新构建、刷新浏览器。
- 代码校验：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过。
- 自动发布：更新完代码后，自动构建出线上发布代码并传输给发布系统。

## webpack配置
webpack官方提供的配置方法是通过module.exports返回一个json，但是这种场景不灵活，不能适配多种场景。
最佳方式：
1、两份配置文件webpack.config.production.js/webpack.config.development.js，然后不同场景下，使用不同的配置文件。
2、通过module.exports返回函数，该函数能接受参数。

```js
module.exports = function(env) {
  return {
    context: config.context,
    entry: config.src,
    output: {
      path: path.join(config.jsDest, project),
      filename: '[name].js',
      chunkFilename: '[name].[chunkhash:8].js',
      publicPath: '/assets/' + project + '/'
    },
    devtool: "eval",
    watch: false,
    profile: true,
    cache: true,
    module: {
      loaders: getLoaders(env)
    },
    resolve: {
      alias: getAlias(env)
    },
    plugins: getPlugins(env)
  };
}
```
`context`：上下文。
`entry`：入口文件，是所有依赖关系的入口，webpack从这个入口开始静态解析，分析模块之间的依赖关系。
`output`：打包输出的配置。
`devtools`：SourceMap选项，便于开发模式下调试。
`watch`：监听模式，增量更新，开发必备！
`profile`：优化。
`cache`：webpack构建的过程中会生成很多临时的文件，打开cache可以让这些临时的文件缓存起来，从而更快的构建。
`module.loaders`：loaders用来对文件做预处理。这样webpack就可以打包任何静态文件。
`resolve.alias`：模块别名，这样可以更方便的引用模块。
`plugins`：webpack的一些内置功能均是以插件的形式提供。

## webpack 使用
#### 1、命令行调用
webpack可以在终端中使用：
- {extry file}填写入口文件的路径
- {destination for bundled file}处填写打包文件的存放路径
- 填写路径的时候不用添加{}
`webpack {entry file}  {destination for bundled file}`  （全局模式）
`node_modules/.bin/webpack  app/main.js  public/bundle.js` （非全局安装的情况）

#### 2、入口与输出
模板
```js
{
  entry: [String | Array | Object], // 入口模块
  output: {
    path: String,                   // 输出路径
    filename: String                // 输出名称或名称 pattern
    publicPath: String              // 指定静态资源的位置
    ...                             // 其他配置
  }
}
```
1. 多个入口文件：eg: `entry: ['./src/index.js', './vendor/bootstrap.min.js']` 一个入口，多个文件
最终 bootstrap 会被追加到打包好的 index.js 中，数组中的最后一个会被 export。

2. 多个打包目标
```js
{
  entry: {
    index: './src/index.js',
    a: './src/a.js'
  },
  output: {
    path: './dist/',
    filename: '[name].js' 
  }
}
```

#### 3、生成Source Maps（使调试更容易）——  devtool

webpack就可以在打包时为我们生成的source maps，提供了一种对应编译文件和源文件的方法，使得编译后的代码可读性更高，也更容易调试。
在webpack的配置文件中配置source maps，需要配置devtool，有四种不同的配置选项：
- `source-map`：在一个单独的文件中产生一个完整且功能完全的文件，但它会减慢打包速度
- `cheap-module-source-map`：在一个单独的文件中生成一个不带列映射的map，不带列映射提高了打包速度，但不能对应到具体的列（符号），会对调试造成不便；
- `eval-source-map`：使用eval打包源文件模块，在同一个文件中生成干净的完整的source map。其可以在不影响构建速度的前提下生成完整的sourcemap，但对打包后输出的JS文件的执行具有性能和安全的隐患。在开发阶段这是一个非常好的选项，在生产阶段则一定不要启用这个选项；
- `cheap-module-eval-source-map`：在打包文件时最快的生成source map的方法，生成的Source Map 会和打包后的JavaScript文件同行显示，没有列映射，和eval-source-map选项具有相似的缺点。（cheap-module-eval-source-map方法构建速度更快，但是不利于调试，推荐在大型项目考虑时间成本时使用。）

```js
module.exports = {
  entry: __dirname + '/app/index.js',
  output: {
    path: __dirname + '/public',
    filename: 'bundle-[hash:7].js'
  },
  devtool: 'eval-source-map'
}
```

#### 4、使用webpack构建本地服务器（webpack-dev-server）—— devServer

背景：浏览器监听代码的修改，并自动刷新显示修改后的结果
Webpack提供一个可选的本地开发服务器，该本地服务器基于node.js构建
先安装：npm install --save-dev webpack-dev-server
再配置: devserver
- `contentBase`：默认webpack-dev-server会为根文件夹提供本地服务器，如果想为另外一个目录下的文件提供本地服务器，应该在这里设置其所在目录
- `port`：设置默认监听端口，默认“8080”；
- `inline`：设置为true，当源文件改变时会自动刷新页面；
- `historyApiFallback`：若设置true，所有的跳转将指向index.html，在单页应用时有用，它依赖于HTML5 history API

```js
module.exports = {
  entry: __dirname + '/app/index.js',
  output: {
    path: __dirname + '/public',
    filename: 'bundle-[hash:7].js'
  },
  devtool: 'eval-source-map',
  devServer: {
    contentBase: './public',    // 本地服务器所加载的页面所在的目录
    historyApiFallback: true,   // 不跳转
    inline: true                // 实时刷新
  }
}
```

#### 5、loader
使用不同的loader，webpack有能力调用外部的脚本或工具，实现对不同格式的文件的处理
在 webpack 中，通过 loader 可以实现 JSX 、Es6、CoffeeScript 等的转换.
- 分析转换scss为css
- 把下一代的JS文件（ES6，ES7)转换为现代浏览器兼容的JS文件
- 把React的中用到的JSX文件转换为JS文件
- ......
Loader需要单独安装，并在config.js 中的 modules关键字中进行配置
Loader 的配置说明：
- `test`：一个用以匹配loaders所处理文件的拓展名的正则表达式（必须）
- `loader`：loader的名称（必须）
- `include/exclude`:手动添加必须处理的文件/文件夹或屏蔽不需要处理的文件/文件夹（可选）
- `query`：为loaders提供额外的设置选项（可选）

**loader 除了做文件转换以外，还可以创建额外的文件**
```js
module: {
  rules: [
    // template loaders
    {
      test: /\.(tpl|html)$/,
      use: 'html-loader',
    },
    {
      test: /\.art$/,
      use: 'art-template-loader',
    },
    {
      test: /\.(hbs|handlebars)$/,
      use: 'handlebars-loader',
    },
    // picture loader
    {
      test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
      use: {
        loader: 'url-loader',
        query: {
          limit: 8192,
          name: 'img/[name].[ext]',
        },
      },
    },
    // font loader
    {
      test: /(\.(woff2?|eot|ttf|otf)|font.*\.svg)(\?.*)?$/,
      use: {
        loader: 'url-loader',
        query: {
          limit: 8192,
          name: 'fonts/[name].[ext]',
        },
      },
    },
    // media file loader
    {
      test: /\.(mp4|3gp|avi|mkv|wmv|mpg|vob|flv|swf|mov|rmvb|mp3|wma|wav|aac|ogg|flac|ape|m4a)$/,
      use: {
        loader: 'url-loader',
        query: {
          limit: 8192,
          name: 'media/[name].[ext]',
        },
      },
    },
  ],
}
```

#### 6、Babel
作用：是一个编译 JS 的平台：
- 使用下一代的 JS 代码（ES6，ES7...），即使这些标准目前并未被当前的浏览器完全的支持；
- 使用基于 JS 进行了拓展的语言，比如React的JSX；
安装：`npm install --save-dev  babel-core  babel-loader  babel-preset-es2015  babel-preset-react`
Babel其实是几个模块化的包，其核心功能位于称为`babel-core`的npm包中，webpack可以把其不同的包整合在一起使用，对于每一个需要的功能或拓展，你都需要安装单独的包（用得最多的是解析ES6的`babel-preset-es2015`包和解析JSX的`babel-preset-react`包）。

#### 7、Babel的配置
Babel 可以完全在`webpack.config.js `中进行配置，但复杂度增加时会单独有`.babelrc `的配置文件
webpack会自动调用`.babelrc`里的babel配置选项

```js
module.exports = {
  entry: __dirname + '/app/index.js',
  output: {
    path: __dirname + '/public',
    filename: 'bundle-[hash:7].js'
  },
  devtool: 'eval-source-map',
  devServer: {
    contentBase: './public',    // 本地服务器所加载的页面所在的目录
    historyApiFallback: true,   // 不跳转
    inline: true                // 实时刷新
  },
  module: {
    rules: [{
      test: /(\.jsx|\.js)/,
      use: {
        loader: 'babel-loader',
      },
      exclude: /node-module/
    }]
  }
}

// .babelrc
{
  persets: ['es2015', 'react', 'stage-0']
}
```

#### 8、CSS module 
CSS modules 的技术：把JS的模块化思想带入CSS中来，通过CSS模块，所有的类名，动画名默认都只作用于当前模块。
Webpack从一开始就对CSS模块化提供了支持，在CSS loader中进行配置后。
首先把”modules“传递到所需要的地方
然后就可以直接把CSS的类名传递到组件的代码中
好处：这样做只对当前组件有效，不必担心在不同的模块中使用相同的类名造成冲突。

#### 9、CSS预处理器 / PostCSS / autoprefixer
`Sass` 和 `Less` 之类的预处理器是对原生CSS的拓展，它们允许使用类似于variables, nesting, mixins, inheritance等不存在于CSS中的特性来写CSS。
在webpack里使用loader进行配置：`less-loader、sass-loader、style-loader`

PostCSS 是一个允许使用 JS 插件转换样式的工具。 这些插件可以检查（lint）你的 CSS，支持 CSS Variables 和 Mixins，CSS 自动加前缀、使用下一代 CSS 语法等.

```js
module.exports = {
  entry: __dirname + '/app/index.js',
  output: {
    path: __dirname + '/public',
    filename: 'bundle-[hash:7].js'
  },
  devtool: 'eval-source-map',
  devServer: {
    contentBase: './public',    // 本地服务器所加载的页面所在的目录
    historyApiFallback: true,   // 不跳转
    inline: true                // 实时刷新
  },
  module: {
    rules: [{
      test: /(\.jsx|\.js)/,
      use: {
        loader: 'babel-loader',
      },
      exclude: /node-module/
    }, {
      test: /\.css$/,
      use: ['style-loader', 'css-loader', 'postcss-loader']
    }]
  }
}

// postcss.config.js
module.exports = {
  plugins: [
    // 需要使用的插件列表
    require('postcss-cssnext')    // 使用下一代 CSS 语法编写代码
  ]
}
```

#### 10、插件（Plugins）— banner-plugins / HtmlWebpackPlugin / Hot Module Replacement
插件（Plugins）是用来拓展Webpack功能，会在整个构建过程中生效，执行相关的任务。
loaders是在打包构建过程中用来处理源文件的（JSX，Scss，Less..），一次处理一个，
插件并不直接操作单个文件，它直接对整个构建过程其作用。

##### 1.banner-plugins: 添加版权声明
##### 2.HtmlWebpackPlugin（动态生成入口 html）
依据一个简单的index.html模板，生成一个自动引用你打包后的JS文件的新index.html

##### 3.Hot Module Replacement: 允许修改组件代码后，自动刷新实时预览修改后的效果。
Hot Module Replacement（HMR）允许修改组件代码后，自动刷新实时预览修改后的效果。
配置：
1、在webpack配置文件中添加HMR插件；
2、在Webpack Dev Server中添加“hot”参数；
不过配置完后，JS模块其实还是不能自动热加载的，还需要在JS模块中执行一个Webpack提供的API才能实现热加载。
虽然这个API不难使用，但是如果是React模块，使用Babel可以更方便的实现功能热加载。
思路：
- HMR是一个webpack插件，它能在浏览器中实时观察模块修改后的效果，但如果想让它工作，需要对模块进行额外的配置；
- Babel 有一个叫做react-transform-hrm 的插件，可以在不对React模块进行额外的配置的前提下让HMR正常工作；


#### 11、优化插件
- `OccurenceOrderPlugin`:为组件分配ID，通过这个插件webpack可以分析和优先考虑使用最多的模块，并为它们分配最小的ID（内置插件）
- `UglifyJsPlugin`：压缩JS代码；（内置插件）
- `ExtractTextPlugin`：分离CSS和JS文件
安装ExtractTextPlugin：`npm install --save-dev extract-text-webpack-plugin`


```js
module.exports = {
  entry: __dirname + '/app/index.js',
  output: {
    path: __dirname + '/public',
    filename: 'bundle-[hash:7].js'
  },
  devtool: 'eval-source-map',
  devServer: {},
  module: {},
  plugins: [
    new webpack.BannerPlugins('版权所有，翻版必究'),
    new HtmlWebpackPlugin({
      template: __dirname + '/app/index.tmpl.html',
    }),
    new webpack.HotModuleReplacementPlugin(),   // 热加载插件
    new webpack.optimize.OccurenceOrderPlugin,
    new webpack.optimize.UglifyJsPlugin,
    new ExtractTextPlugin('style.css')
  ]
}

// .babelrc
{
  'persets': ['react', 'es2015', 'stage-0']
  'env': {
    'devlopment': {
      'plugins': [['react-transform', {
        transform: [{
          transform:: 'react-transform-hmr',
          imports: ['react'],
          locals: ['module']
        }]
      }]]
    }
  }
}

// package.json 
'script': {
  "test"': "echo \"Error: no test specified\" && exit 1",
  "start": "webpack",
  "server": "webpack-dev-server --open",
  "build": "NODE_ENV=production webpack --config ./webpack.config.js --progress"
}
```

HtmlWebpackPlugin 参数说明：
```js 
new HtmlWebpackPlugin({
  template: __dirname + '/app/index.tmpl.html',
  title: 'Test',
  filename: 'index.html',
  inject: 'body',
  favicon: './images/favico.ico',
  minify: true,
  hash: true,
  cache: false,
  showErrors: false,
  xhtml: false,
  chunks: {
    head: {
      entry: 'assets/head_bundle.js',
      css: ['main.css']
    }
  }
}),
```

- title: 设置title的名字   
- filename: 设置这个html的文件名   
- template:要使用的模块的路径  
- inject: 把模板注入到哪个标签后， 'body',
- favicon: 给html添加一个favicon，  './images/favico.ico',
- minify:是否压缩  {...} | false （最新api变动，原来是ture|false)
- hash:是否hash化 true，false ,     
- cache:是否缓存, 
- showErrors:是否显示错误,  
- xhtml:是否自动毕业标签 默认false 

#### 12、缓存
缓存无处不在，使用缓存的最好方法是保证你的文件名和文件内容是匹配的（内容改变，名称相应改变）
解决办法：带hash值

配置说明：
```js
const path = require('path');

module.exports = {
  // entry 表示 入口，Webpack 执行构建的第一步将从 Entry 开始，可抽象成输入。
  // 类型可以是 string | object | array   
  entry: './app/entry', // 只有1个入口，入口只有1个文件
  entry: ['./app/entry1', './app/entry2'], // 只有1个入口，入口有2个文件
  entry: { // 有2个入口
    a: './app/entry-a',
    b: ['./app/entry-b1', './app/entry-b2']
  },

  // 如何输出结果：在 Webpack 经过一系列处理后，如何输出最终想要的代码。
  output: {
    // 输出文件存放的目录，必须是 string 类型的绝对路径。
    path: path.resolve(__dirname, 'dist'),

    // 输出文件的名称
    filename: 'bundle.js', // 完整的名称
    filename: '[name].js', // 当配置了多个 entry 时，通过名称模版为不同的 entry 生成不同的文件名称
    filename: '[chunkhash].js', // 根据文件内容 hash 值生成文件名称，用于浏览器长时间缓存文件

    // 发布到线上的所有资源的 URL 前缀，string 类型
    publicPath: '/assets/', // 放到指定目录下
    publicPath: '', // 放到根目录下
    publicPath: 'https://cdn.example.com/', // 放到 CDN 上去

    // 导出库的名称，string 类型
    // 不填它时，默认输出格式是匿名的立即执行函数
    library: 'MyLibrary',

    // 导出库的类型，枚举类型，默认是 var
    // 可以是 umd | umd2 | commonjs2 | commonjs | amd | this | var | assign | window | global | jsonp ，
    libraryTarget: 'umd', 

    // 是否包含有用的文件路径信息到生成的代码里去，boolean 类型
    pathinfo: true, 

    // 附加 Chunk 的文件名称
    chunkFilename: '[id].js',
    chunkFilename: '[chunkhash].js',

    // JSONP 异步加载资源时的回调函数名称，需要和服务端搭配使用
    jsonpFunction: 'myWebpackJsonp',

    // 生成的 Source Map 文件名称
    sourceMapFilename: '[file].map',

    // 浏览器开发者工具里显示的源码模块名称
    devtoolModuleFilenameTemplate: 'webpack:///[resource-path]',

    // 异步加载跨域的资源时使用的方式
    crossOriginLoading: 'use-credentials',
    crossOriginLoading: 'anonymous',
    crossOriginLoading: false,
  },

  // 配置模块相关
  module: {
    rules: [ // 配置 Loader
      {  
        test: /\.jsx?$/, // 正则匹配命中要使用 Loader 的文件
        include: [ // 只会命中这里面的文件
          path.resolve(__dirname, 'app')
        ],
        exclude: [ // 忽略这里面的文件
          path.resolve(__dirname, 'app/demo-files')
        ],
        use: [ // 使用那些 Loader，有先后次序，从后往前执行
          'style-loader', // 直接使用 Loader 的名称
          {
            loader: 'css-loader',      
            options: { // 给 html-loader 传一些参数
            }
          }
        ]
      },
    ],
    noParse: [ // 不用解析和处理的模块
      /special-library\.js$/  // 用正则匹配
    ],
  },

  // 配置插件
  plugins: [],

  // 配置寻找模块的规则
  resolve: { 
    modules: [ // 寻找模块的根目录，array 类型，默认以 node_modules 为根目录
      'node_modules',
      path.resolve(__dirname, 'app')
    ],
    extensions: ['.js', '.json', '.jsx', '.css'], // 模块的后缀名
    alias: { // 模块别名配置，用于映射模块
       // 把 'module' 映射 'new-module'，同样的 'module/path/file' 也会被映射成 'new-module/path/file'
      'module': 'new-module',
      // 使用结尾符号 $ 后，把 'only-module' 映射成 'new-module'，
      // 但是不像上面的，'module/path/file' 不会被映射成 'new-module/path/file'
      'only-module$': 'new-module', 
    },
    alias: [ // alias 还支持使用数组来更详细的配置
      {
        name: 'module', // 老的模块
        alias: 'new-module', // 新的模块
        // 是否是只映射模块，如果是 true 只有 'module' 会被映射，如果是 false 'module/inner/path' 也会被映射
        onlyModule: true, 
      }
    ],
    symlinks: true, // 是否跟随文件软链接去搜寻模块的路径
    descriptionFiles: ['package.json'], // 模块的描述文件
    mainFields: ['main'], // 模块的描述文件里的描述入口的文件的字段名称
    enforceExtension: false, // 是否强制导入语句必须要写明文件后缀
  },

  // 输出文件性能检查配置
  performance: { 
    hints: 'warning', // 有性能问题时输出警告
    hints: 'error', // 有性能问题时输出错误
    hints: false, // 关闭性能检查
    maxAssetSize: 200000, // 最大文件大小 (单位 bytes)
    maxEntrypointSize: 400000, // 最大入口文件大小 (单位 bytes)
    assetFilter: function(assetFilename) { // 过滤要检查的文件
      return assetFilename.endsWith('.css') || assetFilename.endsWith('.js');
    }
  },

  devtool: 'source-map', // 配置 source-map 类型

  context: __dirname, // Webpack 使用的根目录，string 类型必须是绝对路径

  // 配置输出代码的运行环境
  target: 'web', // 浏览器，默认
  target: 'webworker', // WebWorker
  target: 'node', // Node.js，使用 `require` 语句加载 Chunk 代码
  target: 'async-node', // Node.js，异步加载 Chunk 代码
  target: 'node-webkit', // nw.js
  target: 'electron-main', // electron, 主线程
  target: 'electron-renderer', // electron, 渲染线程

  externals: { // 使用来自 JavaScript 运行环境提供的全局变量
    jquery: 'jQuery'
  },

  stats: { // 控制台输出日志控制
    assets: true,
    colors: true,
    errors: true,
    errorDetails: true,
    hash: true,
  },

  devServer: { // DevServer 相关的配置
    proxy: { // 代理到后端服务接口
      '/api': 'http://localhost:3000'
    },
    contentBase: path.join(__dirname, 'public'), // 配置 DevServer HTTP 服务器的文件根目录
    compress: true, // 是否开启 gzip 压缩
    historyApiFallback: true, // 是否开发 HTML5 History API 网页
    hot: true, // 是否开启模块热替换功能
    https: false, // 是否开启 HTTPS 模式
    },

    profile: true, // 是否捕捉 Webpack 构建的性能信息，用于分析什么原因导致构建性能不佳

    cache: false, // 是否启用缓存提升构建速度

    watch: true, // 是否开始
    watchOptions: { // 监听模式选项
    // 不监听的文件或文件夹，支持正则匹配。默认为空
    ignored: /node_modules/,
    // 监听到变化发生后会等300ms再去执行动作，防止文件更新太快导致重新编译频率太高
    // 默认为300ms 
    aggregateTimeout: 300,
    // 判断文件是否发生变化是不停的去询问系统指定文件有没有变化，默认每秒问 1000 次
    poll: 1000
  },
}
```
