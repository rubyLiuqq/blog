---
title: Object 创建/修改/获取属性的方法
date: 2018-09-26 18:08:12
tags: [JS]
toc: true
categories: 基础知识
cover: http://pe7k320h3.bkt.clouddn.com/psb%20%287%29.jpeg
---

###  Object.defineProperty(obj, prop, descriptor)
会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。
若不指定configurable, writable, enumerable ，则这些属性默认值为false；
若不指定value, get, set，则这些属性默认值为undefined。

- obj: 需要被操作的目标对象
- prop: 目标对象需要定义或修改的属性的名称
- descriptor: 将被定义或修改的属性的描述符

``` js
var obj = new Object();
Object.defineProperty(obj, 'name', {
    configurable: false,
    writable: true,
    enumerable: true,
    value: '张三'
})
console.log(obj.name)  //张三
```

### Object.defineProperties(obj, props)
直接在一个对象上定义一个或多个新的属性或修改现有属性，并返回该对象。
- props: 该对象的一个或多个键值对定义了将要为对象添加或修改的属性的具体配置。

```js
var obj = new Object();
Object.defineProperties(obj, {
    name: {
        value: '张三',
        configurable: false,
        writable: true,
        enumerable: true
    },
    age: {
        value: 18,
        configurable: true
    }
});
console.log(obj.name, obj.age) // 张三, 18
```

在不支持 Object.defineProperties() 方法的浏览器中不能修改[[configurable]]和[[enumerable]]。

### Object.getOwnPropertyDescriptor(obj, prop)
返回指定对象上一个自有属性对应的属性描述符。
- prop: 目标对象内属性名称。

```js
var person = {
    name: '张三',
    age: 18
};
var desc = Object.getOwnPropertyDescriptor(person, 'name'); 
console.log(desc);
// {
//     configurable: true,
//     enumerable: true,
//     writable: true,
//     value: "张三"
// }
```

### Object.getOwnPropertyDescriptors(obj, prop)
所指定对象的所有自身属性的描述符，如果没有任何自身属性，则返回空对象。
返回值是一个对象，若是访问器属性，该对象有Configurable、Enumberable、Get和set；
若是数据属性，该对象有Configurable、Enumberable、Writable和Value；

```js
var book = {};
Object.defineProperties(book, {
    _year: {
        value: 2004
    },
    edition: {
        value: 1
    },
    year: {
        get: function() {
            return this._year;
        },
        set: function(newValue) {
            if(newValue > 2004) {
                this._year = newValue;
                this.edition += newValue - 2004;
            }
        }
    }
});

var descriptor =  Object.getOwnPropertyDescriptors(book, '_year');
descriptor.value;               // 2004
descriptor.configurable;        // false
typeof descriptor.get;          // undefined

var desc = Object.getOwnPropertyDescriptors(book, 'year');
descriptor.value;               // undefined
descriptor.enumberable;         // false
typeof descriptor.get;          // function
```

## 补充说明
在调用 Object.defineProperty() 方法时，如果不指定，configrubale、enumerable和writable 默认都为false。

#### configrubale
设置configrubale属性为false，则不可使用delete操作符(在严格模式下抛出错误), 修改所有内部属性值会抛出错误。

#### enumerable
#### writable
#### get 和 set
