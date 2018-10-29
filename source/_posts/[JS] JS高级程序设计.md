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

3. 工厂模式：一个方法，1、显示化创建一个对象；2、方法和属性都定义在这个对象上；3、return
解决了：创建多个相似对象的问题
问题：没有解决对象相识问题（怎么知道一个对象的类型）

4. 构造函数模式：属性和方法都在构造函数中
问题：每个方法都要在每个实例上重新创建一遍（函数定义在构造函数的内部），这种方法创建函数，会导致不同的作用域链和标识符解析。
采用方法：将方法写到全局中，构造函数内部只需要调用一次。（内部由this辨别不同的对象，将方法提出到公共中定义）
问题：在全局作用域中定义的函数实际上只能被某个对象调用，让全局作用域失去意义，并且当定义很多方法时，会失去封装的意义。
解决办法：原型模式
补充：每个实例化的对象，都有constructor（构造函数）属性指向对象。

5. 原型模式
每个函数都有一个prototype原型属性，这个属性是一个指针，指向一个对象，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。
Prototype就是通过调用构造函数而创建的那个对象实例的原型对象。
优点：可以让所有对象实例共享它所包含的属性和方法。
不必在构造函数中定义对象实例信息，而是可以将这些信息直接添加到原型对象中。
```js
function Person(){}
Person.prototype.name= "Test";
Person.prototype.age= 12;
Person.prototype.job= "Software Engineer";
Person.prototype.sayName = function() {console.log(this.name);}

const person1 = new Person();
const person2 = new Person();
person1.sayName();		// Test
person1.sayName == Person2.sayName	//  true
// person1和person2访问的都是同一组属性和函数
```
原型最初只包含 constructor 属性，而该属性也是共享的，因此可以通过对象实例访问.
访问顺序：先查找实例属性和方法，若没有，再查找原型中的。
实例中属性可以通过delete删除，取消掉覆盖性

`isPrototypeOf()` 确定对象与实例之间的关系
Person.prototype.isPrototypeOf(person1);	// true

`Object.getPrototypeOf()` 取得一个对象的原型
Object.getPrototypeOf(person1) == Person.prototype
Object.getPrototypeOf(person1).name;

`hasOwnPrototype()`检测属性存在实例中，还是原型中。原型中返回false，实例中返回true。
`in`操作符：使用情况：单独使用以及 for…in…中使用
1、单独使用：判断属性是否存在，无论实例还是原型。eg:’name’ in person1

注意：`hasOwnPrototype()和in操作符同时使用可确定一个属性是存在于对象中还是原型中`
```js
function hasPrototypePer(object, name) { 
  return !object.hasOwnPrototype(name) && (name in object);
}
```

2、for…in…循环：返回能否通过对象访问的、可枚举的属性，包括存在实例和原型中的属性

`Object.keys()` 取得对象上所有可枚举的实例属性；接受一个对象做为参数，返回所有可枚举属性的字符串数组
`Object.getOwnPrototypeNames()` 得到所有实例属性，无论可枚举，包括 constructor

6. 更简单的原型写法：用一个包含属性和方法的对象重写整个原型对象
```js
var friend = new Person();
Person.prototype = {
	constructor: Person
	name: 'Test',
	age: 12,
	job: 'Software Engineer',
	sayName: function() {console.log(this.name);}
} 
```
`没有constructor: Person`，这种写法，constructor 不再指向Person，指向Object 构造函数，虽然instanceof还能用。
`存在constructor: Person`，但会导致它的 [[Enumerable]] 特性被设置为true。默认情况，原生的constructor 属性是不可枚举的。

7. 原型的动态性：
实例创建在原型上添加方法之前时，创建的实例仍然可以访问这个新添加的方法，原因：实例与原型之间的松散连接关系。
可以随时为原型添加属性和方法，并且修改能够立即在所有对象实例中反映出来。
但此时若重写整个原型对象，实例将访问不到。原因：实例中指针指向原型，重写将会切断连接。

8. 原型对象的问题：
省略了构造函数传递初始化参数的功能，结果所有的实例在默认的情况下将取得相同的属性值。
对于属性和方法，其都是共享的，可以通过在实例中定义相同的属性名和方法对其进行覆盖，
对于包含引用类型值的属性，对其操作将会污染整个实例的该属性。
```js
Person.prototype = {
	constructor: Person
	name: ‘Test’,
	age: 12,
	friend: ['test1', 'test2' ]
	job: "Software Engineer",
	sayName: function() {console.log(this.name);}
} 
 Person1.friend.push('test3');
 person1.friend === person2.friend ;	// 污染 person2 了
```

解决办法：组合使用构造函数模式和原型模式（同时支持向构造函数传递参数）
构造函数模式定义实例属性，原型模式定义方法和共享属性。
```js
function Person(name, age) {
	this.name = name;
	this.age = age;
	this.friends = ['f1', 'f2']
}

Person.prototype = {
	construction: Person,
	sayName: function() {
		console.log(this.name);
	}
}

Const person1 = new Person('test1', 12);
Const person2 = new Person('test2', 22);

Person1.friends.push(‘f3’);
Person1.friends;	// f1,f2,f3
Person2.friends;	// f1,f2
Person1.sayName === person2.sayName;	// true
```

