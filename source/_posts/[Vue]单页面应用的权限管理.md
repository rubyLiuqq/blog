---
title: 单页面应用的权限管理
date: 2018-10-19 10:26:43
tags: [Vue]
categories: 基础知识
toc: true
---
Vue 单页面应用的权限管理有两种方法：`接口级权限`；`页面级权限`

### 接口级权限
例子：后台好多接口发ajax请求获取数据的时候后端都会需要获取用户是否登录，并在登录的情况反馈到前端，前端跳转到登录页面。这个就可以使用这个拦截器来实现。
在请求或响应被 then 或 catch 处理前拦截它们。
```js
// 添加响应拦截器
axios.interceptors.response.use(response => {
  // 对响应数据做点什么
  return response;
}, error => {
  // 对响应错误做点什么
  return Promise.reject(error);
});
```
```js
// 添加请求拦截器
axios.interceptors.request.use(config  =>{
  // 在发送请求之前做些什么
  return config;
}, error => {
  // 对请求错误做些什么
  return Promise.reject(error);
});
```

### 页面级权限 
页面及权限主要靠vue-router来实现。基本思路是为全局注册一个“前置守卫”钩子函数router.beforeEach。
```js
const router = new VueRouter({ ... })
router.beforeEach((to, from, next) => {
  // 这里检查权限并进行跳转
  next()
})
```
每个守卫方法接收三个参数：
1、`to`:Route 即将要进入的目标
2、`from`: Route 当前导航正要离开的路由
3、`next`: Function: 一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。
`next()`: 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed （确认的）。
`next(false)`: 中断当前的导航。如果浏览器的 URL 改变了（可能是用户手动或者浏览器后退按钮），那么 URL 地址会重置到 from 路由对应的地址。
`next('/') `或者 `next({ path: '/' })`: 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。
你可以向 next 传递任意位置对象，且允许设置诸如 replace: true、name: 'home' 之类的选项以及任何用在 router-link 的 to prop 或 router.push 中的选项。
`next(error)`: (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 router.onError() 注册过的回调。