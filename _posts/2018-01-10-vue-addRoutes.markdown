---
layout:     post
title:      "VUE动态路由的实现"
subtitle:   "js归纳笔记"
date:       2018-02-04
author:     "Joubn"
header-img: "img/hello2017.jpg"
header-mask: 0.3
catalog: true
tags:
    - VUE
---

> “Hope clouds observation. ”

管理后台权限控制也是老生常谈的，今天就来谈谈VUE框架中根据用户权限显示不同的菜单。


## 实现思路

- 第一种就是先所有路由全部实例化出来，然后根据情况显示对应权限的路由
- 第二种就是根据权限去实例化对应的路由

我想我不用说大家也知道第二种方法更优越。第一种虽然看起来好像对应权限下只有这些路由但是我们可以通过地址去访问。
今天我们着重讲一下第二种的实现方法。因为具体项目更复杂的多，一些相关逻辑的附加自行发挥。我只是把主要的思路做一个笔记。

这里主要使用的是`vue-router`2.2版本新增了一个router.addRoutes(routes)方法,因为有这个方法我们才得以实现路由的动态化，之前版本还是老老实实用第一种思路去实现吧

## 实现方法
先实例化一个空的路由池(你也先把一些不用权限的页面先放进去，比如：登录、注册)，这样好处就是进来页面就可以看到 而不是要等从接口获取到权限了才能显示页面

```js
new Router({routes:[]})
```
假如获取到用户A对应的权限是以下
```js
let routes = [{
 path: '/page1',
 name: '页面一',
 component: page1,
},{
  path: '/page2',
  name: '页面二',
  component: page2,
  children: []
}]

//动态添加路由
this.$router.addRoutes(routes)

//添加完路由记得跳转一下页面（相当于刷新一下路由）
this.$router.push({path:'/')

```
至此大的效果已经出来了！是不是SO EASY!

## 注意

虽然上面步骤做完效果都出来了  但是你会发现这个`addRoutes`方法好像每次都是重新去把整个路由池给干掉再去生成...
并不像它的名字add这么友好嘛.所以还是要配合之前的路由内容进行动态生成

比如我想在`页面二`的children里再加一个页面

```js
this.$router.options.routes[1].children.push([
  path: '/page2-1',
  name: '内容二',
  component: content1,
])

this.$router.addRoutes(this.$router.options.routes)

this.$router.push({path:'/')
```

这样就能稍微友好一点的去解决这个问题 ，当然你也可去用状态管理，本地缓存什么的，这就看自己的发挥了。








