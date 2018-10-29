---
title: async、await的介绍
date: 2018-09-29 10:20:51
tags: [ES6]
categories: 基础知识
toc: true
---

1、async/await 是一种编写异步代码的新方法。之前异步代码的方案是回调和 promise。
2、async/await 是建立在 promise 的基础上。
3、async/await 像 promise 一样，也是非阻塞的。
4、async/await 让异步代码看起来、表现起来更像同步代码。


## async
async 函数（包含函数语句、函数表达式、Lambda表达式）会返回一个 Promise 对象，
如果在函数中 return 一个直接量，async 会把这个直接量通过 Promise.resolve() 封装成 Promise 对象。
```js
// 含有返回值
async function testAsync() {
    return "hello async";
}
const result = testAsync();
console.log(result);    // Promise {<resolved>: "hello async"}

// 没有返回值
async function testAsync1() {
  console.log("hello async");
}
const result = testAsync1();    // hello async
console.log(result);    // Promise {<resolved>: undefined} 因为async就有返回

```

## await 
`await` 后可以接普通函数调用或者直接量。
`await` 是个运算符，用于组成表达式，await 表达式的运算结果取决于它等的东西。
若等到的不是一个 Promise 对象，那 await 表达式的运算结果就是它等到的东西。
若等到的是一个 Promise 对象，await 就会阻塞后面的代码，等着 Promise 对象 resolve，然后得到 resolve 的值，作为 await 表达式的运算结果。

```js
function getSomething() {
  return "something";
}

async function testAsync() {
  return Promise.resolve("hello async");
}

async function test() {
  const v1 = await getSomething();
  const v2 = await testAsync();
  console.log(v1, v2);      // 'something' 'hello async'
}
test();   // Promise {<resolved>: undefined}
```

## async和await的作用
<font color="#d00">async/await 的优势在于处理 then 链</font>
async/await 的优势在于处理 then 链
```js
// 案例1
function takeLongTime() {
  return new Promise(resolve => {
    setTimeout(() => resolve("long_time_value"), 1000);
  });
}
takeLongTime().then(v => {
  console.log("got", v);
});

// 案例1 改用 async/await
function takeLongTime() {
  return new Promise(resolve => {
    setTimeout(() => resolve("long_time_value"), 1000);
  });
}
async function test() {
  const v = await takeLongTime();
  console.log(v);
}
test();
```
```js
// 案例2
function takeLongTime(n) {
  return new Promise(resolve => {
    setTimeout(() => resolve(n + 200), n);
  });
}
function step1(n) {
  console.log(`step1 with ${n}`);
  return takeLongTime(n);
}
function step2(n) {
  console.log(`step2 with ${n}`);
  return takeLongTime(n);
}
function step3(n) {
  console.log(`step3 with ${n}`);
  return takeLongTime(n);
}
// 用 Promise 方式来实现这三个步骤的处理
function doIt() {
  console.time("doIt");
  const time1 = 300;
  step1(time1)
    .then(time2 => step2(time2))
    .then(time3 => step3(time3))
    .then(result => {
      console.log(`result is ${result}`);
      console.timeEnd("doIt");
    });
}
// 用 async/await 来实现
async function doIt() {
  console.time("doIt");
  const time1 = 300;
  const time2 = await step1(time1);
  const time3 = await step2(time2);
  const result = await step3(time3);
  console.log(`result is ${result}`);
  console.timeEnd("doIt");
}
// step1 with 300
// step2 with 500
// step3 with 700
// result is 900
// doIt: 1507.251ms
```
```js
// 案例3
function step1(n) {
  console.log(`step1 with ${n}`);
  return takeLongTime(n);
}
function step2(m, n) {
  console.log(`step2 with ${m} and ${n}`);
  return takeLongTime(m + n);
}
function step3(k, m, n) {
  console.log(`step3 with ${k}, ${m} and ${n}`);
  return takeLongTime(k + m + n);
}
// 用 async/await 来写
async function doIt() {
  console.time("doIt");
  const time1 = 300;
  const time2 = await step1(time1);
  const time3 = await step2(time1, time2);
  const result = await step3(time1, time2, time3);
  console.log(`result is ${result}`);
  console.timeEnd("doIt");
}
// 用 Promise 方式来实现
function doIt() {
  console.time("doIt");
  const time1 = 300;
  step1(time1)
    .then(time2 => {
      return step2(time1, time2)
        .then(time3 => [time1, time2, time3]);
    })
    .then(times => {
      const [time1, time2, time3] = times;
      return step3(time1, time2, time3);
    })
    .then(result => {
      console.log(`result is ${result}`);
      console.timeEnd("doIt");
    });
}

// step1 with 300
// step2 with 800 = 300 + 500
// step3 with 1800 = 300 + 500 + 1000
// result is 2000
// doIt: 2907.387ms
```
