---
title: ES6基础知识 — 字符串的扩展
date: 2018-10-17 20:28:31
tags: [ES6]
categories: 基础知识
toc: true
---

[1、字符的Unicode表示法](#1、字符的Unicode表示法)
[2、codePointAt](#2、codePointAt)
[3、String.fromCodePoint](#3、String.fromCodePoint)
[4、字符串的遍历器接口for...of...](#4、字符串的遍历器接口for...of...)
[5、normalize](#5、normalize)
[6、includes(), startsWith(), endsWith()](#6、includes, startsWith, endsWith)
[7、repeat](#7、repeat)
[8、padStart，padEnd](#8、padStart，padEnd)
[9、matchAll](#9、matchAll)
[10、模板字符串](#10、模板字符串)
[11、String.raw](#11、String.raw)

### 1、字符的Unicode表示法
JS允许采用`\uxxxx`形式表示一个字符，其中`xxxx`表示字符的`Unicode`码点， 只限于码点在`\u0000`~`\uFFFF`
JS 共有 6 种方法可以表示一个字符:
```js
'\z' === 'z'      // true
'\172' === 'z'    // true
'\x7A' === 'z'    // true
'\u007A' === 'z'  // true
'\u{7A}' === 'z'  // true   ES6的改进
```
### 2、codePointAt
能够正确说明字符的字节数，返回字符码点的十进制数

### 3、String.fromCodePoint
`String.fromCharCode`从码点返回对应字符，但不能识别 32 位的 UTF-16 字符
`String.fromCodePoint`方法弥补不足，有多个参数，则它们会被合并成一个字符串返回

<font color="#dd0000">`fromCodePoint`方法定义在`String`对象上，而`codePointAt`方法定义在字符串的实例对象上。</font>

### 4、字符串的遍历器接口for...of...
for...of...：字符串可被循环遍历，可以识别大于0xFFFF的码点

### 5、normalize
用来将字符的不同表示方法统一为同样的形式，称为 Unicode 正规化（该方法可传参，具体需要再查）

### 6、includes, startsWith, endsWith
`indexOf`一个字符串是否包含在另一个字符串中
`includes(str, n)`是否找到了参数字符串，n表示开始搜索的位置：
`startsWith(str, n)`参数字符串是否在原字符串的头部，n表示开始搜索的位置：
`endsWith(str, n)`参数字符串是否在原字符串的尾部，n表示开始搜索的位置：
### 7、repeat
将原字符串重复n次，参数若是小数，会被取整；参数是负数或者Infinity，会报错；参数NaN等同于 0。

### 8、padStart，padEnd
`padStart(n, str)`：用于字符串在头部补全，n指定字符串的最小长度，arr用来补全的字符串。
`padEnd(n, str)`：字符串在尾部补全，n指定字符串的最小长度，arr用来补全的字符串。
如果原字符串的长度，等于或大于指定的最小长度，则返回原字符串。
如果用来补全的字符串与原字符串，两者的长度之和超过了指定的最小长度，则会截去超出位数的补全字符串。
如果省略第二个参数，默认使用空格补全长度。

### 9、matchAll
返回一个正则表达式在当前字符串的所有匹配

### 10、模板字符串
`${}和引号`的使用

### 11、String.raw
模板字符串的处理函数
返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串，对应于替换变量后的模板字符串
String.raw`Hi\n${2+3}!`;    // 返回 "Hi\\n5!"
String.raw`Hi\u000A!`;    // 返回 "Hi\\u000A!"

```js
// 1、codePointAt
var str = "𠮷";
str.length;   // length为2，无法正确返回，其为4字节的字符
str.charAt(0);  //'�' 会导致乱码
str.charAt(1);  //'�' 会导致乱码
str.codePointAt();  // 结果:134071

// 2、fromCodePoint
String.fromCodePoint(0x20BB7)
// "𠮷"
String.fromCodePoint(0x78, 0x1f680, 0x79) === 'x\uD83D\uDE80y'
// true

// 3、字符串的遍历器接口for...of...
for (let codePoint of 'foo') {
  console.log(codePoint);      // "f"  // "o"  // "o"
}

let text = String.fromCodePoint(0x20BB7);
for (let i of text) {
  console.log(i);       // "𠮷"
}

// 4、includes(), startsWith(), endsWith()
let s = 'Hello world!';
s.startsWith('world', 6)  // true
s.endsWith('Hello', 5)    // true
s.includes('Hello', 6)    // false

// 5、repeat
'hello'.repeat(2) // "hellohello"
'na'.repeat(2.9) // "nana"
'na'.repeat('na') // ""
'na'.repeat('3') // "nanana"
'na'.repeat(-0.9) // ""
'na'.repeat(NaN) // ""

'xxx'.padStart(2, 'ab')     // 'xxx'
'xxx'.padEnd(2, 'ab')       // 'xxx'  
'abc'.padStart(10, '0123456789')    // '0123456abc'
'x'.padStart(4)         // '   x'
'x'.padEnd(4)           // 'x   '
```