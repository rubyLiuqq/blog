---
title: 跨域的实现方法
date: 2018-10-15 11:09:12
tags: [JS]
categories: 基础知识
toc: true
---

同源： “协议 + 域名 + 端口” 三者相同，即便两个不同的域名指向同一个 ip 地址，也非同源。
同源策略限制下 `cookie`、`localStorage`、`dom`、`ajax`、`IndexDB` 都是不支持跨域的。

## 方法一、使用 jsonp 跨域
```js
// 封装 jsonp 跨域请求的方法
function jsonp({ url, params, cb }) {
  return new Promise((resolve, reject) => {
    // 创建一个 script 标签帮助我们发送请求
    let script = document.createElement("script");
    let arr = [];
    params = { ...params, cb };

    // 循环构建键值对形式的参数
    for (let key in params) {
        arr.push(`${key}=${params[key]}`);
    }

    // 创建全局函数
    window[cb] = function(data) {
        resolve(data);
        // 在跨域拿到数据以后将 script 标签销毁
        document.body.removeChild(script);
    };

    // 拼接发送请求的参数并赋值到 src 属性
    script.src = `${url}?${arr.join("&")}`;
    document.body.appendChild(script);
  });
}
```
<font color="#dd0000">缺点：</font>
只能发送 get 请求 不支持 post、put、delete；
不安全，容易引发 xss 攻击。

## 方法二、使用 CORS 跨域
<font color="#dd0000">使用自定义的 HTTP 头部让浏览器与服务器进行沟通，从而决定请求或响应是应该成功，还是应该失败。</font>

使用场景：多用于开发时，前端与后台在不同的 ip 地址下进行数据访问。
案例说明：通过访问 http://localhost:3000/index.html（服务器1） 获取 index.html 文件并执行其中的 Ajax 请求 http://localhost:4000/getDate（服务器2） 接口去获取数据。
```js
let xhr = new XMLHttpRequest();
// 正常 cookie 是不允许跨域的
document.cookie = 'name=hello';
// cookie 想要实现跨域必须携带凭证
xhr.withCredentials = true;

// xhr.open('GET', 'http://localhost:4000/getDate', true);
xhr.open('PUT', 'http://localhost:4000/getDate', true);

// 设置名为 name 的自定义请求头
xhr.setRequestHeader('name', 'hello');

xhr.onreadystatechange = function () {
  if(xhr.readyState === 4) {
    if(xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
      // 打印返回的数据
      console.log(xhr.response);

      // 打印后台设置的自定义头信息
      console.log(xhr.getResponseHeader('name'));
    }
  }
}
xhr.send();
```
```js
// 服务器2
const express = require("express");
let app = express();

// 允许访问域的白名单
let whiteList = ["http://localhost:3000"];

app.use(function(req, res, next) {
  let origin = req.header.origin;
  if (whiteList.includes(origin)) {
    // 设置那个源可以访问我，参数为 * 时，允许任何人访问，但是不可以和 cookie 凭证的响应头共同使用
    res.setHeader("Access-Control-Allow-Origin", origin);
    // 想要获取 ajax 的头信息，需设置响应头
    res.setHeader("Access-Control-Allow-Headers", "name");
    // 处理复杂请求的头
    res.setHeader("Access-Control-Allow-Methods", "PUT");
    // 允许发送 cookie 凭证的响应头
    res.setHeader("Access-Control-Allow-Credentials", true);
    // 允许前端获取哪个头信息
    res.setHeader("Access-Control-Expose-Headers", "name");
    // 处理 OPTIONS 预检的存活时间，单位 s
    res.setHeader("Access-Control-Max-Age", 5);
    // 发送 PUT 请求会做一个试探性的请求 OPTIONS，其实是请求了两次，当接收的请求为 OPTIONS 时不做任何处理
    if (req.method === "OPTIONS") {
      res.end();
    }
  }
  next();
});

app.put("/getDate", function(req, res) {
  // res.setHeader('name', 'nihao'); // 设置自定义响应头信息
  res.end("I love you");
});

app.get("/getDate", function(req, res) {
  res.end("I love you");
});

app.use(express.static(__dirname));
app.listen(4000);
```

## 方法三、使用 postMessage 实现跨域
postMessage 是 H5 的新 API，跨文档消息传送（cross-document messaging），有时简称为 XMD，指的是在来自不同域的页面间传递消息。
<font color="#dd0000">使用场景：不是使用 Ajax 的数据通信，更多是在两个页面之间的通信，在 A 页面中引入 B 页面，使用iframe 标签, 在 A、B 两个页面之间通信。</font>
```html
<!-- a.html -->
<body>
  <iframe src="http://localhost:4000/b.html" id="frame" onload="load()"></iframe>
  <script>
    function load() {
      let frame = document.getElementById('frame');
      frame.contentWindow.postMessage('I love you', 'http://localhost:4000');
      window.onmessage = function (e) {
        console.log(e.data);
      }
    }
  </script>
</body>
```
```html
<!-- b.html -->
<body>
  <script>
    window.onmessage = function (e) {
      // 打印来自页面 A 的消息
      console.log(e.data);
      // 给页面 A 发送回执
      e.source.postMessage('I love you, too', e.origin);
    }
  </script>
</body>
```


