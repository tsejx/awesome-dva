### awesome-dva [![Awesome](https://cdn.rawgit.com/sindresorhus/awesome/d7305f38d29fed78fa85652e3a63e154dd8e8829/media/badge.svg)](https://github.com/sindresorhus/awesome)

A collection of awesome things regarding dva ecosystem.

#### 工具插件

* [dva](https://github.com/dvajs/dva)
* [dva-cli](https://github.com/dvajs/dva-cli) - 命令行工具，基于 [roadhog](https://github.com/sorrycc/roadhog) 构建，查看 [roadhog 配置项](https://github.com/sorrycc/roadhog/blob/master/README.md#configuration)
* [dva-loading](https://github.com/dvajs/dva/tree/master/packages/dva-loading) - 可以自动处理 loading 状态，不用一遍遍地写 showLoading 和 hideLoading
* [babel-plugin-dva-hmr](https://github.com/dvajs/babel-plugin-dva-hmr) - 实现 components、routes 和 models 的 HMR

#### 底层框架

* [redux](https://github.com/reduxjs/redux) - 为 JavaScript 应用而生的可预测状态管理器
* [react-redux](https://github.com/reduxjs/react-redux) - 官方 React 与 Redux 绑定的中间件
* [redux-saga](https://github.com/redux-saga/redux-saga) - Redux 应用的替代副作用模型
* [react-router](https://github.com/ReactTraining/react-router) - React 声明式路由
* [react-router-dom](https://github.com/ReactTraining/react-router/tree/master/packages/react-router-dom) - DOM 与 ReactRouter 绑定的中间件
* [react-router-redux](https://github.com/reactjs/react-router-redux) - 保持 ReactRouter 与 Redux 同步的绑定中间件
* [isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch) - 用于 Node 和 浏览器的同构通信框架

#### 架构思想

* [elm](http://elm-lang.org) - A delightful language for reliable webapps.
* [choo](https://github.com/choojs/choo) - A `4kb` framework for creating sturdy frontend applications [https://choo.io](https://choo.io/)

#### 文档

* [dva 官方文档](https://dvajs.com)
  * [dva 概念](https://dvajs.com/guide/concepts.html)
  * [dva API](https://dvajs.com/api/)
  * [dva 知识地图](https://dvajs.com/knowledgemap/)
  * dva 输出文件
    * `dva/router` 默认输出 react-router 接口，react-router-redux 接口通过属性 routerRedux 输出
    * `dva/fetch` 异步请求库，输出 isomorphic-fetch（[fetch](https://github.com/github/fetch)） 接口
    * `dva/saga` 输出 redux-saga 接口（[中文文档](https://redux-saga-in-chinese.js.org/)），主要用于用例的编写
* [dva 入门课](https://dvajs.com/guide/introduce-class.html)
* [dva 图解](https://dvajs.com/guide/fig-show.html)
* [dva 源码解析](<https://dvajs.com/guide/source-code-explore.html>)
* [使用 dva 开发复杂 SPA](https://github.com/dvajs/dva/blob/master/docs/guide/develop-complex-spa.md)
* [dva@2.0 发布](https://github.com/sorrycc/blog/issues/48)

#### 相关文章

* [Why dva and what's dva](https://github.com/dvajs/dva/issues/1)
* [支付宝前端应用架构的发展和选择](https://www.github.com/sorrycc/blog/issues/6) 
* [React + Redux 最佳实践](https://github.com/sorrycc/blog/issues/1) - 探索 dva 的起源
* [React 应用框架在蚂蚁金服的实践](http://slides.com/sorrycc/dva)