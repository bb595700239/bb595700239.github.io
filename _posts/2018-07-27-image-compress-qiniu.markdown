---
layout:     post
title:      "图片压缩预览并上传至七牛云"
subtitle:   "图片压缩"
date:       2018-07-27
author:     "Joubn"
header-img: "img/contact-bg.jpg"
header-mask: 0.3
catalog: true
tags:
    - js
---

> “The best way to cheer yourself up is to try to cheer somebody else up.”

## 为什么要前端图片压缩

1. 节省流量
2. 减少上传时间，增加用户体验

## 图片压缩实现

先看demo [image-compress-qinniu](http://static.joubn.com/2018-07-27-image-compress-qiniu/index.html)

可以配置图片信息然后上传图片到七牛云一气呵成。是的 看似简单的过程背后是一连串的代码逻辑。

主要实现逻辑就是先用表单控件`<input type="file" multiple/>`获取到文件列表信息，然后利用`FileReader`这个类将拿到的这个文件列表另存为图片URL，再利用`canvas`将图片压缩。

### canvas的drawImage()方法

这是图片*宽高压缩*的关键方法

```js
context.drawImage(img, dx, dy);
context.drawImage(img, dx, dy, dWidth, dHeight);
context.drawImage(img, sx, sy, sWidth, sHeight, dx, dy, dWidth, dHeight);
```
我们主要用的就是`context.drawImage(img, dx, dy, dWidth, dHeight);`

各参数功能：
<dl>
 <dt><code>image</code></dt>
 <dd>绘制到上下文的元素。允许任何的 canvas 图像源(<a href="/zh-CN/docs/Web/API/CanvasImageSource" title="CanvasImageSource&nbsp;是一个辅助类型，描述下面类型的任何一个对象：HTMLImageElement, HTMLVideoElement, HTMLCanvasElement, CanvasRenderingContext2D, 或&nbsp;ImageBitmap."><code>CanvasImageSource</code></a>)，例如：<a href="/zh-CN/docs/Web/API/HTMLImageElement" title="HTMLImageElement&nbsp;接口提供了特别的属性和方法 (在常规的&nbsp;HTMLElement之外,它也能通过继承使用)来操纵 <img> 元素的布局和图像."><code>HTMLImageElement</code></a>，<a href="/zh-CN/docs/Web/API/HTMLVideoElement" title="HTMLVideoElement&nbsp;接口提供了用于操作视频对象的特殊属性和方法。它同时还继承了HTMLMediaElement 和 HTMLElement&nbsp;的属性与方法。"><code>HTMLVideoElement</code></a>，或者&nbsp;<a href="/zh-CN/docs/Web/API/HTMLCanvasElement" title="DOM canvas元素暴露了HTMLCanvasElement接口,该接口提供了用来操作一个canvas元素布局和呈现的属性和方法.HTMLCanvasElement接口继承了element接口的属性和方法."><code>HTMLCanvasElement</code></a>。</dd>
 <dt><code>dx</code></dt>
 <dd>目标画布的左上角在目标canvas上&nbsp;X 轴的位置。</dd>
 <dt><code>dy</code></dt>
 <dd>目标画布的左上角在目标canvas上&nbsp;Y 轴的位置。</dd>
 <dt><code>dWidth</code></dt>
 <dd>在目标画布上绘制图像的宽度。 允许对绘制的图像进行缩放。 如果不说明， 在绘制时图片宽度不会缩放。</dd>
 <dt><code>dHeight</code></dt>
 <dd>在目标画布上绘制图像的高度。&nbsp;允许对绘制的图像进行缩放。 如果不说明， 在绘制时图片高度不会缩放。</dd>
 <dt><code>sx</code></dt>
 <dd>需要绘制到目标上下文中的，源图像的矩形选择框的左上角 X 坐标。</dd>
 <dt><code>sy</code></dt>
 <dd>需要绘制到目标上下文中的，源图像的矩形选择框的左上角 Y 坐标。</dd>
 <dt><code>sWidth</code></dt>
 <dd>需要绘制到目标上下文中的，源图像的矩形选择框的宽度。如果不说明，整个矩形从坐标的sx和sy开始，到图像的右下角结束。</dd>
 <dt><code>sHeight</code></dt>
 <dd>需要绘制到目标上下文中的，源图像的矩形选择框的高度。</dd>
</dl>

mozilla上的配图：
![](http://pbafbo2ye.bkt.clouddn.com/Frp1lrVSll6c2dN7LcrCJlBzq_Kb)

### canvas的toDataURL()方法和toBlob()方法

这两个方法主要是将canvas转换成base64和blob(二进制)图片类型。并进行图片*质量压缩*。
#### canvas.toDataURL() 

```js
canvas.toDataURL(mimeType, qualityArgument)
```
这个方法是将图片转换成base64类型

其中：
mimeType表示canvas导出来的base64图片的类型，默认是png格式，也即是默认值是'image/png'，我们也可以指定为jpg格式'image/jpeg'或者webp等格式。file对象中的file.type就是文件的mimeType类型，在转换时候正好可以直接拿来用（如果有file对象）。
qualityArgument表示导出的图片质量，只要导出为jpg和webp格式的时候此参数才有效果，默认值是0.92，是一个比较合理的图片质量输出参数，通常情况下，我们无需再设定。


#### canvas.toBlob()

```js
canvas.toBlob(callback, mimeType, qualityArgument)
```

和toDataURL()方法相比，toBlob()方法是异步的（所以会看到代码逻辑里用到了大量的`Promise`），因此多了个callback参数，这个callback回调方法默认的第一个参数就是转换好的blob文件信息，本文demo的文件上传就是将canvas图片转换成二进制的blob文件

### FileReader

这是html5里新增的方法，主要是将`type="file"`表单控制获取到的图片信息变成可用的图片地址。使用如下：

```js
let reader = new FileReader();
reader.readAsDataURL(file);
reader.onload = (e) => {
  
};
```
因为这个也是异步的所以也会用到`Promise`

## 模块化

compress.js

```js
/**
 * Created by joubn on 2018/08/01.
 */

const compressImgSource = (img,maxW,maxH,quality) => {
	const canvas = document.createElement('canvas');
	let width = img.width;
	let height = img.height;
	const max_width = maxW || 640;
	const max_height = maxH || 640;
	quality = quality || 1

	if (width > max_width) {
		height *= max_width / width;
		height = Math.round(height);
		width = max_width;
	}
	if(height > max_height){
		width *= max_height / height;
		width = Math.round(width);
		height = max_height;
	}
	canvas.width = width
	canvas.height = height
	const wh = {width, height}
	const ctx = canvas.getContext("2d");
	ctx.drawImage(img, 0, 0, width, height);

	let resBase64, base64, blob;

	return new Promise((resolve, reject) => {
		resBase64 = canvas.toDataURL("image/jpeg",quality);
		base64 = Object.assign({},wh,{res:resBase64})

		canvas.toBlob(data => {
			blob = Object.assign({},wh,{res:data})
			resolve({base64,blob});
		}, "image/jpeg", quality)
	})
}


const compressImg = ([...files],maxW,maxH,quality) => {
	if(typeof FileReader==='undefined'){
		new Error( '不支持图片上传' );
		return false;
	}

	let arr = [],promArr = [];

		files.forEach(file => {
			if (file.type.indexOf("image") == 0) {
				let reader = new FileReader();
				reader.readAsDataURL(file);
				promArr.push(
					new Promise((resolve, reject) => {
						reader.onload = (e) => {
							let image = new Image();
							image.src = e.target.result;
							image.onload = () => {
								compressImgSource(image, maxW, maxH, quality).then(res=>{
									resolve(res);
								})
							}
						};
					})
				)
			}
		})

	return promArr
}

export {compressImg, compressImgSource}


```


使用方法：
 
```js
import { compressImg } from  './compress'

Promise.all(compressImg(fileList)).then(imgs => {
  // imgs 为所有图片信息 包括了base64和blob 
})

```

## 上传至七牛云

这一步请看这篇文章[elementUI+七牛实现js图片服务器](http://joubn.com/2018/07/03/qiniu-elementUI/)

使用方法也是一样：

```js
import {token,action,domain} from "./qiniuToken";
```

这就是模块化的好处哇


## 后言

📍 划重点：[看这里](https://github.com/bb595700239/image-compress-qiniu)
