## 方法四、使用 window.name 实现跨域
同样是页面之间的通信，需要借助 iframe 标签
案例说明：A 页面和 B 页面是同域的 http://localhost:3000，C 页面在独立的域 http://localhost:4000。
在 A 页面中将 iframe 的 src 指向 C 页面，在 C 页面中将属性值存入 window.name 中，
再把 iframe 的 src 换成同域的 B 页面，在当前的 iframe 的 window 对象中取出 name 的值，访问 http://localhost:3000/a.html。
```html
<!-- a.html -->
<body>
  <iframe src="http://localhost:4000/c.html" id="frame" onload="load()"></iframe>
  <script>
    // 增加一个标识，第一次触发 load 时更改地址，更改后再次触发直接取值
    let isFirst = true;
    function load() {
      let frame = document.getElementById('frame');
      if(isFirst) {
        frame.src = 'http://localhost:3000/b.html';
        isFirst = false;
      } else {
        console.log(frame.contentWindow.name);
      }
    }
  </script>
</body>
```
```html
<!-- c.html -->
<body>
  <script>
    window.name = 'I love you';
  </script>
</body>
```


## 方法五、使用 location.hash 实现跨域
<font color="#dd0000">与 window.name 跨域的情况相同，是不同域的页面间的参数传递</font>
案例说明：需要借助 iframe 标签，A 页面和 B 页面是同域的 http://localhost:3000，C 页面是独立的域 http://localhost:4000。
A 页面通过 iframe 引入 C 页面，并给 C 页面传一个 hash 值，C 页面收到 hash 值后创建 iframe 引入 B 页面，
把 hash 值传给 B 页面，B 页面将自己的 hash 值放在 A 页面的 hash 值中，访问 http://localhost:3000/a.html。
```html
<!-- a.html -->
<body>
  <iframe src="http://localhost:4000/c.html#Iloveyou" id="frame"></iframe>
  <script>
    // 使用 hashchange 事件接收来自 B 页面设置给 A 页面的 hash 值
    window.onhashchange = function () {
      console.log(location.hash);
    }
  </script>
</body>
```
```html
<!-- c.html -->
<body>
  <script>
    // 打印 A 页面引入 C 页面设置的 hash 值
    console.log(location.hash);
    let iframe = document.createElement('iframe');
    iframe.src = 'http://localhost:3000/b.html#Iloveyoutoo';
    document.body.appendChild(iframe);
  </script>
</body>
```
```html
<!-- b.html -->
<body>
  <script>
    // 将 C 页面引入 B 页面设置的 hash 值设置给 A页面
    window.parent.parent.location.hash = location.hash;
  </script>
</body>
```

## 方法六、使用 document.domain 实现跨域
<font color="#dd0000">使用场景：不是万能的跨域方式，大多使用于同一公司不同产品间获取数据，必须是一级域名和二级域名的关系，如 www.baidu.com 与 video.baidu.com 之间。</font>
```html
<!-- http://www.domainacross.com:3000/a.html。 -->
<body>
  <p>我是页面 A 的内容</p>
  <iframe src="http://sucess.domainacross.com:3000/b.html" onload="load()" id="frame"></iframe>
  <script>
    document.domain = 'domainacross.com';
    function load() {
      console.log(frame.contentWindow.message);
    }
  </script>
</body>
```
```html
<body>
  <p>我是 B 页面的内容</p>
  <script>
    document.domain = 'domainacross.com';
    var message = 'Hello A';
  </script>
</body>
```

## 方法七、使用 WebSocket 实现跨域
WebSocket 没有跨域限制，高级 API（不兼容），想要兼容低版本浏览器，可以使用 socket.io 的库，
WebSocket 与 HTTP 内部都是基于 TCP 协议，区别在于 HTTP 是单向的（单双工），WebSocket 是双向的（全双工），
协议是 ws:// 和 wss:// 对应 http:// 和 https://，因为没有跨域限制，所以使用 file:// 协议也可以进行通信。
```html
<body>
  <script>
    // 创建 webSocket
    let socket = new WebSocket('ws://localhost:3000');
    // 连接上触发
    socket.onopen = function () {
      socket.send('I love you');
    }
    // 收到消息触发
    socket.onmessage = function (e) {
      // 打印收到的数据
      console.log(e.data); // I love you, too
    }
  </script>
</body>
```
```js
// 服务器
const express = require("express");
let app = express();

// 引入 webSocket
const WebSocket = require("ws");
// 创建连接，端口号与前端相对应
let wss = new WebSocket.Server({ port: 3000 });

// 监听连接
wss.on("connection", function(ws) {
  // 监听消息
  ws.on("message", function(data) {
    // 打印消息
    console.log(data); // I love you
    // 发送消息
    ws.send("I love you, too");
  });
});
```

## 方法八、使用 nginx 实现跨域

## 方法九、使用 http-proxy-middleware 实现跨域
NodeJS 中间件 http-proxy-middleware 实现跨域代理，原理大致与 nginx 相同，都是通过启一个代理服务器，实现数据的转发，
也可以通过设置 cookieDomainRewrite 参数修改响应头中 cookie 中的域名，实现当前域的 cookie 写入，方便接口登录认证。