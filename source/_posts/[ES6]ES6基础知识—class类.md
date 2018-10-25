---
title: ES6基础知识—class类
date: 2018-10-25 22:01:41
tags: [ES6]
categories: 基础知识
toc: true
---

## 1、简介
1、constructor是构造方法，this代表实例对象
2、方法之间不需要逗号分隔
3、类的数据类型就是函数，类本身就指向构造函数
4、类的所有方法都定义在类的prototype属性上
5、实例的constructor方法 === 类原型的constructor方法
6、类的方法定义在prototype对象上，所以类的新方法可添加在prototype对象上。Object.assign方法可以很方便地一次向类添加多个方法。
7、prototype对象的constructor属性，直接指向“类”的本身，这与 ES5 的行为是一致的。
8、类的内部所有定义的方法，都是不可枚举的，这与 ES5 的行为是不一致的。
9、类的属性名，可以采用表达式。
10、类和模块的内部，默认就是严格模式，所以不需要使用use strict指定运行模式
11、constructor方法是类的默认方法，如果没有显式定义，一个空的constructor方法会被默认添加。
12、constructor 方法默认返回实例对象（即this），完全可以指定返回另外一个对象
13、类不存在变量提升（hoist），这一点与 ES5 完全不同，原因与继承有关
14、name属性总是返回紧跟在class关键字后面的类名。

```js
// 4、类的所有方法都定义在类的prototype属性上
class Point {
  constructor() {}
  toString() {}
  toValue() {}
}
// 等同于
Point.prototype = {
  constructor() {},
  toString() {},
  toValue() {},
};
Point.prototype.constructor === Point // true

// 5、实例的constructor方法 === 类原型的constructor方法
class B {}
let b = new B();
b.constructor === B.prototype.constructor // true

// 8、类的内部所有定义的方法，都是不可枚举的
class Point {
  constructor(x, y) { }
  toString() {}
}
Object.keys(Point.prototype)  // []
Object.getOwnPropertyNames(Point.prototype)   // ["constructor","toString"]

// toString方法是Point类内部定义的方法，它是不可枚举的。这一点与 ES5 的行为不一致。
var Point = function (x, y) {};
Point.prototype.toString = function() {};
Object.keys(Point.prototype);    // ["toString"]
Object.getOwnPropertyNames(Point.prototype);    // ["constructor","toString"]

// 9、类的属性名，可以采用表达式。
let methodName = 'getArea';
class Square {
  constructor(length) {}
  [methodName]() {}
}

// 14、name属性总是返回紧跟在class关键字后面的类名。
class Point {}
Point.name // "Point"
```

## 2、类的实例对象
1、实例的属性除非显式定义在其本身（即定义在this对象上），否则都是定义在原型上（即定义在class上）；
2、类的所有实例共享一个原型对象，与 ES5 一样。
3、可以通过实例的`__proto__`属性为“类”添加方法(必须谨慎，不推荐使用)。
```js
// 定义类
class Point {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
  toString() {
    return '(' + this.x + ', ' + this.y + ')';
  }
}
var point = new Point(2, 3);
point.toString() // (2, 3)
point.hasOwnProperty('x') // true
point.hasOwnProperty('y') // true
point.hasOwnProperty('toString') // false
point.__proto__.hasOwnProperty('toString') // true

var p1 = new Point(2,3);
var p2 = new Point(3,2);
p1.__proto__ === p2.__proto__; // true

p1.__proto__.printName = function () { return 'Oops' };
p1.printName() // "Oops"
p2.printName() // "Oops"
var p3 = new Point(4,2);
p3.printName() // "Oops"

```

## 3、私有方法和私有属性
1、在方法前加下划线，表示这是一个只限于内部使用的私有方法。
但是，这种命名是不保险的，在类的外部，还是可以调用到这个方法。
2、将私有方法移出模块。
3、利用`Symbol`值的唯一性，将私有方法的名字命名为一个Symbol值。
4、在属性名之前使用`#`表示.
5、私有属性也可以设置 getter 和 setter 方法
6、私有属性不限于从this引用，类的实例也可以引用私有属性
7、直接从实例上引用私有属性是不可以的，只能在类的定义中引用
```js
class Widget {
  // 公有方法
  foo (baz) {
    this._bar(baz);
  }
  // 私有方法
  _bar (baz) {
    return this.snaf = baz;
  }

  // 2、将私有方法移出模块；
  foo1 (baz) {
    bar.call(this, baz);
  }
}
function bar(baz) {
  return this.snaf = baz;
}

// 3、利用`Symbol`值的唯一性
const bar = Symbol('bar');
const snaf = Symbol('snaf');
export default class myClass{
  // 公有方法
  foo(baz) {
    this[bar](baz);
  }
  // 私有方法
  [bar](baz) {
    return this[snaf] = baz;
  }
};
// bar和snaf都是Symbol值，导致第三方无法获取到它们，因此达到了私有方法和私有属性的效果

// 5、私有属性也可以设置 getter 和 setter 方法
class Counter {
  #xValue = 0;
  get #x() { return #xValue; }
  set #x(value) {
    this.#xValue = value;
  }
  constructor() {
    super();
    // ...
  }
}

// 6、私有属性不限于从this引用，类的实例也可以引用私有属性
class Foo {
  #privateValue = 42;
  static getPrivateValue(foo) {
    return foo.#privateValue;
  }
}
Foo.getPrivateValue(new Foo()); // 42
```

## 4、this 的指向
1、类的方法内部如果含有this，它默认指向类的实例。但不能单独使用该方法，很可能报错。
2、解决办法：在构造方法中绑定this。
3、解决办法：使用箭头函数。
4、解决办法：使用Proxy，获取方法的时候，自动绑定this。
```js
class Logger {
  printName(name = 'there') {
    this.print(`Hello ${name}`);
  }
  print(text) {
    console.log(text);
  }
}
const logger = new Logger();
const { printName } = logger;
printName();  // TypeError: Cannot read property 'print' of undefined
logger.printName(); // Hello there

// 2、在构造方法中绑定this
class Logger {
  constructor() {
    this.printName = this.printName.bind(this);
  }
}

// 3、使用箭头函数
class Logger {
  constructor() {
    this.printName = (name = 'there') => {
      this.print(`Hello ${name}`);
    };
  }
}

// 4、使用Proxy
function selfish (target) {
  const cache = new WeakMap();
  const handler = {
    get (target, key) {
      const value = Reflect.get(target, key);
      if (typeof value !== 'function') {
        return value;
      }
      if (!cache.has(value)) {
        cache.set(value, value.bind(target));
      }
      return cache.get(value);
    }
  };
  const proxy = new Proxy(target, handler);
  return proxy;
}
const logger = selfish(new Logger());
```

## 5、Class 的取值函数（getter）和存值函数（setter）
在“类”的内部可以使用get和set关键字，对某个属性设置存值函数和取值函数，拦截该属性的存取行为。
```js
class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
}
let inst = new MyClass();
inst.prop = 123;  // setter: 123
inst.prop;   // 'getter'
```