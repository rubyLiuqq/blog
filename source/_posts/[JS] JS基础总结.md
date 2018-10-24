---
title: JS基础总结
date: 2018-09-26 14:48:21
tags: [JS]
categories: 基础知识
toc: true
---

## 目录：
[一、JS 中的数据类型](#一、JS中的数据类型)
[二、JS 中类型检测](#二、JS中类型检测)
[三、$.extend()和$.fn.extend()](#三、-extend和-fn-extend)
[四、jquery的事件绑定on、bind、dele](#四、jquery的事件绑定on、bind、dele)
[五、defer和async](#五、defer和async)
[六、module.exports和export的区别](#六、module-exports和export的区别)
[七、Promise](#七、Promise)
[八、JS中正则表达式的使用](#八、JS中正则表达式的使用)
[九、数组](#九、数组)
[十、前端的各种排序](#十、前端的各种排序)
[十一、列举HTML5新特性](#十一、列举HTML5新特性)
[十二、列举Css3新特性](#十二、列举Css3新特性)


### 一、JS 中的数据类型
JavaScript 有 5 种简单数据类型：Undefined、Null、Boolean、Number、String 和 1 种复杂数据类型 Object 。
基本类型（值类型）：Undefined、Null、Boolean、Number、String
复杂类型（引用类型）：Object、Array、Date、RegExp、Function、基本包装类型（Boolean | Number | String）、单体内置对象（Global | Math）。
类型检测：typeof、instanceof、`Object.prototype.toString.call()`；

> **值类型与引用类型的差别:**
> * 基本类型在内存中占据固定大小的空间，因此被保存在栈内存中;
> * 从一个变量向另一个变量复制基本类型的值，复制的是值的副本;
> * 引用类型的值是对象，保存在堆内存;
> * 包含引用类型值的变量实际上包含的并不是对象本身，而是一个指向该对象的指针;
> * 从一个变量向另一个变量复制引用类型的值的时候，复制是引用指针，因此两个变量最终都指向同一个对象。

-----------

### 二、JS中类型检测
在JavaScript里使用typeof判断数据类型，只能区分基本类型，即：`number`、`string`、`undefined`、`boolean`、`object`。
对于`null`、`array`、`function`、`object`来说，使用typeof都会统一返回object字符串。

要想区分对象、数组、函数、单纯使用typeof是不行的。在JS中，可以通过`Object.prototype.toString`方法，判断某个对象之属于哪种内置类型。分为null、string、boolean、number、undefined、RegExp、array、function、object、date、math。

判断原生JSON对象: `window.JSON && Object.prototype.toString.call(JSON)` 输出结果为”[object JSON]”说明JSON是原生的，否则不是。
**注意：Object.prototpye.toString()本身也可能会被修改**

对于自定义的函数，可以使用 instanceof判断，判断一个实例是否属于某种类型。`console.log(person instanceof Person);`

-----------

### 三、$.extend和$.fn.extend
1. `extend(dest,src1,src2,src3...)`: 将src1,src2,src3...合并到dest中,返回值为合并后的dest,修改了dest的结构。
    `$.extend({},src1,src2,src3...)`  // 也就是将"{}"作为dest参数，不修改dest的结构。
```js
// 后面的参数如果和前面的参数存在相同的名称，那么后面的会覆盖前面的参数值。
$.extend({},{name:"Tom",age:21},{name:"Jerry",sex:"Boy"}); // {name:"Jerry",age:21,sex:"Boy"}
```
2. 省略 dest 参数
`$.extend(src)`: 将src合并到jquery的全局对象中去。
```js
// 将hello方法合并到jquery的全局对象中
$.extend({
    hello: function() {
        alert('hello');
    }
});
```
3. $.fn.extend(src): 将src合并到jquery的实例对象中去 (**$.fn=jquery.prototype**)
```js
// 这是在jquery全局对象中扩展一个net命名空间。
$.extend({net:{}});
　　 
// 这是将hello方法扩展到之前扩展的Jquery的net命名空间中去
$.extend($.net, {
    hello:function(){alert('hello');}
});　　
```
4. extend方法还有一个重载原型：  
`extend(boolean,dest,src1,src2,src3...)`: boolean代表是否进行深度拷贝。
```js
var result=$.extend( true, {},
    { name:"John", location: {city: "Boston",county:"USA"} },
    { last:"Resig", location: {state: "MA",county:"China"} } );
//  result = {name:"John",last:"Resig",location:{city:"Boston",state:"MA",county:"China"}}
var result=$.extend( false, {},
    { name:"John", location:{city: "Boston",county:"USA"} },
    { last:"Resig", location: {state: "MA",county:"China"} });
// result = {name:"John",last:"Resig",location:{state:"MA",county:"China"}}
```

-----------

### 四、jquery的事件绑定on、bind、dele
1. sdas

-----------

### 五、defer和async
defer可以让脚本在文档完全呈现之后再执行，延迟脚本总是按照指定它们的顺序执行。
async可表示当前脚本不必等待其他脚本，也不必阻塞文档呈现。不能保证异步脚本按照它们在页面中出现的顺序执行。

-----------

### 六、module.exports和export的区别
模块化：公共代码抽离成为一个单独的js文件，作为一个模块，模块只有通过module.exports 和 exports 才能对外暴露接口。
区别：
* exports 是指向的module.exports 的引用；
* module.exports 初始值为一个空对象{}，所以exports 初始值也是{}；
* require() 返回的是module.exports 而不是exports。

### 七、Promise
Promise代表了某个未来才会知道结果的事件（通常是一个异步操作），可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。
Promise对象有三种状态：pending状态（ 等待中），fulfilled状态（操作完成的状态），rejected状态（操作失败的状态），状态不受外界影响，一旦状态改变，就不会再变。

基本的API有：
    `Promise.resolve()`
    `Promise.reject()`
    `Promise.prototype.then()`
    `Promise.prototype.catch()`
    `Promise.all()`
    `Promise.race()`

常见使用场景：ajax请求，定时器，数据验证，动画结束回调，多重回调解耦等。

-----------

### 八、JS中正则表达式的使用
1. `String.prototype.search`方法。eg："abchello".search(/hello/); // 3
2. `String.prototype.replace`方法。eg："abchello".replace(/hello/,"hi"); // "abchi"
3. `String.prototype.split`方法。 eg: "abchelloasdasdhelloasd".split(/hello/); //["abc", "asdasd", "asd"]
4. `String.prototype.match`方法。 eg: 
用来捕获字符串中的子字符串到一个数组中。**默认情况下只捕获一个结果到数组中**。
eg：  "abchelloasdasdhelloasd".match(/hello/);  //["hello"]
     "abchelloasdasdhelloasd".match(/hello/g);  //["hello","hello"]
5. `RegExp.prototype.exec`方法。
1、exec方法一次只能捕获一份子字符串到数组中，无论正则表达式是否有全局属性
eg: `/hello/g.exec("abchelloasdasdhelloasd");` // ["hello"]
2、正则表达式对象有一个lastIndex属性，用来表示下一次从哪个位置开始捕获，每一次执行exec方法后，lastIndex就会往后推，直到找不到匹配的字符返回null，然后又从头开始捕获。 这个属性可以用来遍历捕获字符串中的子串。
``` js
var reg=/hello/g;
reg.lastIndex; //0
reg.exec("abchelloasdasdhelloasd"); // ["hello"]
reg.lastIndex; //8
reg.exec("abchelloasdasdhelloasd"); // ["hello"]
reg.lastIndex; //19
reg.exec("abchelloasdasdhelloasd"); // null
reg.lastIndex; //0
```
6. `RegExp.prototype.test`方法: 用来测试字符串中是否含有子字符串
eg: /hello/.test("abchello"); // true

### 九、数组
1. Array.prototype.flat(depth)（扁平化数组）
会递归到指定深度将所有子数组连接，并返回一个新数组。depth 指定嵌套数组中的结构深度，默认值为1
```js
var arr1 = [1, 2, [3, 4]];
arr1.flat();  // [1, 2, 3, 4]

var arr2 = [1, 2, [3, 4, [5, 6]]];
arr2.flat();  // [1, 2, 3, 4, [5, 6]]

var arr3 = [1, 2, [3, 4, [5, 6]]];
arr3.flat(2); // [1, 2, 3, 4, 5, 6]

var arr4 = [1, 2, , 4, 5];
arr4.flat();    // [1, 2, 4, 5] 会移除数组中的空项
```

### 十、前端的各种排序
![算法导图](http://pfuoi64aq.bkt.clouddn.com/1093977-20170718164528771-1976850903.jpg)

1. 快速排序
思想：  1、找基准（一般是以中间项为基准）；2、遍历数组，小于基准的放在left，大于基准的放在right；3、递归
```js
var times = 0;
function quickSort(arr) {
  //如果数组<=1,则直接返回
  if(arr.length <= 1){ return arr; }
  var pivotIndex = Math.floor(arr.length/2);
  //找基准，并把基准从原数组删除
  var pivot = arr.splice(pivotIndex,1)[0];
  //定义左右数组
  var left = [];
  var right = [];

  //比基准小的放在left，比基准大的放在right
  for(var i=0; i<arr.length; i++) {
    if(arr[i] <= pivot) {
      left.push(arr[i]);
    }
    else{
      right.push(arr[i]);
    }
    console.log("第"+(++times)+"次排序后："+arr);
  }
  //递归
  return quickSort(left).concat([pivot],quickSort(right));
}
// 数组长度10，排序次数22次。
```

2. 冒牌排序
思想： 每一次对比相邻两个数据的大小，小的排在前面，如果前面的数据比后面的大就交换这两个数的位置
```js
var times=0;
function(arr){	
  for(var i = 0;i < arr.length-1; i++) {		
    for(var j = i + 1;j < arr.length; j++) {			
      if(arr[i] > arr[j]) {
        //如果前面的数据比后面的大就交换				
        var temp = arr[i];				
        arr[i] = arr[j];				
        arr[j] = temp;			
      }		
      console.log("第"+(++times)+"次排序后："+arr);	
    }	
  } 	
  return arr;
}
// 数组长度10，排序次数45次。
```
3. 选择排序
思想：每次选择最小的数，将最小的数记录下来，然后排到最前面



### 十一、列举HTML5新特性
- 语意化标签(nav、aside、dialog、header、footer等)
- canvas
- 拖放相关api
- Audio、Video
- 获取地理位置
- 更好的input校验
- web存储(localStorage、sessionStorage)
- webWorkers(类似于多线程并发)
- webSocket

### 十二、列举Css3新特性
- 选择器
- 边框(border-image、border-radius、box-shadow)
- 背景(background-clip、background-origin、background-size)
- 渐变(linear-gradients、radial-gradents)
- 字体(@font-face)
- 转换、形变(transform)
- 过度(transition)
- 动画(animation)
- 弹性盒模型(flex-box)
- 媒体查询(@media)

### 十三、页面渲染机制
#### 1.CSS加载会阻塞DOM树吗？
```html
<head>
  <title>css阻塞</title>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    h1 {
      color: red !important
    }
  </style>
  <script>
    function h () {
      console.log(document.querySelectorAll('h1'))
    }
    setTimeout(h, 0)
  </script>
  <link href="https://cdn.bootcss.com/bootstrap/4.0.0-alpha.6/css/bootstrap.css" rel="stylesheet">
</head>
<body>
  <h1>这是红色的</h1>
</body>
```
<img src="http://pfuoi64aq.bkt.clouddn.com/1658ea252321cb0f" width="70%" height="70%">
由上图可以看出，控制台已打出console.log，说明此时的h1已经被解析，但是并没有显示。

解释：是浏览器的一种优化机制。因为加载css时，可能会修改下面DOM节点的样式，如果css加载不阻塞DOM树渲染的话，那么当css加载完之后，DOM树可能又得重新重绘或者回流了，这就造成了一些没有必要的损耗。
所以就先把DOM树的结构先解析完，把可以做的工作做完，然后等css加载完之后，在根据最终的样式来渲染DOM树。
<font color="#d00">css加载不会阻塞DOM树解析，但是会阻塞DOM树渲染</font>

#### 2.css加载会阻塞js运行吗？
```html
<head>
  <title>css阻塞</title>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <script>
    console.log('before css')
    var startDate = new Date()
  </script>
  <link href="https://cdn.bootcss.com/bootstrap/4.0.0-alpha.6/css/bootstrap.css" rel="stylesheet">
</head>
<body>
  <h1>这是红色的</h1>
  <script>
    var endDate = new Date()
    console.log('after css')
    console.log('经过了' + (endDate -startDate) + 'ms')
  </script>
</body>
```
<img src="http://pfuoi64aq.bkt.clouddn.com/1.gif" width="70%" height="70%">

可以看出，css之前的js代码已经解析完毕，但是css之后的js等到css加载完才执行。
<font color="#d00">css加载会阻塞后面的js语句的执行</font>
<font color="#d00">css会阻塞Dom渲染和js执行，而js会阻塞Dom解析</font>

提高css加载速度的方法：
1. 使用CDN(因为CDN会根据你的网络状况，替你挑选最近的一个具有缓存内容的节点为你提供资源，因此可以减少加载时间)
2. 对css进行压缩(可以用很多打包工具，比如webpack,gulp等，也可以通过开启gzip压缩)
3. 合理的使用缓存(设置cache-control,expires,以及E-tag都是不错的，不过要注意一个问题，就是文件更新后，你要避免缓存而带来的影响。其中一个解决防范是在文件名字后面加一个版本号)
4. 减少http请求数，将多个css文件合并，或者是干脆直接写成内联样式(内联样式的一个缺点就是不能缓存)

#### 3. 说明上述的原因
不用浏览器使用的内核不同，目前主要有两个：
**webkit渲染过程**
<img src="http://pfuoi64aq.bkt.clouddn.com/2" width="70%" height="70%">
**Gecko渲染过程**
<img src="http://pfuoi64aq.bkt.clouddn.com/3" width="70%" height="70%">

从上面两个流程图我们可以看出来，浏览器渲染的流程如下：
1. HTML解析文件，生成DOM Tree，解析CSS文件生成CSSOM Tree
2. 将Dom Tree和CSSOM Tree结合，生成Render Tree(渲染树)
3. 根据Render Tree渲染绘制，将像素渲染到屏幕上。

因此：
<font color="#d00">1. DOM解析和CSS解析是两个并行的进程，所以这也解释了为什么CSS加载不会阻塞DOM的解析。</font>
<font color="#d00">2. 然而，由于Render Tree是依赖于DOM Tree和CSSOM Tree的，所以他必须等待到CSSOM Tree构建完成，也就是CSS资源加载完成(或者CSS资源加载失败)后，才能开始渲染。因此，CSS加载是会阻塞Dom的渲染的。</font>
<font color="#d00">3. 由于js可能会操作之前的Dom节点和css样式，因此浏览器会维持html中css和js的顺序。因此，样式表会在后面的js执行前先加载执行完毕。所以css会阻塞后面js的执行。</font>

#### 4.DOMContentLoaded
对于浏览器来说，页面加载主要有两个事件，一个是 DOMContentLoaded ，另一个是 onLoad 。
onLoad: 等待页面的所有资源都加载完成才会触发，css、js、图片视频
DOMContentLoaded: 当页面的内容解析完成后，则触发该事件