9. 动态原型模式
利用if判断，在方法不存在时再添加到原型
```js
function Person(name, age) {
	this.name = name;
	this.age = age;
	if (typeof this.sayName !== 'function') {
    Person.prototype.sayname = function() {}
  };
}
```
弊端：不能使用对象字面量重写原型，如果在已经创建实例的情况下重写原型，将会切断实例与新原型之间的连接。

10. 寄生构造函数模式（其他模式能用的情况下，不要使用这种方法）
```js
function Person(name, age) {
	var o = new Object();
	o.name = name;
	o.age = age;
	o.sayName = function(){
		console.log(this.name);
	}
	return o;
}
var friend = new Person('hello', 12);
friend. sayName();
```
基本思想：创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后再返回新创建的对象
```js
// 由于不能直接修改Array构造函数，可以使用以下方法
funciton specialArray() {
  var values = new Array();
	values.push.apply(values, arguments);		// 添加值
	values.toPipedString = function() {
    return this.join(‘|’);
	}
	return values;
}
Var colors = new specialArray(['red', 'blue', 'green']);
colors.toPipedString();	// red|blue|green
```
返回的对象与构造函数或者与构造函数的原型属性之间没有关系。
构造函数返回的对象与在构造函数外部创建的对象没有什么不同。
不能依赖instanceof操作符来确定对象类型。

11. 稳妥构造函数模式
稳妥对象：没有公共属性，而且其方法也不引用this的对象。
稳妥对象最适合在一些安全的环境中（这些环境中会禁止使用this和new），或者在防止数据被其他应用程序改动时使用。
稳妥构造函数和寄生构造函数的区别：
	1、新创建的对象实例方法不引用this；
	2、不实用new操作符调用构造函数。
```js
function Person(name, age) {
	var o = new Object();
	o.name = name;
	o.age = age;
	o.sayName = function(){
		console.log(name);
	}
	return o;
}
var friend = Person('hello', 12);
friend. sayName();
```

与寄生构造函数模式类似，使用稳妥构造函数创建的对象与构造函数之间也没有任何关系，因此instanceof操作符对这种对象也没有意义。

12. 继承
继承分为两种：接口继承和实现继承
接口继承：只继承方法签名
实现继承：继承实际的方法

13. 原型链：
利用原型链实现继承：利用原型让一个引用类型继承另一个引用类型的属性和方法。

方法：将父级的实例重写子级的原型，这样子级就继承了父级实例上属性和方法
父级的实例属性位于子级的原型中，父级的实例方法依旧存储于父级的实例原型中，但存在一种指针关系。
实际上，不是子级的原型的constructor属性被重写了，而是子级的原型指向了另一个对象——父级原型，而这个原型对象的constructor指向父级。

1、确定原型和实例的关系：instanceof和isPrototypeOf
eg: child instanceof parent
parent.prototype.isPrototypeOf(child);
注意：通过原型链实现继承时，不能使用对象字面量创建原型。

2、原型链的问题
问题一、包含引用类型值的原型属性会被所有实例共享。
问题二、在创建子类型的实例时，没有办法在不影响所有对象实例的情况下，给超类型的构造函数传递参数。

```js
function SuperType() {
  this.color = ['red', 'blue'];
}
function SubType() {}

// 继承了superType
SubType.prototype = new SuperType();

var instance1 = new SubType();
instance1.color.push('black');
console.log(instance1.color);		// red blue black

var instance2 = new SubType();
console.log(instance2.color);		// red blue black
```

14. 借用构造函数/伪造对象/经典继承（解决原型中包含引用类型值的问题）
思想：在子类构造函数的内部调用超类型构造函数，可以使用apply 和 call。

```js
function SuperType(name) {
  this.color = ['red', 'blue'];
	this.name = name
}
function SubType() {
	SuperType.call(this, 'Hello');
	this.age = 19;
}
var instance1 = new SubType();
instance1.color.push('black');
console.log(instance1.color);		// red blue black
console.log(instance1.name);		// Hello

var instance2 = new SubType();
console.log(instance2.color);		// red blue
```
缺点：方法都在构造函数中定义，因此函数复用就无从谈起。并且在超类型的原型中定义的方法，对子类型而言也是看不见的。

