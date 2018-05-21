---
layout:     post
title:      "快速实现视差滚动效果"
subtitle:   "视差滚动"
date:       2018-05-21
author:     "Joubn"
header-img: "img/contact-bg.jpg"
header-mask: 0.3
catalog: true
tags:
    - 特效
---

> “Every noble work is at first impossible. ”

# CSS和JS各自实现视差滚动效果

视差效果在各类网站（官网，BANNER,展示页等）上都有很多的应用，其实这种效果并局限于滚动。
包括像鼠标移动手指移动都可以应用视差效果。这样会使你的应用直接提升一个逼格。闲话不多说，直接上DEMO.

## CSS视差滚动DEMO

<div class="scrollbox">
     <style type="text/css">
     	.scrollbox{perspective: 1px; padding: 0; height: 500px;    overflow: auto; overflow-x: hidden;}
     	.scrollbox .box{width: 100%;height: 1280px;transform-style: preserve-3d;position: relative;}
     	.scrollbox .bg{position: absolute;transform: translate3D(0, 0, -1px) scale(2); width: 100%; height: 100%;object-fit: cover;  }
     	.scrollbox img.body{    position: absolute; left: 50%; top: 50%;    transform: translate(-50%,-50%); }
     </style>
    <div class="box">
        <img src="/img/meinv.jpg" class="body" alt="">
        <img src="/img/home-bg.jpg" alt="" class="bg">
    </div>
</div>

像这种效果的插件也是很多的，但是我们自己几行代码就能搞定的当然最好自己搞定了。如果对兼容性要求不高，就直接是CSS的方案是最好不过了。毕竟能用CSS实现的最好不要用JS

当然你也可以改变背景的距离 改成多个单位
<input type="range" max="10" value="0" style="margin:0 auto;" onchange="demo(this)" />
<script type="text/javascript">
	function demo(obj){
		var val = Number(obj.value)+1
		var bgObj = document.querySelector('.scrollbox .bg')
		bgObj.style.transform = 'translate3D(0, 0, -'+val+'px) scale(2)'
	}
</script>

代码：
```html
<style>
    .scrollbox{perspective: 1px; padding: 0; height: 500px;    overflow: auto; overflow-x: hidden;}
    .scrollbox .box{width: 100%;height: 1280px;transform-style: preserve-3d;position: relative;}
    .scrollbox .bg{position: absolute;transform: translate3D(0, 0, -1px) scale(2); width: 100%; height: 100%;object-fit: cover;  }
    .scrollbox img.body{    position: absolute; left: 50%; top: 50%;    transform: translate(-50%,-50%); }
</style>
<div class="scrollbox">
    <div class="box">
        <img src="/img/meinv.jpg" class="body" alt="">
        <img src="/img/home-bg.jpg" alt="" class="bg">
    </div>
</div>
```

这里主要用的CSS3 3D效果。先设置元素的被查看位置的视图`perspective`,然后将背景放到视图后1个单位的地方`transform: translate3D(0, 0, -1px)`,此时背景应该是缩小了一倍（就像你看的越远的地方东西越小一个道理），再通过`transform:scale(2)`放大一倍就形成了视差的感觉







