---
title: webpack流程
date: 2018-10-20 22:49:14
tags: [webpack]
categories: 构建
toc: true
---

## 流程概括
Webpack 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：

1、`初始化参数`：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数；
2、`开始编译`：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译；
3、`确定入口`：根据配置中的 entry 找出所有的入口文件；
4、`编译模块`：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理；
5、`完成模块编译`：在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系；
6、`输出资源`：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会；
7、`输出完成`：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。

在以上过程中，Webpack 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果。

## 流程细节
Webpack 的构建流程可以分为以下三大阶段：
1、`初始化`：启动构建，读取与合并配置参数，加载 `Plugin`，实例化` Compiler`。
2、`编译`：从 `Entry` 发出，针对每个 `Module` 串行调用对应的 `Loader` 去翻译文件内容，再找到该 Module 依赖的 Module，递归地进行编译处理。
3、`输出`：对编译后的 Module 组合成 `Chunk`，把 Chunk 转换成文件，输出到文件系统。

在每个大阶段中又会发生很多事件，Webpack 会把这些事件广播出来供给 Plugin 使用，下面来一一介绍。

### 初始化阶段

| 事件名        | 解释   | 
| --------   | -----  | 
| 初始化参数   | 从配置文件和 Shell 语句中读取与合并参数，得出最终的参数。 这个过程中还会执行配置文件中的插件实例化语句 `new Plugin()`。 | 
| 实例化 `Compiler`  | 用上一步得到的参数初始化 `Compiler` 实例，`Compiler` 负责文件监听和启动编译。`Compiler` 实例中包含了完整的 Webpack 配置，全局只有一个 `Compiler` 实例。  |
| 加载插件    |  依次调用插件的 `apply` 方法，让插件可以监听后续的所有事件节点。同时给插件传入 `compiler` 实例的引用，以方便插件通过 `compiler` 调用 Webpack 提供的 API。    |
| `environment`    |  开始应用 Node.js 风格的文件系统到 `compiler` 对象，以方便后续的文件寻找和读取。    |
| `entry-option `   | 读取配置的 `Entrys`，为每个 `Entry` 实例化一个对应的 `EntryPlugin`，为后面该 `Entry` 的递归解析工作做准备。   |
| `after-plugins`   | 调用完所有内置的和配置的插件的 `apply` 方法。   |
| `after-resolvers`     | 根据配置初始化完 `resolver`，`resolver `负责在文件系统中寻找指定路径的文件。    |

### 编译阶段

| 事件名        | 解释   | 
| --------    | -----  | 
| `run`   | 启动一次新的编译。 | 
| `watch-run`  | 和 run 类似，区别在于它是在监听模式下启动的编译，在这个事件中可以获取到是哪些文件发生了变化导致重新启动一次新的编译。  |
| `compile`    | 该事件是为了告诉插件一次新的编译将要启动，同时会给插件带上 compiler 对象。 |
| `compilation`  | 当 Webpack 以开发模式运行时，每当检测到文件变化，一次新的 `Compilation` 将被创建。一个 `Compilation` 对象包含了当前的模块资源、编译生成资源、变化的文件等。`Compilation` 对象也提供了很多事件回调供插件做扩展。  |
| `make`   | 一个新的 `Compilation` 创建完毕，即将从 `Entry` 开始读取文件，根据文件类型和配置的 Loader 对文件进行编译，编译完后再找出该文件依赖的文件，递归的编译和解析。   |
| `after-compile`  | 一次 Compilation 执行完成。   |
| `invalid`   | 当遇到文件不存在、文件编译错误等异常时会触发该事件，该事件不会导致 Webpack 退出。   |

在编译阶段中，最重要的要数 compilation 事件了，因为在 compilation 阶段调用了 Loader 完成了每个模块的转换操作，在 compilation 阶段又包括很多小的事件，它们分别是：

| 事件名        | 解释   | 
| --------    | -----  | 
| `build-module`  | 使用对应的 Loader 去转换一个模块。 | 
| `normal-module-loader`  | 在用 Loader 对一个模块转换完后，使用 acorn 解析转换后的内容，输出对应的抽象语法树（AST），以方便 Webpack 后面对代码的分析。  |
| `program`    | 从配置的入口模块开始，分析其 AST，当遇到 require 等导入其它模块语句时，便将其加入到依赖的模块列表，同时对新找出的依赖模块递归分析，最终搞清所有模块的依赖关系。 |
| `seal`  | 所有模块及其依赖的模块都通过 Loader 转换完成后，根据依赖关系开始生成 Chunk。  |


