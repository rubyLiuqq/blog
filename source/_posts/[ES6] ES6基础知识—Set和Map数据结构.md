---
title: ES6基础知识—Set和Map数据结构
date: 2018-10-26 10:23:14
tags: [ES6]
categories: 基础知识
toc: true
---

[1、Set](#1、Set)
[2、Set实例的属性和方法](#2、Set实例的属性和方法)
[](#)
[](#)

## 1、Set
1、Set类似于数组，但是成员的值都是唯一的，没有重复的值。
2、Set 内部判断两个值是否不同，使用的算法叫做“Same-value-zero equality”，类似于精确相等运算符（===），
主要的区别是NaN等于自身，而精确相等运算符认为NaN不等于自身。
3、在 Set 内部，两个NaN是相等。
4、由于两个空对象不相等，所以Set 内部视为两个值。
```js
// 例一
const set = new Set([1, 2, 3, 4, 4]);
[...set]    // [1, 2, 3, 4]

// 例二
const items = new Set([1, 2, 3, 4, 5, 5, 5, 5]);
items.size    // 5

// 例三
const set = new Set(document.querySelectorAll('div'));
set.size    // 56
```

## 2、Set实例的属性和方法
1、属性
`Set.prototype.constructor`：构造函数，默认就是Set函数。
`Set.prototype.size`：返回Set实例的成员总数。
2、方法
Set 实例的方法分为两大类：操作方法（用于操作数据）和遍历方法（用于遍历成员）
`Array.from`方法可以将 Set 结构转为数组。

操作方法（用于操作数据）
`add(value)`：添加某个值，返回 Set 结构本身。
`delete(value)`：删除某个值，返回一个布尔值，表示删除是否成功。
`has(value)`：返回一个布尔值，表示该值是否为Set的成员。
`clear()`：清除所有成员，没有返回值。

遍历方法（用于遍历成员）
`keys()`：返回键名的遍历器
`values()`：返回键值的遍历器
`entries()`：返回键值对的遍历器
`forEach()`：使用回调函数遍历每个成员

3、Set的遍历顺序就是插入顺序
4、`keys`方法、`values`方法、`entries`方法返回的都是遍历器对象。
由于 Set 结构没有键名，只有键值（或者说键名和键值是同一个值），所以keys方法和values方法的行为完全一致。
5、Set 结构的实例默认可遍历，它的默认遍历器生成函数就是它的values方法。这意味着，可以省略values方法，直接用for...of循环遍历 Set。
6、数组的`map`和`filter`方法也可以间接用于 Set。
7、使用 Set 可以很容易地实现并集（Union）、交集（Intersect）和差集（Difference）。
```js
// 去除数组重复成员
function dedupe(array) {
  return Array.from(new Set(array));
}
dedupe([1, 1, 2, 3]) // [1, 2, 3]

[...new Set([3, 5, 2, 2, 5, 5])]; // [3, 5, 2]

// 6、数组的`map`和`filter`方法也可以间接用于 Set。
new Set([...new Set([1, 2, 3])].map(x => x * 2));  // 返回Set结构：{2, 4, 6}
new Set([...new Set([1, 2, 3, 4, 5])].filter(x => (x % 2) == 0));    // 返回Set结构：{2, 4}

// 7、使用 Set 可以很容易地实现并集（Union）、交集（Intersect）和差集（Difference）。
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);
// 并集
let union = new Set([...a, ...b]);  // Set {1, 2, 3, 4}
// 交集
let intersect = new Set([...a].filter(x => b.has(x)));  // set {2, 3}
// 差集
let difference = new Set([...a].filter(x => !b.has(x)));  // Set {1}

```