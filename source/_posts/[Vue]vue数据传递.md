---
title: vue数据传递
date: 2018-10-18 16:47:41
tags: [JS]
categories: 基础知识
toc: true
---

## 父组件到子组件
### 方法一：Prop方法
在父组件调用子组件时通过v-bind(:)绑定动态数据，在子组件，使用Prop方法（单项绑定，防止数据倒流）
父：
<edit-dialog :dialogData="dialogData" :rendom="new Date().getTime()"></edit-dialog>

子：
```js
props: {
  dialogData: Object,
  rendom: Number
}
```
子组件中通过watch方法来监控数据是否改变，可触发相应的方法。

### 方法二：provide/inject
provider/inject：在父组件中通过provider来提供变量，在子组件中通过inject来注入变量。 
不论子组件有多深，只要调用了inject那么就可以注入provider中的数据。而不是局限于只能从当前父组件的prop属性来获取数据，只要在父组件的生命周期内，子组件都可以调用。
```js
// 父组件
export default {
  name: "Parent",
  provide: {     //重要一步，在父组件中注入一个变量
    for: "demo"
  },
  components:{
    childOne
  }
}

// 子组件
export default {
  name: "childOne",
  inject: ['for'],   //子孙组件中使用inject接住变量即可
}
```

## 子组件到父组件
### 方法一：发送/监听事件
在父组件中使用v-on(@)绑定自定义事件接收，在子组件中使用$emit来监控，并传给回掉。
父：
<edit-dialog @message="recieveMessage"></edit-dialog>

子：
this.$emit('message', data)

### 方法二：$children、$ref或$parent
`$children`: 返回所有子组件的实例，是一个数组。如果能清楚的知道子组件的顺序，也可以使用下标来操作；
```js
for(let i = 0; i < this.$children.length; i++) {
  console.log(this.$children[i].children_data);
  this.$children[i].children_fun();
}
```
1、`$ref`：给子组件做标记，即可在父组件中调用到子组件的数据和方法
首先在子组件做标记： <firstchild ref="one"></firstchild>
然后在父组件中，通过`this.$refs.one`就可以访问该子组件，包括访问子组件的data里面的数据，调用它的函数。
2、`ref` 被用来给元素或子组件注册引用信息。引用信息将会注册在父组件的 $refs 对象上。
如果在普通的 DOM 元素上使用，引用指向的就是 DOM 元素; 
如果用在子组件上，引用就指向组件实例。
3、子组件访问根组件`$root`当前组件树的根 Vue 实例。如果当前实例没有父实例，此实例将会是其自已。
4、`$parent`在子组件中调用父组件的方法或获得其数据。
```js
// parents.vue
<template>
  <div id='parents'>
            <p>我是父组件
              <button @click="click1hanlde">获取子组件1的数据与方法</button>
              <button @click="click2hanlde">获取所有子组件的数据和方法</button></p>
             <children1 ref="children1"></children1>
             <children2 ref="children2"></children2>
  </div>
</template>
<script>
  import children1 from './children1.vue'
  import children2 from './children2.vue'
  export default {
    components:{
      children1, children2
    },
    data() {
      return {
        ParentData:'AAA'
      };
    },
    methods:{
      click1hanlde(){
        console.log(this.$refs.children1.children_data)
        this.$refs.children1.children_fun();
      },
      click2hanlde(){
        for(let i=0;i<this.$children.length;i++){
          console.log(this.$children[i].children_data);
          this.$children[i].children_fun();
        }
      },
      showParentData(){
            console.log(this.ParentData)
      }
    }
  };
</script>

```

## 非父子组件 eventBus / vuex
有时候两个组件也需要通信(非父子关系)。在简单的场景下，可以使用一个空的 Vue 实例作为中央事件总线：
```js
// eventBus.js
import Vue from 'vue'
export default new Vue();

// 发送事件（发送数据）
import bus from '@/bus';
bus.$emit('childa-message', this.data); // 方法内执行下面动作

// 组件内监听（接收数据组件）
import bus from '@/bus';
bus.$on('childa-message', function(data) {    // 方法内执行下面动作
  console.log('I get it');
});
```
[了解整个vue](http://pfuoi64aq.bkt.clouddn.com/vue.webp)
