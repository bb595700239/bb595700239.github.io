---
layout:     post
title:      "background-clip 实现图片文字效果"
subtitle:   "css3，css归纳笔记"
date:       2017-10-26
author:     "Joubn"
header-img: "img/hello2017.jpg"
header-mask: 0.3
catalog: true
tags:
    - css3
    - 特效
    - 自定义字体
---

> “The shortest distance between two people is a smile. ”

## 前言

UI：前端哥哥，帮我把这个图切了

![](/img/in-post/background-clip/text.png)

我：好嘞，小case 等我10秒钟
    
UI：对了，效果要跟我一样，而且文字是不固定的

我：呃（mmp）...

**好吧，有需求了我就要义不容辞的完成。毕竟我是有追求有理想的前端。**

## 正文

根据效果图可以明确两个需求 *自定义字体*、*文字蒙版*

既然目标明确，那就开干。

### 自定义字体

效果图上的字体是**迷你简菱心** ，那先去下载好这个字体（百度一下你就知道）

下载好的字体一般是`.ttf`格式文件，但是并不是所有的浏览器都支持这种字体。这时就要转换字体，地址：[font2web](http://www.font2web.com/)。你可能会发现很多转换字体的地址但很少有能转换的，而且有些能转换又提示文件过大。 

这个时候我们要做的操作就是删文字，没错就是删文字。用`FontCreator`打开字体文件
![](/img/in-post/background-clip/font.jpg)
大概就是这样，然后删除你觉得用不到的文字吧。删除好再转换就OK啦！

现在就开始引入文字了css里加上以下代码
```css
@font-face {  
 font-family: 'MyCustomFont';  
 src: url('fontFileName.eot');
  src: url('fontFileName.eot?#iefix') format('embedded-opentype'),
       url('fontFileName.woff') format('woff'),
       url('fontFileName.svg') format('svg'),
   url('fontFileName.ttf') format('truetype');
  font-weight: normal;
  font-style: normal;
} 
```

页面应用字体
```css
body{
    font-family: 'MyCustomFont';
}
```

到此需求一完成



### 文字蒙版

到这里才算切入主题了。（急死我啦，我也不想跑题...）

要做的文字蒙版效果就是用的`background-clip`属性[详细了解](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-clip)，应该更准确的说是`background-clip:text`属性值。
下面给例子感受一下`background-clip`属性的各个值(content-box | padding-box | border-box)的作用：
<div class="demobox">
<div id="demo" style="-webkit-background-clip: content-box;">美女</div>
<div id="dctrl2"> 
<input id="cb" type="radio" name="dctrl2" value="content-box" checked="checked"> 
<label for="cb">content-box</label> <br>
	<input id="pb" type="radio" name="dctrl2" value="padding-box">
	<label for="pb">padding-box</label>
	<br>
	<input id="bb" type="radio" name="dctrl2" value="border-box">
	<label for="bb">border-box</label>
<br>
	<input id="t" type="radio" name="dctrl2" value="text">
	<label for="t">text</label>
</div>
<script type="text/javascript">
    $('input[name="dctrl2"]').change(function(){
        $('#demo').css('-webkit-background-clip', $(this).val());
    });
</script>
<style>
@font-face {  
 font-family: 'MyCustomFont';  
 src: url('/img/in-post/background-clip/lingxin.eot');
  src: url('/img/in-post/background-clip/lingxin.eot?#iefix') format('embedded-opentype'),
       url('/img/in-post/background-clip/lingxin.woff') format('woff'),
       url('/img/in-post/background-clip/lingxin.svg') format('svg'),
   url('/img/in-post/background-clip/lingxin.ttf') format('truetype');
  font-weight: normal;
  font-style: normal;
}
.demobox{
    width: 303px;
    margin: 0 auto;
}
#demo{
    border: 20px dashed #ff5256;
    padding: 40px;
    display: inline-block;
    width: 303px;
    height: 485px;
    background: url(/img/meinv.jpg);
    font-size: 180px;
    line-height: 180px;
    font-weight: 900;
    color: transparent;
    font-family: 'MyCustomFont';
    margin-bottom:20px;
}

.list-name{
    position: relative;
    cursor: pointer;
    width: 322px;
    height: 61px;
    margin:0 auto;
    background: url(/img/in-post/background-clip/goldlala.png) no-repeat;
    background-size: 100% 100%;
    -webkit-background-clip: text;
    color: transparent;
    font-family: 'MyCustomFont';
    font-size: 35px;
    text-align: center;
    line-height: 72px;
}
.list-name img{
    width: 322px;
    height: 61px;
    position: absolute;
    left: 0;
    display: inline-block;
    z-index: -1;
    margin: 0;
}


@-webkit-keyframes loop{
     0%{background-position: -800px 0;}
     100%{background-position: -400px 0;}
}
.text2{
     width: 300px;
     margin: 0 auto;
     height: 78px;
     line-height: 78px;
     font-size: 40px;
     font-family: 'MyCustomFont';
     font-weight: bold;
     -webkit-background-clip: text;
     -webkit-text-fill-color: transparent;
     background-color: red;
     background-image:url(/img/in-post/background-clip/bg.jpg);
     background-repeat:no-repeat;
     background-position: -800px 0;
     -webkit-animation:loop 10s linear infinite;
}
  
</style>
</div>

这里的`text`值就是我们想要的效果。

这里要注意的事设置`background-clip:text`的同时要把字体颜色设置为透明`color: transparent`这样底下的图片才能显示出来。（跟PS图层蒙板很像，蒙板越黑显示的图片越清楚。因为PS里黑色就是代表没有颜色）

### 最终效果
<div class="list-name">
    <img src="/img/in-post/background-clip/listbg.png">
    点我试试
</div>
<script>
$('.list-name').click(function(){
    $(this).html('<img src="/img/in-post/background-clip/listbg.png">试试就试试')
})
</script>

### 拓展

现在我们可以轻松做一个歌词效果

<div class="text2">天空灰的像哭过</div>