### 输出阶段
| 事件名        | 解释   | 
| --------    | -----  | 
| `should-emit`  | 所有需要输出的文件已经生成好，询问插件哪些文件需要输出，哪些不需要。 | 
| `emit`  | 确定好要输出哪些文件后，执行文件输出，可以在这里获取和修改输出内容。  |
| `after-emit`    | 文件输出完毕。 |
| `done`  | 成功完成一次完成的编译和输出流程。  |
| `failed`  | 如果在编译和输出流程中遇到异常导致 Webpack 退出时，就会直接跳转到本步骤，插件可以在本事件中获取到具体的错误原因。  |

## 输出文件分析
bundle.js
```js
(
    // webpackBootstrap 启动函数
    // modules 即为存放所有模块的数组，数组中的每一个元素都是一个函数
    function (modules) {
        // 安装过的模块都存放在这里面
        // 作用是把已经加载过的模块缓存在内存中，提升性能
        var installedModules = {};

        // 去数组中加载一个模块，moduleId 为要加载模块在数组中的 index
        // 作用和 Node.js 中 require 语句相似
        function __webpack_require__(moduleId) {
            // 如果需要加载的模块已经被加载过，就直接从内存缓存中返回
            if (installedModules[moduleId]) {
                return installedModules[moduleId].exports;
            }

            // 如果缓存中不存在需要加载的模块，就新建一个模块，并把它存在缓存中
            var module = installedModules[moduleId] = {
                // 模块在数组中的 index
                i: moduleId,
                // 该模块是否已经加载完毕
                l: false,
                // 该模块的导出值
                exports: {}
            };

            // 从 modules 中获取 index 为 moduleId 的模块对应的函数
            // 再调用这个函数，同时把函数需要的参数传入
            modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);
            // 把这个模块标记为已加载
            module.l = true;
            // 返回这个模块的导出值
            return module.exports;
        }

        // Webpack 配置中的 publicPath，用于加载被分割出去的异步代码
        __webpack_require__.p = "";

        // 使用 __webpack_require__ 去加载 index 为 0 的模块，并且返回该模块导出的内容
        // index 为 0 的模块就是 main.js 对应的文件，也就是执行入口模块
        // __webpack_require__.s 的含义是启动模块对应的 index
        return __webpack_require__(__webpack_require__.s = 0);

    })(

    // 所有的模块都存放在了一个数组里，根据每个模块在数组的 index 来区分和定位模块
    [
        /* 0 */
        (function (module, exports, __webpack_require__) {
            // 通过 __webpack_require__ 规范导入 show 函数，show.js 对应的模块 index 为 1
            const show = __webpack_require__(1);
            // 执行 show 函数
            show('Webpack');
        }),
        /* 1 */
        (function (module, exports) {
            function show(content) {
                window.document.getElementById('app').innerText = 'Hello,' + content;
            }
            // 通过 CommonJS 规范导出 show 函数
            module.exports = show;
        })
    ]
);
```
以上看上去复杂的代码其实是一个立即执行函数，可以简写为如下：
```js
(function(modules) {

  // 模拟 require 语句
  function __webpack_require__() {
  }

  // 执行存放所有模块数组中的第0个模块
  __webpack_require__(0);

})([/*存放所有模块的数组*/])
```
bundle.js 能直接运行在浏览器中的原因在于输出的文件中通过 `__webpack_require__` 函数定义了一个可以在浏览器中执行的加载函数来模拟 Node.js 中的 require 语句。

原来一个个独立的模块文件被合并到了一个单独的 bundle.js 的原因在于浏览器不能像 Node.js 那样快速地去本地加载一个个模块文件，而必须通过网络请求去加载还未得到的文件。 
如果模块数量很多，加载时间会很长，因此把所有模块都存放在了数组中，执行一次网络加载。

如果仔细分析 _`_webpack_require__` 函数的实现，还有发现 Webpack 做了缓存优化： **执行加载过的模块不会再执行第二次，执行结果会缓存在内存中，当某个模块第二次被访问时会直接去内存中读取被缓存的返回值。**

### 分割代码时的输出
把源码中的 main.js 修改为如下：
```js
// 异步加载 show.js
import('./show').then((show) => {
  // 执行 show 函数
  show('Webpack');
});
```
重新构建后会输出两个文件，分别是执行入口文件 bundle.js 和 异步加载文件 0.bundle.js。

