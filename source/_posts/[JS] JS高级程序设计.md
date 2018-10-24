---
title: JS高级程序设计
date: 2018-09-26 14:48:21
tags: [JS]
toc: true
categories: 基础知识
---

## 目录：
[chap5、引用类型](#chap5、引用类型)
[chap6、面向对象](#chap6、面向对象)

### chap5、引用类型
引用类型是一种数据结构，用于将数据和功能组织在一起。
1. Object类型
可以使用.点和[]进行访问对象属性，没有任何去边，但方括号的主要优点：可以通过变量来访问属性。
若属性名中包含关键字或保留字或导致语法错误的字符，可以使用方括号表示法。
**除非必须使用变量来访问属性，否则建议使用点表示法**

2. Array类型
Array构造函数时可以省略new操作符，const color = new Array(3); const color = Array(3);
数组的length并不是只读的，可以被设置。

```js
const color = ['red', 'green', 'blue'];
color.length = 2;
console.log(color[2]);  // undefined，会被删除

color.length = 4;
console.log(color[3]);  // undefined，会被添加
```

3. Array类型方法

**转换方法**：所有对象都具有toLocaleString()、toString()和valueod()方法。
`valueOf()`函数用于返回指定对象的原始值；
`toString()`函数用于返回指定对象的字符串；

| 对象         | valueOf() 返回值       | toString() 返回值     |
| :--------:  | :--------:            | :-------:           |
| Number      | 数字值(number)         | 数字值(string)        |
| String      | 字符串值                | 字符串值              |
| Boolean     | 布尔值                  | 字符串值              |
| Object      | 对象本身                 | 字符串值             |
| Function    | 函数本身                | 字符串值              |
| Date    | 以毫秒数存储的时间值(number)   | 标准时间输出(string)   |

若数组中的某一项的值是null或undefined，在join()、toLocaleString()、toString()、valueof()方法返回的结果中以空字符串表示。

**队列方法(数组：先进后出)：**
`shift()`: 取出（删除）数组的第一项，最开始的头部
`unshift()`：添加一项至数组的第一项头部
`push()`：添加一项至数组的第一项尾部
`pop()`：取出（删除）数组的最后一项目，尾部

**操作方法：**
`concat()`：无参数时，复制当前数组并返回；参数为一个或多个数组，将数组汇总的每一项都添加到结果数组中。
`slice()`：返回项的起始和结束位置。该函数不影响原是数组。
**若slice的参数为负数，则用长度去减，若结束位置小于起始位置，则返回空数组。**
eg：slice(-2, -1)的长度为5的数组调用后为 slice(3,4);
`splice() `：向数组的中部插入项，实现删除、插入和替换。
    删除：splice(起始位置，项数);
    插入：splice(起始位置，0，要插入的项，要插入的项，要插入的项，...);
    替换：splice(起始位置，项数，要插入的项，要插入的项，要插入的项，...);删除后再添加

**位置方法：**
`indexOf(查找项，查找起点位置的索引)`从数组的开头开始（位置0）向后查找。
`lastIndexOf(查找项，查找起点位置的索引)`从数组的末尾开始向前查找。

**迭代方法：**
`every()`：数组中的每一项都必须满足条件，才返回true
`filter()`：过滤
`forEach()`：遍历循环
`map()`：遍历循环
`some()`：数组中的只要有满足条件的，就返回true
```js
const numbers = [1,2,3,4,5,4,3,2,1];
const everyResult = numbers.every((item, index, array) => {
    return (item > 2);
});
// everyResult 结果为： false

const someResult = numbers.every((item, index, array) => {
    return (item > 2);
});
// someResult 结果为： true

const filterResult = numbers.every((item, index, array) => {
    return (item > 2);
});
// filterResult 结果为： [3,4,5,4,3]

const mapResult = numbers.every((item, index, array) => {
    return (item > 2);
});
// mapResult 结果为： [2,4,6,8,10,8,6,4,2]
```

**缩小方法：**
`reduce(函数，缩小基础的初始值)`和`reduceRight(函数，缩小基础的初始值)`都会迭代数组的所有项，reduceRight是从最后一项开始。
```js
const val = [1,2,3,4,5];
// 前一个值、当前值、项的索引和数组对象
val.reduce((prev, cur, index, array) => {});
```

4. Function类型
`arguments`（保存函数参数）为类数组对象，包含着传入函数中的所有参数。
`callee` 为一个指针，指向拥有这个arguments对象的函数。
`this` 函数据以执行的环境对象。

```js
// 阶乘函数的递归算法
function factorial(num) {
   return num <= 1 ? 1 : num * arguments.callee(num-1);
}
// 利用callee 松散耦合
```

`apply `和 `call` 都是在特定的作用域中调用函数，实际上等于设置函数体内this对象的值。
**能够扩充函数赖以运行的作用域**
`apply(运行函数的作用域，参数数组Array / arguments))`
`call(运行函数的作用域，参数1，参数2，....)`
```js
function sum(num1, num2) {
    return num1 + num2;
}
function applySum(num1, num2) {
    return sum.apply(this, arguments);
}
// 因为在非严格的全局调用，this指向window，严格模式下，this为undefined。
// 除非明确把函数添加到某个对象或者调用者
console.log(applySum(10,10));    // 20

function callSum(num1, num2) {
    return sum.call(this, ...arguments);
}
console.log(callSum(10,10));    // 20

window.color = 'red';
const o = { color: 'blue' };

function sayColor() {
    console.log(this.color);
}

sayColor(); // red
sayColor.call(this);    // red
sayColor.call(window);  // red
sayColor.call(o);       // blue 此时this指向对象o

// this值会被绑定到传给bind() 函数的值
const objSaycolor = sayColor.bind(o);
objSaycolor();      // blue
```

5. 基本包装类型
`toFixed()` 返回字符串类型；
`toExponential()` 指数表示法表示的数值的字符串形式；
`charAt(0开始的字符位置)`：该位置对应的字符；
`charCodeAt(0开始的字符位置)`：该位置对应的字符编码；
`concat()`：将一或多个字符串拼接起来，返回新的；
`slice()`、`substr()`和`substring()`：裁剪字符串；

```js
// 负数时，存在差异
const stringVal = 'hello world';
console.log(stringVal.slice(-3));       // 'rld'
console.log(stringVal.substring(-3));   // 'hello world'
console.log(stringVal.substr(-3));      // 'rld'
console.log(stringVal.slice(3, -4));    // 'lo w'
console.log(stringVal.substring(3, -4)); // 'hel'
console.log(stringVal.substr(3, -4));    // ''
```

`trim()` 创建一个字符串副本，删除前置及后缀的所有空格。
`match()`：模式匹配方法，类似于`RegExp `和 `exec()`
```js
const text = 'cat, bat, sat, fat';
const pattern = /.at/;
const result = text.match(pattern); // ["cat", index: 0, input: "cat, bat, sat, fat", groups: undefined]
const result = pattern.exec(text)  // ["cat", index: 0, input: "cat, bat, sat, fat", groups: undefined]
const result = text.search(/at/);  // 1
const result = text.replace('at', 'ond');   // "cond, bat, sat, fat"
const result = text.replace(/at/g, 'ond');   // "cond, bond, sond, fond"
const result = text.replace(/(.at)/g, 'word($1)'); // "word(cat), word(bat), word(sat), word(fat)"
```
`split()` 基于指定的分隔符将一个字符串分割成多个子字符串；
`localeCompare()`比较两个字符串，返回与字符串在字母表中的排序有关，靠前，返回1，否则-1，相同为0；
const stringVal = 'yellow';
stringVal.localeCompare('brick');   // 1
`fromCharCode()`：接收一或多个字符编码，并转换成字符串；
String.fromCharCode(104, 101, 108, 108, 111);   // hello

-----------

### chap6、面向对象

1. 对象的属性类型
ECMAScript中有两种属性：数据属性和访问器属性。
数据属性包含一个数据值的位置，可以读取和写入值。数据属性有4个描述其行为的特性：
`[[Configurable]]`：表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。默认值为true；
`[[Enumberable]]`：表示能否通过 for-in 循环返回属性。默认值为true；
`[[Writable]]`：表示能否修改属性的值。
`[[Value]]`：包含这个属性的数据值，读取属性值时，从这个位置读，写入属性值时，把新值保存在这个位置。

```js
const person = {
    name: 'ruby'
}
```
要修改属性默认的特性，需要使用 Object.defineProperty()（见“Object 创建/修改/获取属性的方法”）。

2. 访问器属性
不包含数据值，包含 getter 和 setter 函数。访问器属性有4个特性：
`[[Configurable]]`：表示能否通过delete删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性。默认值为true；
`[[Enumberable]]`：表示能否通过 for-in 循环返回属性。默认值为true；
`[[Get]]`：在读取属性时调用的函数，默认为undefined。
`[[Set]]`：在写入属性时调用的函数，默认为undefined。

访问器属性不能直接定义，必须使用Object.defineProperties()来定义。
```js
```

-----------
