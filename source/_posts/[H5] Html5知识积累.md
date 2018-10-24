---
title: Html5知识积累
date: 2018-09-30 14:59:31
tags: [H5]
categories: 基础知识
toc: true
cover: http://pe7k320h3.bkt.clouddn.com/psb%20%287%29.jpeg
---

## sessionStorage 和 localStorage
`Web Storage`包括了两种存储方式：`sessionStorage`和`localStorage`。
`sessionStorage`用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁，生命周期为当前窗口或标签页，一旦窗口或标签页被永久关闭了。
  因此sessionStorage不是一种持久化的本地存储，仅仅是会话级别的存储。
  页面及标签页仅指顶级窗口，如果一个标签页包含多个iframe标签且他们属于同源页面，那么他们之间是可以共享sessionStorage的。
`localStorage`用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。

`web storage`和`cookie`的区别：
Web Storage是为了更大容量存储而设计。
Cookie的大小是受限的，并且每次你请求一个新的页面的时候Cookie都会被发送过去，这样无形中浪费了带宽，另外cookie还需要指定作用域，不可以跨域调用。

Web Storage拥有`setItem`,`getItem`,`removeItem`,`clear`等方法，不像cookie需要开发者自己封装`setCookie`,`getCookie`。

Cookie优点：Cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在 ，而Web Storage仅仅是为了在本地“存储”数据而生。

html5 web storage的浏览器支持情况: 
浏览器的支持除了IE７及以下不支持外，其他标准浏览器都完全支持。通过简单的代码封装可以统一到所有的浏览器都支持web storage。
```js
if(window.localStorage){ 
  // 浏览支持localStorage
}else{
  // 浏览暂不支持localStorage
}

//或者 
if(typeof window.localStorage == 'undefined'){
  // 浏览暂不支持localStorage
}
```
`setItem`存储value 用途，.setItem(key, value)
`getItem`获取value 用途，.getItem(key) 
`removeItem`删除key 用途，.removeItem(key)
`clear`清除所有的key/value 用途， .clear()
`localStorage`和`sessionStorage`的key和length属性实现遍历: `sessionStorage`和`localStorage`提供的key()和length可以方便的实现存储的数据遍历.

```js 
sessionStorage.setItem("key", "value");     
localStorage.setItem("site", "js8.in");

var value = sessionStorage.getItem("key");     
var site = localStorage.getItem("site");

sessionStorage.removeItem("key");     
localStorage.removeItem("site");

sessionStorage.clear();     
localStorage.clear();

var storage = window.localStorage; 
for (var i=0, len = storage.length; i < len; i++){
  var key = storage.key(i); 
  var value = storage.getItem(key); 
  console.log(key + "=" + value); 
}
```

## web storage 的离线存储
H5 的 Web storage API 采用了离线缓存，会生成一个清单文件（manifest file)，这个清单文件实质就是一系列的URL列表文件，这些URL分别指向页面当中的html,css,javascript,图片等相关内容。
当使用离线应用时，应用会引入这一清单文件，浏览器会读取这一文件，下载相应的文件，并将其缓存到本地。
使得这些web应用能够脱离网络使用，而用户在离线时的更改也同样会映射到清单文件中，并在重新连线之后将更改返回应用，工作方式与我们现在所使用的网盘有着异曲同工之处。
首先，需要在页面头加入manifest属性：
```html
<!DOCTYPE HTML>
<html manifest = "cache.manifest">
...
</html>
```
然后cache.manifest文件的书写方式为：
```
CACHE MANIFEST
#v0.11
CACHE:
js/app.js
css/style.css
NETWORK:
resourse/logo.png
FALLBACK:
/ /offline.html
```
离线存储的manifest一般由三个部分组成:
1. CACHE:表示需要离线存储的资源列表，由于包含manifest文件的页面将被自动离线存储，所以不需要把页面自身也列出来。
2. NETWORK:表示在它下面列出来的资源只有在在线的情况下才能访问，他们不会被离线存储，所以在离线情况下无法使用这些资源。不过，如果在CACHE和NETWORK中有一个相同的资源，那么这个资源还是会被离线存储，也就是说CACHE的优先级更高。
3. FALLBACK:表示如果访问第一个资源失败，那么就使用第二个资源来替换他，比如上面这个文件表示的就是如果访问根目录下任何一个资源失败了，那么就去访问offline.html。

http://jartto.wang/2016/07/25/make-an-inventory-of-html5-api/