---
layout:     post
title:      "svg-圆形进度条"
subtitle:   "svg，js归纳笔记"
date:       2017-09-26
author:     "Joubn"
header-img: "img/hello2017.jpg"
header-mask: 0.3
catalog: true
tags:
    - 前端开发
    - svg
---

> “A little bit of progress every day. ”

## 前言

这篇文章主要讲一下使用`<svg>`里的`<path>`来画一个圆弧，并封装成我们想要方法供日后使用。

申明:*这里只用到`<path>`的A命令，并没有涉及到贝塞尔曲线相关指令，后面文章会着重去解剖一下贝塞尔曲线相关指令！*

## path 指令概要

SVG中path的元素，也就是路径绘制，属性名称是d, 具体值是由专门的“指令字母+坐标值”实现的，例如下面这个简单代码示意：

```html
<path d="M10 10L90 90" stroke="#000000" style="stroke-width: 5px;"></path>
```
<table cellspacing="1" cellpadding="0" class="params_table">
    <thead>
        <tr>
            <th>命令</th>
            <th>名称</th>
            <th>参数</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>M</td>
            <td>moveto  移动到</td>
            <td>(x y)+</td>
        </tr>
        <tr>
            <td>Z</td>
            <td>closepath  关闭路径</td>
            <td>(none)</td>
        </tr>
        <tr>
            <td>L</td>
            <td>lineto  画线到</td>
            <td>(x y)+</td>
        </tr>
        <tr>
            <td>H</td>
            <td>horizontal lineto  水平线到</td>
            <td>x+</td>
        </tr>
        <tr>
            <td>V</td>
            <td>vertical lineto  垂直线到</td>
            <td>y+</td>
        </tr>
        <tr>
            <td>C</td>
            <td>curveto  三次贝塞尔曲线到</td>
            <td>(x1 y1 x2 y2 x y)+</td>
        </tr>
        <tr>
            <td>S</td>
            <td>smooth curveto  光滑三次贝塞尔曲线到</td>
            <td>(x2 y2 x y)+</td>
        </tr>
        <tr>
            <td>Q</td>
            <td>quadratic Bézier curveto  二次贝塞尔曲线到</td>
            <td>(x1 y1 x y)+</td>
        </tr>
        <tr>
            <td>T</td>
            <td>smooth quadratic Bézier curveto  光滑二次贝塞尔曲线到</td>
            <td>(x y)+</td>
        </tr>
        <tr>
            <td>A</td>
            <td>elliptical arc  椭圆弧</td>
            <td>(rx ry x-axis-rotation large-arc-flag sweep-flag x y)+</td>
        </tr>
        <tr>
            <td>R</td>
            <td><a href="http://en.wikipedia.org/wiki/Catmull–Rom_spline#Catmull.E2.80.93Rom_spline">Catmull-Rom curveto</a>*  Catmull-Rom曲线</td>
            <td>x1 y1 (x y)+</td>
        </tr>
    </tbody>
</table>

这里我们只讨论**A**指令详细用法，其它**CSQT**贝塞尔曲线相关指令后面再探讨，**MZLHV**基本指令比较好上手就不去废话了

如果指令字母是大写的，例如M, 则表示坐标位置是绝对位置；如果指令字母小写的，例如m, 则表示坐标位置是相对位置。

其中`Catmull-Rom`曲线不是标准的SVG命令，我们不予以讨论!

## A指令参数说明

>A rx ry x-axis-rotation large-arc-flag sweep-flag x y

1. *rx*               x轴半径
2. *ry*               y轴半径
3. *x-axis-rotation*  弧形的旋转情况
4. *large-arc-flag*   角度大小
5. *sweep-flag*       弧线方向
6. *x*                终点x轴坐标
7. *y*                终点y轴坐标

## 画静态圆孤

<div style="text-align:center;margin-bottom:20px;">
<svg style="width:250px; margin:0 auto;" viewBox="0,0,250,250"><circle cx="125" cy="125" r="122" stroke="#d9d9d9" fill="none" stroke-width="3"></circle><path class="path" stroke="#ff5256" fill="none" stroke-width="3" stroke-linecap="round" d="M 3 125 A 122,122 0 0 1 247,125"></path><text x="86" y="130" fill="#ccc" font-size="30">Joubn</text></svg>
</div>

```html
<svg viewBox="0,0,250,250">
    <circle cx="125" cy="125" r="122" stroke="#d9d9d9" fill="none" stroke-width="3"></circle>
    <path class="path" id="pathss" stroke="#ff5256" fill="none" stroke-width="3" stroke-linecap="round" d="M 3 125 A 122,122 0 0 1 247,125"></path>
    <text x="86" y="130" fill="#ccc" font-size="30">Joubn</text>
</svg>
```

