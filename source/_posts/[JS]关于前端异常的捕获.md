---
title: 关于前端异常的捕获
date: 2018-10-25 17:47:42
tags: [JS]
categories: 基础知识
toc: true
---
开发过程中，有不少bug能够逃过各种test、QA、review，最终出现在线上，对于线上bug，如果没有足够的log分析，那会大大增加定位时间。所以不论前后端开发，日志系统都是必不可少的一个环节。前端日志系统不像后端有比较成熟统一的解决方案，对于前端异常日志的处理通常采用前端埋点或其它异常捕获方式收集错误日志，然后提交给服务器持久化。

## 收集日志的方法
js的异常机制并不如其它编译型语言严谨，比如java中对可检查异常需要强制捕获处理，而js代码可能就会在运行时才发现有个异常直接导致线程终止。平时收集日志的手段，可以归类为两个方面，一个是逻辑中的错误判断，为主动判断；一个是利用语言给我们提供的捷径，暴力式获取错误信息，如 try..catch 和 window.onerror。所以在js项目中，更需要对异常捕获机制有统一约定，一般来说基本方法有几种：

### 1.直接捕获
对于很明确的某些达到约定异常条件的（接口返回状态不对，客户端认证异常等），直接捕获提交服务端，在可预测范围内反馈异常并记录，比如：  
```js
asyncRequest((data) => {
 // 接口返回的数据不在约定范围内，直接捕获提交
 if (data !== 'someValue') {
   reportToServer({
     // error message
   });
 }
})
```
### 2.主动判断
我们在一些运算之后，得到一个期望的结果，然而结果不是我们想要的，这种属于逻辑错误/状态错误的反馈，在接口 status 判断中用的比较多。
```js
// test.js
function calc(){
  // code...
  return val;
}
if(calc() !== "someVal"){
  Reporter.send({
    position: "test.js::<Function>calc"
    msg: "calc error"
  });
}
```
### 3.try...catch 捕获
java中如果某段代码可能抛出checked Exception，那么就需要强制在外层加上try...catch，类似于这样的处理机制，js中，在有可能抛出异常的地方，使用try...catch进行异常捕获处理，比如，对于一个有可能为undefined的数组参数arr，常规处理和try..catch方式对比：
```js
// normal
// 会报TypeError: Cannot read property 'length' of undefined错误，并且线程终止
doSomething(a.length);
doOtherthings();
// try...catch
try {
 doSomething(a.length);
} catch (e) {
 reportToServer(e);
 console.log('HaHa, I catch the error');
}
doOtherthings();
```

### 4.window.onerror/window.addEventListener('error')
捕获全局错误：
```js
window.onerror = function() {
  var errInfo = format(arguments);
  Reporter.send(errInfo);
  return true;
};
```
在上面的函数中返回 return true，错误便不会暴露到控制台中。下面是它的参数信息：

```js
/**
 * @param {String}  errorMessage   错误信息
 * @param {String}  scriptURI      出错的文件
 * @param {Long}    lineNumber     出错代码的行号
 * @param {Long}    columnNumber   出错代码的列号
 * @param {Object}  errorObj       错误的详细信息，Anything
 */
window.onerror = function(errorMessage, scriptURI, lineNumber,columnNumber,errorObj) { 
    // code..
}
```
`window.addEventListener('error)`监听error事件时，只有一个ErrorEvent事件参数，继承自Event，包含以下属性：
`ErrorEvent.message`：错误信息
`ErrorEvent.filename`：出错文件位置
`ErrorEvent.lineno`：出错位置行号
`ErrorEvent.colno`：出错位置列号
`ErrorEvent.error`：错误详细信息
window.onerror 算是一种特别暴力的容错手段，try..catch 也是如此，他们底层的实现就是利用 C/C++ 中的 goto 语句实现，一旦发现错误，不管目前的堆栈有多深，不管代码运行到了何处，直接跑到顶层或者 try..catch 捕获的那一层，这种一脚踢开错误的处理方式并不是很好。

## 收集日志存在的问题
收集日志的目的是为了及时发现问题，最好日志能够告诉我们，错误在哪里，更优秀的做法是，不仅告诉错误在哪里，还告诉我们，如何处理这个错误。终极目标是，发现错误，自动容错，这一步是最难的。
### 1.无具体报错信息，Script error.
具体原因![可看这里](https://stackoverflow.com/questions/5913978/cryptic-script-error-reported-in-javascript-in-chrome-and-firefox)，修改方式为：
1、`在script标签中加入crossorigin属性`
`<script crossorigin="anonymous" src="your.crossorigin.script"></script>`
2、`响应头增加Access-Control-Allow-Origin`
`Access-Control-Allow-Origin: *`

### 2.上报方式
常使用的方式有两种：
<font color="#d00">1、ajax请求上报</font>
<font color="#d00">2、利用img标签的src属性上报(考虑解析性能和多站点服务，更常用的上报方式)</font>

需要注意的是，尽量避免简单直接的捕获--> 上报这样的流程，可能造成日志量过大、阻塞用户操作等问题。所以一些错误合并、延迟上报、抽样采集还是有必要做的。

## 压缩混淆的线上代码
对于线上经压缩混淆后的代码，即使拿到错误行列信息也很难去代码中定位问题，看到的解决方案有：  
<font color="#d00">生成不同文件配合sourceMap</font>
<font color="#d00">后台实现功能：上传源码，选择压缩平台产生对应sourceMap，再通过转换将压缩的行列信息对应到源码的行列信息</font>
这部分目前缺少实践经验，实践后再补充实现方案。