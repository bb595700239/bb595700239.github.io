---
layout:     post
title:      "rem 开发自适应网页"
subtitle:   "微码归纳笔记"
date:       2017-11-27
author:     "Joubn"
header-img: "img/hello2017.jpg"
header-mask: 0.3
catalog: true
tags:
    - 前端开发
    - rem
    - 微码
---

> “Eternity is said not to be an extension of time but an absence of time. ”

## 简介

rem顾名思义就是root element，这个是一个相对的单位，与em的差别在于rem相对的是根节点html的font-size,em相对的是父元素font-size。使用rem其实是用媒体查询修来改html的font-size，那么使用rem的元素的大小也会相应的改变，所以我们只要写一份css就可以适配所有手机的屏幕了。虽然rem在某些浏览器下面不够精准，但那也只是极少数，如果需要非常精准的适配，那么可以使用js来计算html的font-size。

## 换算

`2rem = 2 * html标签上字体的大小`

## 微码

```js
(function (doc, win, temW) {
  let docEl = doc.documentElement
  let resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize'
  let recalc = function () {
    var clientWidth = docEl.clientWidth
    if (!clientWidth) { return }
    docEl.style.fontSize = 100 * (clientWidth / temW) + 'px'
  }
  if (!doc.addEventListener) return
  win.addEventListener(resizeEvt, recalc, false)
  doc.addEventListener('DOMContentLoaded', recalc, false)
})(document, window, templetWidth)

```

这里`templetWidth`就是你拿到的效果图宽度

根据窗口大小的变化动态改变`documentElement`的字体大小。这里乘以100为了方便计算以及网页最小字体`10px`而添加的，所以后面应用的时候我们在效果图上量到的大小都除以100就是我们想要的rem大小

如： 50px  =>  .5rem







