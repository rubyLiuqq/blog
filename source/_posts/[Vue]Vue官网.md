---
title: vue官网阅读
date: 2018-10-19 16:38:36
tags: [Vue]
categories: 基础知识
toc: true
---

## Vue实例
只有当实例被创建时 data 中存在的属性才是响应式的。
vm.b = 'hi';  // 对 b 的改动将不会触发任何视图的更新。
```js
var data = { a: 1 };
var vm = new Vue({
  data: data
});
vm.a == data.a // => true
// 设置属性也会影响到原始数据
vm.a = 2
data.a // => 2
// 反之亦然
data.a = 3
vm.a // => 3
```
`Object.freeze()`会阻止修改现有的属性，也意味着响应系统无法再追踪变化。

### 实例提供的属性和方法
```js
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})

vm.$data === data // => true
vm.$el === document.getElementById('example') // => true

// $watch 是一个实例方法
vm.$watch('a', function (newValue, oldValue) {
  // 这个回调将在 `vm.a` 改变后调用
})
```

## 模板语法 - 修饰符
修饰符 (Modifiers) 是以半角句号 `.` 指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。
例如，`.prevent` 修饰符告诉 `v-on` 指令对于触发的事件调用 `event.preventDefault()`：
<form v-on:submit.prevent="onSubmit">...</form>

## 计算属性和侦听器

### 计算属性缓存computed vs 方法methods
可以将同一函数定义为一个方法methods而不是一个计算属性computed。两种方式的最终结果确实是完全相同的。
然而，不同的是`计算属性是基于它们的依赖进行缓存的`，只在相关依赖发生改变时它们才会重新求值。

### 计算属性computed vs 侦听属性watch
#### 1、计算属性的 setter
计算属性默认只有 getter ，不过在需要时也可以提供一个 setter ：
```js
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```
现在再运行 `vm.fullName = 'John Doe'` 时，`setter` 会被调用，`vm.firstName` 和 `vm.lastName` 也会相应地被更新。

#### 2、侦听器
当需要在数据变化时执行异步或开销较大的操作时，`侦听器`最有用的
[查看官网demo](https://cn.vuejs.org/v2/guide/computed.html#%E4%BE%A6%E5%90%AC%E5%99%A8)

## Class与Style绑定
### 绑定 HTML Class - 对象语法
```js
// 方法一
<div class="static" v-bind:class="{ active: isActive, 'text-danger': hasError }"> </div>

data: {
  isActive: true,
  hasError: false
}

// 方法二
<div v-bind:class="classObject"></div>

data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
// 方法三
data: {
  isActive: true,
  error: null
},
computed: {
  classObject: function () {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```
