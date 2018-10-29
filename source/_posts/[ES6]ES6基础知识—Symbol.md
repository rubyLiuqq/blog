---
title: ES6基础知识—Symbol
date: 2018-10-26 10:38:24
tags: [ES6]
categories: 基础知识
toc: true
---
[1、概述](#1、概述)
[2、作为属性名的Symbol](#2、作为属性名的Symbol)
[3、消除魔术字符串](#3、消除魔术字符串)
[4、属性名的遍历](#4、属性名的遍历)
[5、Symbol.for/Symbol.keyFor](#5、Symbol.for/Symbol.keyFor)

JS 有了第七种数据类型：Symbol，创建一个独一无二的值；它用于对象的属性，设计初衷是为了避免对象属性冲突的问题。要获取对象symbol类型的属性，要用`Object.getOwnPropertySymbols()`；还提供了`Symbol.for()`和`Symbol.keyFor()`方法用于搜索对应的symbol值。

## 1、概述
1、原始数据类型`Symbol`：防止属性名的冲突
2、它是 JS 语言的第七种数据类型，前六种是：undefined、null、Boolean、String、Number、Object。
3、Symbol 值不是对象，不能添加属性，基本上，它是一种类似于字符串的数据类型。
4、Symbol函数可以接受一个字符串作为参数，表示对 Symbol 实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易区分。
5、Symbol 的参数是一个对象，就会调用该对象的toString方法，将其转为字符串，然后才生成一个 Symbol 值。
6、Symbol函数的参数只是表示对当前 Symbol 值的描述，因此相同参数的Symbol函数的返回值是不相等的。
7、Symbol 值不能与其他类型的值进行运算，会报错
8、Symbol 值可以显式转为字符串，Symbol 值也可以转为布尔值，但是不能转为数值。
```js
let s = Symbol();
let s1 = Symbol('foo');
let s2 = Symbol('bar');
s;  // Symbol()
typeof s  // "symbol"
s1; // Symbol(foo)
s2; // Symbol(bar)
s1.toString() // "Symbol(foo)"
s2.toString() // "Symbol(bar)"

// 5、Symbol 的参数是一个对象
const obj = {
  toString() {
    return 'abc';
  }
};
const sym = Symbol(obj);
sym   // Symbol(abc)

// 7、Symbol 值不能与其他类型的值进行运算，会报错
let sym = Symbol('My symbol');
"your symbol is " + sym   // TypeError: can't convert symbol to string
`your symbol is ${sym}` // TypeError: can't convert symbol to string

// Symbol 值可以显式转为字符串、布尔值，不能转为数值
let sym = Symbol('My symbol');
String(sym) // 'Symbol(My symbol)'
sym.toString() // 'Symbol(My symbol)'

let sym = Symbol();
Boolean(sym) // true
!sym  // false
if (sym) {}

Number(sym) // TypeError
sym + 2 // TypeError
```

## 2、作为属性名的Symbol
1、由于每一个 Symbol 值都是不相等的，这意味着 Symbol 值可以作为标识符，用于对象的属性名，就能保证不会出现同名的属性。
2、Symbol 值作为对象属性名时，不能用点运算符。
3、在对象的内部，使用 Symbol 值定义属性时，Symbol 值必须放在方括号之中。如果不放在方括号中，该属性的键名就是字符串，而不是所代表的那个 Symbol 值。
4、Symbol 值作为属性名时，该属性还是公开属性，不是私有属性。

```js
let mySymbol = Symbol();
// 第一种写法
let a = {};
a[mySymbol] = 'Hello!';
// 第二种写法
let a = {
  [mySymbol]: 'Hello'
};
// 第三种写法
let a = {};
Object.defineProperty(a, mySymbol, { value: 'Hello!' });
// 以上写法都得到同样结果
a[mySymbol] // "Hello!"

// 3、在对象的内部使用 Symbol 值定义属性
let s = Symbol();
let obj = {
  [s]: function (arg) { ... }
};
// 等价于
let obj = {
  [s](arg) { ... }
};
obj[s](123);
```

## 3、消除魔术字符串
魔术字符串指的是，在代码之中多次出现、与代码形成强耦合的某一个具体的字符串或者数值。
```js
const shapeType = {
  triangle: 'Triangle'
};
// 等价于 triangle 的值不影响
const shapeType = {
  triangle: Symbol()
};

function getArea(shape, options) {
  let area = 0;
  switch (shape) {
    case shapeType.triangle:
      area = .5 * options.width * options.height;
      break;
  }
  return area;
}

getArea(shapeType.triangle, { width: 100, height: 100 });
```

## 4、属性名的遍历
Symbol 作为属性名，该属性不会出现在`for...in`、`for...of`循环中，也不会被`Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()`返回。但是，它也不是私有属性，有一个`Object.getOwnPropertySymbols`方法，可以获取指定对象的所有 Symbol 属性名。
1、`Object.getOwnPropertySymbols`方法返回一个数组，成员是当前对象的所有用作属性名的 Symbol 值。
2、`Reflect.ownKeys`方法可以返回所有类型的键名，包括常规键名和 Symbol 键名。
3、以 Symbol 值作为名称的属性，不会被常规方法遍历得到。我们可以利用这个特性，为对象定义一些非私有的、但又希望只用于内部的方法。
```js
const obj = {};
let a = Symbol('a');
let b = Symbol('b');
obj[a] = 'Hello';
obj[b] = 'World';
Object.getOwnPropertySymbols(obj);  // [Symbol(a), Symbol(b)]

// `Reflect.ownKeys`方法
let obj = {
  [Symbol('my_key')]: 1,
  enum: 2,
  nonEnum: 3
};
Reflect.ownKeys(obj)    //  ["enum", "nonEnum", Symbol(my_key)]

// 以 Symbol 值作为名称的属性，不会被常规方法遍历得到。我们可以利用这个特性
let size = Symbol('size');
class Collection {
  constructor() {
    this[size] = 0;
  }
  add(item) {
    this[this[size]] = item;
    this[size]++;
  }
  static sizeOf(instance) {
    return instance[size];
  }
}
let x = new Collection();
Collection.sizeOf(x) // 0
x.add('foo');
Collection.sizeOf(x) // 1
Object.keys(x) // ['0']
Object.getOwnPropertyNames(x) // ['0']
Object.getOwnPropertySymbols(x) // [Symbol(size)]
```

## 5、Symbol.for/Symbol.keyFor 
1、`Symbol.for`方法重新使用同一个 Symbol 值。`Symbol.for()`与`Symbol()`这两种写法，都会生成新的 Symbol。它们的区别是，前者会被登记在全局环境中供搜索，后者不会。`Symbol.for()`不会每次调用就返回一个新的 Symbol 类型的值，而是会先检查给定的key是否已经存在，如果不存在才会新建一个值。
2、`Symbol()`写法没有登记机制，所以每次调用都会返回一个不同的值。
3、`Symbol.for`为 Symbol 值登记的名字，是全局环境的，可以在不同的 iframe 或 service worker 中取到同一个值。
4、`Symbol.keyFor`方法返回一个已登记的 Symbol 类型值的key。
```js
Symbol.for("bar") === Symbol.for("bar") // true
Symbol("bar") === Symbol("bar") // false

// 3、`Symbol.for`为 Symbol 值登记的名字，是全局环境的
iframe = document.createElement('iframe');
iframe.src = String(window.location);
document.body.appendChild(iframe);
iframe.contentWindow.Symbol.for('foo') === Symbol.for('foo')  // true

// 4、`Symbol.keyFor`
let s1 = Symbol.for("foo");
Symbol.keyFor(s1) // "foo"
let s2 = Symbol("foo");
Symbol.keyFor(s2) // undefined  变量s2属于未登记的 Symbol 值
```
