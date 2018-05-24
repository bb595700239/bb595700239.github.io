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

## CSS和JS各自实现视差滚动效果

视差效果在各类网站（官网，BANNER,展示页等）上都有很多的应用，其实这种效果并局限于滚动。
包括像鼠标移动手指移动都可以应用视差效果。这样会使你的应用直接提升一个逼格。闲话不多说，直接上DEMO.

### CSS视差滚动DEMO

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
<input style="width: 200px;display: inline-block;margin-left: 20px;vertical-align: middle;" type="range" max="10" value="0" onchange="demo(this)" />
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

这里主要用的CSS3 3D效果。先设置元素的被查看位置的视图`perspective`,然后将背景放到视图后1个单位的地方`transform: translate3D(0, 0, -1px)`,此时背景应该是缩小了一倍（就像你看的越远的地方东西越小一个道理），再通过`transform:scale(2)`将背景放大一倍就形成了视差的感觉

看这里看这里：
![](http://static.joubn.com/2018-05-21-parallax-effect/css3d-edited.png)

### JS视差滚动DEMO

<div class="scrollbox2" onscroll="demo2(this)">
    <style type="text/css">
        .scrollbox2{padding: 0; height: 500px;    overflow: auto; overflow-x: hidden;}
    	.scrollbox2 .box{width: 100%;height: 1280px;position: relative;background-image: url('/img/home-bg.jpg');}
    	.scrollbox2 img.body{    position: absolute; left: 50%; top: 50%;    transform: translate(-50%,-50%); }
    </style>
    <div class="box">
        <img src="/img/meinv.jpg" class="body" alt="">
    </div>
</div>

<script type="text/javascript">
	function demo2(obj){
		var scrollTop = obj.scrollTop
		var bgObj = document.querySelector('.scrollbox2 .box')
		bgObj.style.backgroundPositionY = scrollTop/1.5 +'px'
	}
</script>


代码：
```html
<div class="scrollbox2" onscroll="demo2(this)">
    <div class="box">
        <img src="/img/meinv.jpg" class="body" alt="">
    </div>
</div>

<script type="text/javascript">
	function demo2(obj){
		var scrollTop = obj.scrollTop
		var bgObj = document.querySelector('.scrollbox2 .box')
		bgObj.style.backgroundPositionY = scrollTop/1.5 +'px'
	}
</script>
```

js实现起来就容易理解了，并不用了解3D视角。因为这个3D视角最终给人的感觉就是一个平面上的元素滚动有的快有的慢嘛。
所以我们根据滚动的距离来差异性的调整背景和美女的移动速度就OK啦！

### 扩展

前面说到其实这种效果可以应用到各多场景和各种事件。
这里我再展示一个之前写的根据鼠标移动的视差效果：

<div class="scrollbox3" onmousemove="demo3(this,event)">
    <style type="text/css">
        .scrollbox3{padding: 0; height: 500px; position: relative;}
        	.scrollbox3 .bubble1{transform: translate(30%,5%) scale(.5);}
        	.scrollbox3 .bubble2{transform: translate(-80%,-80%) scale(.6);}
        	.bubble{width:200px;height:200px;box-shadow:inset -30px -30px 75px rgba(255,30,85,.2),inset 0 0 5px rgba(255,30,85,.5),inset 0 0 55px rgba(255,255,255,.5),inset -3px -3px 5px rgba(0,0,0,.5),0 0 50px rgba(255,255,255,.75);border-radius:100px;background:rgba(255,255,255,.4);position:absolute;left: 50%; top: 50%;    transform: translate(-50%,-50%);}
        	.bubble:before{content:"";display:block;position:absolute;width:50px;height:50px;top:25px;left:25px;border-radius:75px 25px;box-shadow:inset 10px 10px 50px rgba(255,255,255,.6)}
        	.bubble:after{content:"";display:block;position:absolute;width:190px;height:190px;border-radius:190px;left:5px;top:5px;box-shadow:inset 0 -5px 5px rgba(255,30,85,.05)}
    </style>
    <div class="bubble bubble1" style="z-index:3;"></div>
    <div class="bubble bubble2" style="z-index:5;"></div>
    <div class="bubble bubble3" style="z-index:9;"></div>
</div>

<script type="text/javascript">
	function demo3(obj,ev){
        var l = obj.offsetWidth/2
        var t = obj.offsetHeight/2
        var bubble = document.querySelectorAll('.scrollbox3 .bubble')

        var iL = ev.clientX;
        var iT = ev.clientY;
        for(var i=0; i<bubble.length; i++){
            bubble[i].style.marginLeft=(iL - l )/100*bubble[i].style.zIndex+'px';
            bubble[i].style.marginTop=(iT - t )/70*bubble[i].style.zIndex+'px';
        }
    }
</script>

这里代码就不展示了 自己找吧。因为其实JS的实现还是比较简单的。根据鼠标的移动距离来差异性的调整每个泡泡的移动速度。每个泡泡的差异性又是根据它自身样式`z-index`的值去获取就OK了 

好了到这里结束吧。





