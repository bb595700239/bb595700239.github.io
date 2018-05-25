---
layout:     post
title:      "微信分享(JS-SDK)注意项(二次分享、分享页返回首页)"
subtitle:   "微信分享"
date:       2018-05-25
author:     "Joubn"
header-img: "img/contact-bg.jpg"
header-mask: 0.3
catalog: true
tags:
    -微码
---

> “Nothing for nothing. ”

## 序言

微信分享？what  这个东西有什么好说的呀不就是调个SDK的事情么，本来我也觉得没什么好特意写篇文章的，但是发现这个坑今天又踩了，而且踩出快感了。所以才决定写出来给自己和大家提个醒。
（默认大家会配微信CONFIG,会调JS-SDK）

### 二次分享

故名思义就分享的页面被访问再次分享的意思。感觉好像没有什么问题呀，BUT微信会给分享的页面添加两个参数

>朋友圈   from=timeline&isappinstalled=0
>
>微信群   from=groupmessage&isappinstalled=0
>
>好友分享 from=singlemessage&isappinstalled=0

本来`http://localhost/#/index?id=1769`这样的地址被改成
`http://localhost/?from=singlemessage&isappinstalled=0#/index?id=1769`这样了。虽然这样好像也能访问，但是会有一个致命的问题那就是会导致页面签名失败从而导致第二次分享就会出问题（访问地址和后台的签名地址不一致了）。
 
 所以就要用下面的代码重载页面：
 ```js
if(window.location.href.indexOf('from=')!=-1 && window.location.href.indexOf('isappinstalled=')!=-1){
        window.location.href = 重置网址
}
```

这样这个问题就轻松解决了！

### 返回首页

这个需求应该是最常见了，就是微信分享出去的页面再返回要跳到首页。真实情况是我们点返回直接关闭页面了。(这里的返回按钮指的是微信头部自带的)

#### 监听popstate事件
如果您的应用是`hash`形式跳转的单页面应用
就只能用下面的代码：

```js
if('这是分享的页面'){
	window.goto = () => {
      //do something
    }
    window.addEventListener("popstate", goto, false);
    function pushHistory() {
      const state = {
        title: "title",
        url: window.location.href
      }
      window.history.pushState(state, state.title, state.url);
    }
    pushHistory();
}
```
先在`history`里再加一个当前页面，这样点返回还能留在当前页面。然后监听`popstate`返回事件就OK了。

这种方案是通用的 基本情况都可以使用。

#### document.referrer

这里要提一下`document.referrer`这个方法（对`hash`形式跳转是不起作用的）。

如果返回按钮是我们自己写的用这个方式是个不错的选择。它的作用就是记录上一页referrer信息。

所以就可以这样判断：

```js
if (document.referrer === '') {
    // 没有来源页面信息的时候
    //do something
}
```



最后，还有个最坑的，有没有发现一般我都把最重要的放到后面😄。

就是`hash`形式跳转的单页面应用在打开页面的时候一定要带上后面的hash值 如：[http://hpweb.hpaxcf.com/#/index](http://hpweb.hpaxcf.com/#/index)
而不要用[http://hpweb.hpaxcf.com](http://hpweb.hpaxcf.com)这种（如果是这种情况android端不管你分享的是什么页面打开都跳到首页），我发现我竟然也找不到原因。关键是分享是成功的，分享的链接地址也是对的，只是分享出去后链接就被改了。 可能是某种玄学吧！散了散了！