## 封装方法

```js
function setRate(rate){
    var angle=rate/100*360
    var l,x,y;
    if(angle==360){angle=359.99}
    angle>180 ? l=1 : l=0;
    x=125+122*Math.cos((180-angle)*(2*Math.PI/360));
    y=125-122*Math.sin((180-angle)*(2*Math.PI/360));
    return 'M 3 125 A 122,122 0 '+l+' 1 '+x+','+y;
}
```
## 补充canvas

```js
function draw(d){
    var canvas = document.getElementById('tutorial');
    if (canvas.getContext){
      var ctx = canvas.getContext('2d');

      ctx.fillStyle = '#fff';
      ctx.fillRect(0,0,canvas.width,canvas.height);

      //画圆形
      ctx.lineCap = "round"
      ctx.strokeStyle ="#d9d9d9"; //设置描边颜色
      ctx.lineWidth = 3 //设置描边宽度
      ctx.beginPath();
      ctx.arc(125,125,122,0,Math.PI*2,true);//画一个圆心坐标（125*125px）半径122px的圆弧，从0开始到Math.PI*2结束，按照顺时针方向。
      ctx.stroke();
      ctx.strokeStyle ="#ff5256";
      var path = new Path2D(d);
      ctx.stroke(path);
      ctx.fillStyle = "#ccc";
      ctx.font = "30px -apple-system";
      ctx.fillText("canvas", 80, 130);//实心文字

    }
}
```

## 最终效果
<div style="text-align:center;margin-bottom:20px;">
<svg style="width:250px; margin:0 auto;" viewBox="0,0,250,250"><circle cx="125" cy="125" r="122" stroke="#d9d9d9" fill="none" stroke-width="3"></circle><path class="path" id="pathss" stroke="#ff5256" fill="none" stroke-width="3" stroke-linecap="round" d="M 3 125 A 122,122 0 0 1 247,125"></path><text x="103" y="130" fill="#ccc" font-size="30" font-family="arial">svg</text></svg>
</div>

<div style="text-align:center;margin-bottom:20px;">
    <input type="range" style="margin:0 auto;" onchange="demo(this)" />
</div>

<div style="text-align:center;margin-bottom:20px;">
    <canvas id="tutorial" width="250" height="250" style="width:250px; margin:0 auto;"></canvas>
</div>

<script type="text/javascript">
function draw(d){
    var canvas = document.getElementById('tutorial');
    if (canvas.getContext){
      var ctx = canvas.getContext('2d');

      ctx.fillStyle = '#fff';
      ctx.fillRect(0,0,canvas.width,canvas.height);

      //画圆形
      ctx.lineCap = "round"
      ctx.strokeStyle ="#d9d9d9"; //设置描边颜色
      ctx.lineWidth = 3 //设置描边宽度
      ctx.beginPath();
      ctx.arc(125,125,122,0,Math.PI*2,true);//画一个圆心坐标（125*125）半径122px的圆弧，从0开始到Math.PI*2结束，按照顺时针方向。
      ctx.stroke();
      ctx.strokeStyle ="#ff5256";
      var path = new Path2D(d);
      ctx.stroke(path);
      ctx.fillStyle = "#ccc";
      ctx.font = "30px arial";
      ctx.fillText("canvas", 80, 130);//实心文字

    }
  }

function setRate(rate){
    var angle=rate/100*360
    var l,x,y;
    if(angle==360){angle=359.99}
    angle>180 ? l=1 : l=0;
    x=125+122*Math.cos((180-angle)*(2*Math.PI/360));
    y=125-122*Math.sin((180-angle)*(2*Math.PI/360));
    return 'M 3 125 A 122,122 0 '+l+' 1 '+x+','+y;
}
function demo(obj){
    var val=obj.value
    document.querySelector('#pathss').setAttribute('d',setRate(val))
    draw(setRate(val))
}

window.onload = draw('M 3 125 A 122,122 0 0 1 247,125');

</script>
<style type="text/css">
.path {
  stroke-dasharray: 1000;
  stroke-dashoffset: 1000;
  -webkit-animation: dash 3s ease forwards;
  animation: dash 3s ease forwards;
}

@keyframes dash {
  to {
    stroke-dashoffset: 0;
  }
}
@-webkit-keyframes dash
{
  to {
    stroke-dashoffset: 0;
  }
}
</style>