其中 0.bundle.js 内容如下：
```js
// 加载在本文件(0.bundle.js)中包含的模块
webpackJsonp(
  // 在其它文件中存放着的模块的 ID
  [0],
  // 本文件所包含的模块
  [
    // show.js 所对应的模块
    (function (module, exports) {
      function show(content) {
        window.document.getElementById('app').innerText = 'Hello,' + content;
      }

      module.exports = show;
    })
  ]
);
```
bundle.js 内容如下：
```js
(function (modules) {
  /***
   * webpackJsonp 用于从异步加载的文件中安装模块。
   * 把 webpackJsonp 挂载到全局是为了方便在其它文件中调用。
   *
   * @param chunkIds 异步加载的文件中存放的需要安装的模块对应的 Chunk ID
   * @param moreModules 异步加载的文件中存放的需要安装的模块列表
   * @param executeModules 在异步加载的文件中存放的需要安装的模块都安装成功后，需要执行的模块对应的 index
   */
  window["webpackJsonp"] = function webpackJsonpCallback(chunkIds, moreModules, executeModules) {
    // 把 moreModules 添加到 modules 对象中
    // 把所有 chunkIds 对应的模块都标记成已经加载成功 
    var moduleId, chunkId, i = 0, resolves = [], result;
    for (; i < chunkIds.length; i++) {
      chunkId = chunkIds[i];
      if (installedChunks[chunkId]) {
        resolves.push(installedChunks[chunkId][0]);
      }
      installedChunks[chunkId] = 0;
    }
    for (moduleId in moreModules) {
      if (Object.prototype.hasOwnProperty.call(moreModules, moduleId)) {
        modules[moduleId] = moreModules[moduleId];
      }
    }
    while (resolves.length) {
      resolves.shift()();
    }
  };

  // 缓存已经安装的模块
  var installedModules = {};

  // 存储每个 Chunk 的加载状态；
  // 键为 Chunk 的 ID，值为0代表已经加载成功
  var installedChunks = {
    1: 0
  };

  // 模拟 require 语句，和上面介绍的一致
  function __webpack_require__(moduleId) {
    // ... 省略和上面一样的内容
  }

  /**
   * 用于加载被分割出去的，需要异步加载的 Chunk 对应的文件
   * @param chunkId 需要异步加载的 Chunk 对应的 ID
   * @returns {Promise}
   */
  __webpack_require__.e = function requireEnsure(chunkId) {
    // 从上面定义的 installedChunks 中获取 chunkId 对应的 Chunk 的加载状态
    var installedChunkData = installedChunks[chunkId];
    // 如果加载状态为0表示该 Chunk 已经加载成功了，直接返回 resolve Promise
    if (installedChunkData === 0) {
      return new Promise(function (resolve) {
        resolve();
      });
    }

    // installedChunkData 不为空且不为0表示该 Chunk 正在网络加载中
    if (installedChunkData) {
      // 返回存放在 installedChunkData 数组中的 Promise 对象
      return installedChunkData[2];
    }

    // installedChunkData 为空，表示该 Chunk 还没有加载过，去加载该 Chunk 对应的文件
    var promise = new Promise(function (resolve, reject) {
      installedChunkData = installedChunks[chunkId] = [resolve, reject];
    });
    installedChunkData[2] = promise;

    // 通过 DOM 操作，往 HTML head 中插入一个 script 标签去异步加载 Chunk 对应的 JavaScript 文件
    var head = document.getElementsByTagName('head')[0];
    var script = document.createElement('script');
    script.type = 'text/javascript';
    script.charset = 'utf-8';
    script.async = true;
    script.timeout = 120000;

    // 文件的路径为配置的 publicPath、chunkId 拼接而成
    script.src = __webpack_require__.p + "" + chunkId + ".bundle.js";

    // 设置异步加载的最长超时时间
    var timeout = setTimeout(onScriptComplete, 120000);
    script.onerror = script.onload = onScriptComplete;

    // 在 script 加载和执行完成时回调
    function onScriptComplete() {
      // 防止内存泄露
      script.onerror = script.onload = null;
      clearTimeout(timeout);

      // 去检查 chunkId 对应的 Chunk 是否安装成功，安装成功时才会存在于 installedChunks 中
      var chunk = installedChunks[chunkId];
      if (chunk !== 0) {
        if (chunk) {
          chunk[1](new Error('Loading chunk ' + chunkId + ' failed.'));
        }
        installedChunks[chunkId] = undefined;
      }
    };
    head.appendChild(script);

    return promise;
  };

  // 加载并执行入口模块，和上面介绍的一致
  return __webpack_require__(__webpack_require__.s = 0);
})
(
  // 存放所有没有经过异步加载的，随着执行入口文件加载的模块
  [
    // main.js 对应的模块
    (function (module, exports, __webpack_require__) {
      // 通过 __webpack_require__.e 去异步加载 show.js 对应的 Chunk
      __webpack_require__.e(0).then(__webpack_require__.bind(null, 1)).then((show) => {
        // 执行 show 函数
        show('Webpack');
      });
    })
  ]
);
```
这里的 bundle.js 和上面所讲的 bundle.js 非常相似，区别在于：
1、多了一个 `__webpack_require__.e` 用于加载被分割出去的，需要异步加载的 Chunk 对应的文件;
2、多了一个 `webpackJsonp` 函数用于从异步加载的文件中安装模块。

在使用了 CommonsChunkPlugin 去提取公共代码时输出的文件和使用了异步加载时输出的文件是一样的，都会有 __webpack_require__.e 和 webpackJsonp。 原因在于提取公共代码和异步加载本质上都是代码分割。

