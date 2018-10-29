---
title: vue官网阅读
date: 2018-10-19 16:38:36
tags: [Vue]
categories: 基础知识
toc: true
---

## 一、Vue实例
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

#### 实例提供的属性和方法
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

## 二、模板语法 - 修饰符
修饰符 (Modifiers) 是以半角句号 `.` 指明的特殊后缀，用于指出一个指令应该以特殊方式绑定。
例如，`.prevent` 修饰符告诉 `v-on` 指令对于触发的事件调用 `event.preventDefault()`：
<form v-on:submit.prevent="onSubmit">...</form>

## 三、计算属性和侦听器

#### 1、计算属性缓存computed vs 方法methods
可以将同一函数定义为一个方法methods而不是一个计算属性computed。两种方式的最终结果确实是完全相同的。
然而，不同的是`计算属性是基于它们的依赖进行缓存的`，只在相关依赖发生改变时它们才会重新求值。

#### 2、计算属性computed vs 侦听属性watch
#### 计算属性的 setter
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

#### 3、侦听器watch
当需要在数据变化时执行异步或开销较大的操作时，`侦听器`最有用的
[查看官网demo](https://cn.vuejs.org/v2/guide/computed.html#%E4%BE%A6%E5%90%AC%E5%99%A8)

## 四、Class与Style绑定
#### 1、绑定 HTML Class - 对象语法
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
#### 2、绑定 HTML Class - 数组语法
```js
<div v-bind:class="[activeClass, errorClass]"></div>

data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}

// 渲染结果为：
<div class="active text-danger"></div>
```
#### 3、绑定 HTML Class - 用在组件上
当在一个自定义组件上使用 class 属性时，这些类将被添加到该组件的根元素上面。这个元素上已经存在的类不会被覆盖。
```js
// 自定义组件
Vue.component('my-component', {
  template: '<p class="foo bar">Hi</p>'
});

// 组件的引用
<my-component class="baz boo"></my-component>

// HTML 将被渲染为:
<p class="foo bar baz boo">Hi</p>
```

## 五、条件渲染 - v-if vs v-show
`v-if` 是惰性的：如果在初始渲染时条件为假，则什么也不做，直到条件第一次变为真时，才会开始渲染条件块。
`v-show` 不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。
<font color="#d00">v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。</font>
<font color="#d00">因此，如果需要非常频繁地切换，则使用 v-show 较好；如果在运行时条件很少改变，则使用 v-if 较好。</font>

## 六、列表渲染
### 1、v-if/v-show
`变异方法`：会改变被这些方法调用的原始数组。push(), pop(), shift(), unshift(), splice(), sort(), reverse()。
`非变异方法`，例如：filter(), concat() 和 slice() 。这些不会改变原始数组，但总是返回一个新数组。
<font color="#d00">注意事项：</font>
由于 JavaScript 的限制，Vue 不能检测以下变动的数组：
1、利用索引直接设置一个项时，例如：vm.items[indexOfItem] = newValue；
2、修改数组的长度时，例如：vm.items.length = newLength；
<font color="#d00">解决第一类问题：</font>
```js
// Vue.set
Vue.set(vm.items, indexOfItem, newValue);
// 也可以使用 vm.$set 实例方法，该方法是全局方法 Vue.set 的一个别名：
vm.$set(vm.items, indexOfItem, newValue);

// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue);
```
<font color="#d00">解决第二类问题：</font>
```js
vm.items.splice(newLength)
```

### 2、对象更改检测注意事项
由于 JS 的限制，Vue 不能检测对象属性的添加或删除。
可以使用 `Vue.set(object, key, value)` 方法向嵌套对象添加响应式属性。
```js
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
});
Vue.set(vm.userProfile, 'age', 27);
vm.$set(vm.userProfile, 'age', 27);
```
可能需要为已有对象赋予多个新属性，比如使用 `Object.assign()` 或 `_.extend()`。
在这种情况下，你应该用两个对象的属性创建一个新的对象。
```js
vm.userProfile = Object.assign({}, vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

### 3、v-for with v-if
<font color="#d00">当它们处于同一节点，v-for 的优先级比 v-if 更高，这意味着 v-if 将分别重复运行于每个 v-for 循环中。</font>
```js
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

### 4、一个组件的 v-for
is="todo-item" 属性。这种做法在使用 DOM 模板时是十分必要的，
因为在 `<ul>` 元素内只有 `<li>` 元素会被看作有效内容。这样做实现的效果与 `<todo-item>` 相同，但是可以避开一些潜在的浏览器解析错误。
```js
<ul>
  <li
    is="todo-item"
    v-for="(todo, index) in todos"
    v-bind:key="todo.id"
    v-bind:title="todo.title"
    v-on:remove="todos.splice(index, 1)"
  ></li>
</ul>
```

## 七、事件处理
有时也需要在内联语句处理器中访问原始的 DOM 事件。可以用特殊变量 `$event` 把它传入方法

### 1、事件修饰符
`.stop`、`.prevent`、`.capture`、`.self`、`.once`、`.passive`
```js
// 阻止单击事件继续传播
<a v-on:click.stop="doThis"></a>

// 提交事件不再重载页面
<form v-on:submit.prevent="onSubmit"></form>

// 修饰符可以串联
<a v-on:click.stop.prevent="doThat"></a>

// 只有修饰符
<form v-on:submit.prevent></form>

// 添加事件监听器时使用事件捕获模式
// 即元素自身触发的事件先在此处理，然后才交由内部元素进行处理
<div v-on:click.capture="doThis"></div>

// 只当在 event.target 是当前元素自身时触发处理函数
// 即事件不是从内部元素触发的
<div v-on:click.self="doThat"></div>

// 点击事件将只会触发一次
<a v-on:click.once="doThis"></a>

// 滚动事件的默认行为 (即滚动行为) 将会立即触发
// 而不会等待 `onScroll` 完成
// 这其中包含 `event.preventDefault()` 的情况
<div v-on:scroll.passive="onScroll">...</div>
```
使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。
因此，用 `v-on:click.prevent.self` 会阻止所有的点击，而 `v-on:click.self.prevent`只会阻止对元素自身的点击。
不要把 `.passive` 和 `.prevent` 一起使用，因为 `.prevent` 将会被忽略，同时浏览器可能会向你展示一个警告。
请记住，`.passive` 会告诉浏览器你不想阻止事件的默认行为。

### 2、按键修饰符
可以通过全局 `config.keyCodes` 对象自定义按键修饰符别名
```js
<input @keyup.enter="submit">
// 可以使用 `v-on:keyup.f1`
Vue.config.keyCodes.f1 = 112
```

### 3、系统修饰符
`.exact` 修饰符允许你控制由精确的系统修饰符组合触发的事件。
```js
// 即使 Alt 或 Shift 被一同按下时也会触发
<button @click.ctrl="onClick">A</button>

// 有且只有 Ctrl 被按下的时候才触发
<button @click.ctrl.exact="onCtrlClick">A</button>

// 没有任何系统修饰符被按下的时候才触发
<button @click.exact="onClick">A</button>
```

## 八、表单输入绑定
可以用 `v-model` 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。
它会根据控件类型自动选取正确的方法来更新元素。
`v-model` 会忽略所有表单元素的 `value`、`checked`、`selected` 特性的初始值而总是将 Vue 实例的数据作为数据来源。
你应该通过 JS 在组件的 data 选项中声明初始值。
对于需要使用`输入法 (如中文、日文、韩文等) 的语言`，会发现 `v-model` 不会在输入法组合文字过程中得到更新。
如果你也想处理这个过程，请使用 input 事件。

### 1、选择框
如果 v-model 表达式的初始值未能匹配任何选项，<select> 元素将被渲染为“未选中”状态。
在 iOS 中，这会使用户无法选择第一个选项。因为这样的情况下，iOS 不会触发 change 事件。因此，更推荐像上面这样提供一个值为空的禁用选项。
```js
<div id="example-5">
  <select v-model="selected">
    <option disabled value="">请选择</option>
    <option>A</option>
    <option>B</option>
    <option>C</option>
  </select>
  <span>Selected: {{ selected }}</span>
</div>

new Vue({
  el: '...',
  data: {
    selected: ''
  }
});

// 用 v-for 渲染的动态选项
<select v-model="selected">
  <option v-for="option in options" v-bind:value="option.value">
    {{ option.text }}
  </option>
</select>
<span>Selected: {{ selected }}</span>

new Vue({
  el: '...',
  data: {
    selected: 'A',
    options: [
      { text: 'One', value: 'A' },
      { text: 'Two', value: 'B' },
      { text: 'Three', value: 'C' }
    ]
  }
})

// 值绑定
<select v-model="selected">
  // 内联对象字面量
  <option v-bind:value="{ number: 123 }">123</option>
</select>

// 当选中时
typeof vm.selected // => 'object'
vm.selected.number // => 123
```

### 2、修饰符
`.lazy`: 在默认情况下，`v-model` 在每次 input 事件触发后将输入框的值与数据进行同步 (除了上述输入法组合文字时)。
你可以添加 `lazy` 修饰符，从而转变为使用 `change` 事件进行同步.
`.number`: 如果想自动将用户的输入值转为数值类型，可以给 `v-mode`l 添加 `number` 修饰符。
<font color="#d00">如果这个值无法被 parseFloat() 解析，则会返回原始的值。</font>
`.trim`: 如果要自动过滤用户输入的首尾空白字符，可以给 `v-model` 添加 `trim` 修饰符。

```js
// 在“change”时而非“input”时更新
<input v-model.lazy="msg">

// 即使在 type="number" 时，HTML 输入元素的值也总会返回字符串
<input v-model.number="age" type="number">

<input v-model.trim="msg">
```

### 3、解析DOM模版时需要注意
有些 HTML 元素，诸如 `<ul>`、`<ol>`、`<table>` 和 `<select>`，对于哪些元素可以出现在其内部是有严格限制的。
而有些元素，诸如 `<li>`、`<tr>` 和 `<option>`，只能出现在其它某些特定的元素内部。
```js
<table>
  <tr is="blog-post-row"></tr>
</table>
```
需要注意的是<font color="#d00">如果我们从以下来源使用模板的话，这条限制是不存在</font>的：
1、字符串 (例如：template: '...')
2、单文件组件 (.vue)
3、`<script type="text/x-template">`

如果使用了 webpack，那么就可以使用 require.context 只全局注册这些非常通用的基础组件。
```js
// src/main.js
import Vue from 'vue'
import upperFirst from 'lodash/upperFirst'
import camelCase from 'lodash/camelCase'

const requireComponent = require.context(
  // 其组件目录的相对路径
  './components',
  // 是否查询其子目录
  false,
  // 匹配基础组件文件名的正则表达式
  /Base[A-Z]\w+\.(vue|js)$/
)

requireComponent.keys().forEach(fileName => {
  // 获取组件配置
  const componentConfig = requireComponent(fileName)

  // 获取组件的 PascalCase 命名
  const componentName = upperFirst(
    camelCase(
      // 剥去文件名开头的 `./` 和结尾的扩展名
      fileName.replace(/^\.\/(.*)\.\w+$/, '$1')
    )
  )

  // 全局注册组件
  Vue.component(
    componentName,
    // 如果这个组件选项是通过 `export default` 导出的，
    // 那么就会优先使用 `.default`，
    // 否则回退到使用模块的根。
    componentConfig.default || componentConfig
  )
})
```
<font color="#d00">全局注册的行为必须在根 Vue 实例 (通过 new Vue) 创建之前发生</font>
