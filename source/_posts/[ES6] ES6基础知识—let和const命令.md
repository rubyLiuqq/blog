---
title: ES6基础知识 — let和const命令
date: 2018-10-17 20:23:12
tags: [ES6]
categories: 基础知识
toc: true
---

[1、let和const的性质](#1、let和const的性质)
[2、块级作用域](#2、块级作用域)
[3、顶层对象的属性](#3、顶层对象的属性)
[4、global 对象](#4、global 对象)

### 1、let和const的性质
1、`let`、`const`声明的变量只在声明所在的块级作用域内有效；**const 一旦声明变量，就必须立即初始化，不能留到以后赋值。**
2、不存在变量提升；
3、暂时性死区: 在代码块内，只能在声明的位置后面使用；
4、不允许在相同作用域内，重复声明同一个变量；

<font color="#dd0000">对于简单类型的数据（数值、字符串、布尔值），const等同于常量。</font>
<font color="#dd0000">对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，const只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了。</font>
<font color="#dd0000">如果将对象冻结，可以使用Object.freeze方法。</font>

```js
// 1、变量声明提升
// var 的情况
console.log(foo); // 输出undefined
var foo = 2;
// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 2;

// 2、暂时性死区案例
if (true) {
  // TDZ开始
  tmp = 'abc'; // ReferenceError
  console.log(tmp); // ReferenceError

  let tmp; // TDZ结束
  console.log(tmp); // undefined

  tmp = 123;
  console.log(tmp); // 123
}

// 3、对象冻结
const foo = Object.freeze({});
// 常规模式时，下面一行不起作用；严格模式时，该行会报错
foo.prop = 123;

// 4、除了将对象本身冻结，对象的属性也应该冻结。
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, i) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```
### 2、块级作用域
提出原因：内层变量可能会覆盖外层变量；用来计数的循环变量泄露为全局变量……
实现作用：是在块内使用，不影响外面，外面的变量，内部也能定义。

<font color="#dd0000">ES6 有 6 种声明变量的方法：`var`、`function`、`let`、`const`、`import`、`class`</font>

### 3、顶层对象的属性
```
window.a = 1;
a   // 返回 1
a = 2;
window.a // 返回 2
```
上述代码的弊端：
1、不能在编译时就报出变量未声明的错误，只有运行时才能知道（因为全局变量可能是顶层对象的属性创造的，而属性的创造是动态的）；
2、其次，程序员很容易不知不觉地就创建了全局变量（比如打字出错）；
3、顶层对象的属性是到处可以读写的，这非常不利于模块化编程。
4、window对象有实体含义，指的是浏览器的窗口对象，顶层对象是一个有实体含义的对象，也是不合适的。

<font color="#dd0000">`var`命令和`function`命令声明的全局变量，依旧是顶层对象的属性；</font>
<font color="#dd0000">`let`命令、`const`命令、`class`命令声明的全局变量，不属于顶层对象的属性。</font>
```js
var a = 1;
// 如果在 Node 的 REPL 环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 返回 1

let b = 1;
window.b // 返回 undefined
```
### 4、global 对象
目的：为了在不同的环境中都能获取到顶层对象。
ES5顶层对象的获取：
1、浏览器里面，顶层对象是window，但 Node 和 Web Worker 没有window。
2、浏览器和 Web Worker 里面，self也指向顶层对象，但是 Node 没有self。
3、Node 里面，顶层对象是global，但其他环境都不支持。

同一段代码为了能够在各种环境，都能取到顶层对象，现在一般是使用this变量，但是有局限性。
1、全局环境中，this会返回顶层对象。但Node 模块和 ES6 模块中，this返回的是当前模块。
2、函数里面的this，如果函数不是作为对象的方法运行，而是单纯作为函数运行，this会指向顶层对象。但是，严格模式下，这时this会返回undefined。
3、不管是严格模式，还是普通模式，new Function('return this')()，总是会返回全局对象。但是，如果浏览器用了 CSP（Content Security Policy，内容安全策略），那么eval、new Function这些方法都可能无法使用。

提出了`垫片库system.global`.
```js
// CommonJS 的写法
var global = require('system.global')();

// ES6 模块的写法
import getGlobal from 'system.global';
const global = getGlobal();
```
