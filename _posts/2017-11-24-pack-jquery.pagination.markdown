---
layout:     post
title:      "拓展jQuery Pagination分页功能"
subtitle:   "实用组件"
date:       2017-11-24
author:     "Joubn"
header-img: "img/hello2017.jpg"
header-mask: 0.3
catalog: true
tags:
    - 前端开发
    - jquery
    - 插件
---

> “Happiness is a direction, not a place. ”

## 介绍说明

原[jquery.pagination.js](https://github.com/gbirke/jquery_pagination)已经很优秀，感谢[MikeGrace](https://github.com/MikeGrace)给我们带来这么好用的轮子！

我的拓展：
- 原插件CSS不太合理，使用浮动，故无法方便实现左右方向的定位，且未清除浮动，对其DOM进行优化，使其支持text-align的定位。
- 添加输入页码跳转功能，新增API`isJump`、`jump_format_text`、`jump_outofrange_text`、`jump_null_text`。
- 对原js文件注释进行了中文翻译，默认文字也是汉化了的

## 参数

参数名 | 描述 | 参数值 
--- | :------: | :-----: 
maxentries | 总条目数 | 必选参数，整数 
items_per_page | 每页显示的条目数 | 可选参数，默认是10
num_display_entries | 连续分页主体部分显示的分页条目数 | 可选参数，默认是10
current_page | 当前选中的页面 | 可选参数，默认是0，表示第1页
num_edge_entries | 两侧显示的首尾分页的条目数 | 可选参数，默认是0
link_to | 分页的链接 | 字符串，可选参数，默认是"#"
prev_text | “前一页”分页按钮上显示的文字 | 字符串参数，可选，默认是"Prev"
next_text | “下一页”分页按钮上显示的文字 | 字符串参数，可选，默认是"Next"
ellipse_text | 省略的页数用什么文字表示 | 可选字符串参数，默认是"…"
prev_show_always | 是否显示“前一页”分页按钮 | 布尔型，可选参数，默认为true，即显示“前一页”按钮
next_show_always | 是否显示“下一页”分页按钮 | 布尔型，可选参数，默认为true，即显示“下一页”按钮
callback | 回调函数 | 默认无执行效果
load_first_page | 初始是否回调 | 布尔型，可选参数，默认为false
isJump | 显示输入页码跳转 | 布尔型，可选参数，默认为true

## 使用

调用方法：

``` js
$("#Pagination").pagination(100, {
    num_edge_entries: 2,
    num_display_entries: 4,
    callback: pageselectCallback,
    items_per_page:1
});
```

这段代码表示的含义是：总共有100(maxentries)个列表项，首尾两侧分页显示2(num_edge_entries)个，连续分页主体数目显示4(num_display_entries)个，回调函数为pageselectCallback(callback)，每页显示的列表项为1(items_per_page)。您可以对照参数表修改配置这里的参数。

## demo及下载
<div id="Pagination-con"></div>
<div id="Pagination" class="pagination"></div>
<style>
.pagination{text-align: center;padding: 15px;height: 32px;overflow: hidden;vertical-align: top;position: relative;z-index:100;}
.pagination .con{height: 32px;margin: 0 auto;display: inline-block;*zoom: 1;*display: inline;padding: 0;}
.pagination span.page-num.prev, .pagination span.page-num.next{padding: 0 14px;margin: 0 10px;border-radius: 10px;font-size: 14px;}
.pagination span{font-size: 14px;float: left;display: inline-block;*zoom: 1;*display: inline;height: 30px;line-height: 30px;}
.pagination span.page-num{font-size: 14px;padding: 0 10px;border-radius: 10px;border: 1px solid #cccfd2;margin: 0 3px;cursor: pointer;background: #fff;}
.pagination span.page-num.on{ background: #ffb80d\9; background-image: linear-gradient( 90deg, #ffb80d, #ff960d);color: #fff;border: none;height: 32px;line-height: 32px;}
.pagination span.next, .pagination span.prev{border: 1px solid #cccfd2;color: #666666;padding: 0 20px;margin: 0 10px;border-radius: 5px;}
.pagination span.total-info{font-size: 14px;margin-left: 12px;margin-right: 12px;}
.pagination #jump-index{border: 1px solid #bfbfbf;height: 30px;line-height: 30px;border-radius: 5px;margin: 0 10px;width: 40px;}
.pagination .jump-page{border: 1px solid #cccfd2;background: #fff;border-radius: 10px;color: #666666;height: 30px;line-height: 30px;width: 64px;cursor: pointer;margin-left: 26px;}
.pagination-small span{height: 32px;line-height: 32px;}
.pagination-small span.page-num{padding: 0 9px;}
.pagination-small #jump-index{height: 32px;line-height: 32px;}
.pagination-small .jump-page{height: 32px;line-height: 32px;}
</style>

<script src="{{ '/js/plugin/jquery.pagination.js' | prepend: site.baseurl }}"></script>

<script>
    $("#Pagination").pagination(100, {
        num_edge_entries: 2,
        num_display_entries: 4,
        callback: function(i){
            $('#Pagination-con').html(i)  
        },
        items_per_page:1,
        load_first_page:true,
    });
</script>