15. 组合继承/伪经典继承
思想：使用原型链实现对原型属性和方法的继承，通过借用构造函数来实现对实例属性的继承。
调用了两次超类型的构造函数：1、创建子类型的原型时；2、子类型构造函数的内部。
```js
function SuperType(name) {
	this.name = name;
  this.color = ['red', 'blue'];
}
SuperType.prototype.sayName = function() {console.log(this.name);}

function SubType(name, age) {
	SuperType.call(this,  name);			// 第二次调用
	this.age = age;
}
SubType.prototype = new SuperType();			// 第一次调用
SubType.sayAge = function() {console.log(this.age);}

var instance1 = new SubType('Hello', 19);
instance1.color.push('black');
console.log(instance1.color);		// red blue black
console.log(instance1.name);		// Hello
console.log(instance1.age);		// 19

var instance2 = new SubType('Hello1', 17);
console.log(instance2.color);		// red blue
console.log(instance2.name);		// Hello1
console.log(instance2.age);		// 17
```
`Instance` 和 `isPrototypeof()`也能够用于识别基于组合继承创建的对象。
组合继承最大的问题：会调用两次超类型构造函数：
第一次调用SuperType构造函数时，SubType.prototype会得到name 和 color的属性，现位于 SubType的原型中。
当调用SubType构造函数时，又会调用一次SuperType构造函数，这时又在新对象上创建了实例属性name 和 color，因此此时屏蔽了原型中的两个属性。

16. 原型式继承：
`Object.create`(用作新对象原型的对象，需要修改的属性值对象)
其中Object.create的原理为：
```js
	function object(o) {
		function F() {};
		F.prototype = o;
		return new F();
	}
```
```js
var person = {
	name: 'Hello',
	friends: ['red', 'blue']
}

var anotherPerson = Object.create(person);
anotherPerson.name = 'Gery';
anotherPerson.name.friends.push('green');

var yetAnotherPerson = Object.create(person);
yetAnotherPerson.name = 'Linda';
yetAnotherPerson.name.friends.push('pink');

Console.log(person.friends);	// red blue green pink
```
想让一个对象与另一个对象保持类似的情况时，可以使用，但是依旧存在引用类型值的属性被共享的情况

17. 寄生式继承
寄生式继承的思路与寄生构造函数和工厂模式类似，即创建一个仅用于封装继承过程的函数。
```js
function createAnother(original) {
  var clone = object(original);   // 通过调用函数创建一个新对象
  clone.sayHi = function() {  // 以某种方式来增强这个对象
    console.log('hi');
  };
  return clone;     // 返回这个对象
}
var person = {
  name: 'Hello',
  friends: ['red', 'blue']
};

var anotherPerson = createAnother(person);
anotherPerson.sayHi();    // hi
```
前面示范继承时使用的object() 函数不是必需的，任何能够返回新对象的函数都使用于此模式。
使用寄生式继承来为对象添加函数，会由于不能做到函数复用而降低效率；这一点与构造函数模式类似。

18. 寄生组合式继承
通过构造函数来继承属性，通过原型链的混成模式来继承方法。使用寄生式继承来继承超类型的原型，然后再将结果指定给自类型的原型。
思想：不必为了指定子类型的原型而调用超类型的构造函数，需要超类型原型的副本。
```js
function inheritPrototype(subType, superType) {
  var prototype = object(superType.prototype);    // 创建对象
  prototype.constructor = subType;    // 增强对象
  subType.prototype = prototype;    // 指定对象
}

function SuperType (name) {
  this.name = name;
  this.colors = ['red', 'blue']
} 
SuperType.prototype.sayName = function() {
  console.log(this.name);
}
function SubType(name, age) {
  SuperType.call(this, name);

  this.age = age;
}
inheritPrototype(SubType, SuperType);

SubType.prototype.sayAge = function() {
  console.log(this.age);
}
```
只调用了一次 SuperType 构造函数，避免了在 SuperType.prototype 上创建不必要的、多余的属性；
原型链还能保持不变；
能够正常使用 instanceof 和 isPropertyOf() 

19. 总结
工厂模式，使用简单的函数创建对象，为对象添加属性和方法，然后返回对象。
构造函数模式，可以创建自定义引用类型，可以像创建内置对象实例一样使用new操作符。不过，缺点：它的每个成员都无法得到复用，包括函数。由于函数可以不局限于任何对象，因此没有理由不在多个对象间共享函数。
原型模式，使用构造函数的prototype属性来指定共享的属性和方法。
组合使用构造函数模式和原型模式时，使用构造函数定义实例属性，使用原型定义共享的属性和方法。

JS主要通过原型链实现继承。
原型链的构建是通过将一个类型的实例赋值给另一个构造函数的原型实现的。这样，子类型就能访问超类型的所有属性和方法。
原型链的问题：对象实例共享所有继承的属性和方法，因此不适合单独使用。解决办法需要利用构造函数，即在子类型构造函数的内部调用超类型构造函数。
组合继承：使用原型链模式继承共享的属性和方法，借用构造函数继承实例属性。

原型式继承：可以在不必预先定义构造函数的情况下实现继承，本质是执行对给定对象的浅复制。
寄生式继承：基于某个对象或某些信息创建一个对象，然后增强对象，最后返回对象。为了解决组合继承模式由于多次调用超类型构造函数而导致的低效率问题，可以将这个模式与组合继承一起使用——寄生组合式继承

-----------
