---
layout:     post
title:      "VUE-组件递归调用"
subtitle:   "VUE，es6归纳笔记"
date:       2017-09-25
author:     "Joubn"
header-img: "img/hello2017.jpg"
header-mask: 0.3
catalog: true
tags:
    - 前端开发
    - VUE
    - es6
---

> “A little bit of progress every day. ”

## 前言

前段时间做一个VUE后台管理系统，发现很多地方都有使用到组件的递归调用。

一开始也是各种踩坑，今天抽时间整理一下放出来大家分享。

## 组件递归

**组件在它的模板内可以递归地调用自己，只有当它有 name 选项时才可以。**

--这是定义递归组件的一个关键点

#### 效果图

![](/img/in-post/vue-component-recursion/end.png)
<small class="img-hint">最终效果图</small>

#### mock数据

```js
data () {
  return {
    list:[
      {
        name: '协会1',
        children: [
          {
            "open_id": "1",
            "name": "周标"
          }
        ]
      },
      {
        name: '协会2',
        children: [
          {
            "name": "会长",
            children: [
              {
                "open_id": "1",
                "name": "周标"
              }
            ]
          }
        ]
      },
      {
        name: '协会3',
        children: [
          {
            "name": "会长",
            children: [
              {
                "open_id": "1",
                "name": "周标"
              }
            ]
          }
        ]
      },
      {
        name: '协会4',
        children: [
          {
            "name": "会长",
            children: [
              {
                "open_id": "1",
                "name": "周标"
              }
            ]
          }
        ]
      },
      {
        name: '协会5',
        children: [
          {
            "name": "会长",
            children: [
              {
                "open_id": "1",
                "name": "周标"
              }
            ]
          }
        ]
      }
    ]
  }
}
```

#### 封装成组件

```html
<template>
  <li class="item">
    <div @click='toggle' v-if="!model.open_id">
      <div class="tit flex-box"><i class="iconfont icon-unfold" :class="[open?'open':'']" v-if='isFolder'></i><i class="iconfont icon-move" v-else></i><div class="name" :class="[!isFolder?'folder':'']" >{{model.name}}</div><!--<div class="num">{{model.data.currentNum}}/{{model.data.totalNum}}</div>--></div>
    </div>
    <ul v-else>
      <router-link tag="li" to="/allxuehui" class="flex-box users">
        <div v-if="model.photo" class="pic flex-box"><img :src="model.photo" alt=""></div>
        <div  class="pic text" v-else>{{model.name | filename}}</div>
        <div class="info"><p class="p1">{{model.name}}</p><p class="p2">{{model.open_id}}</p></div>
      </router-link>
    </ul>
    <ul v-show="open" v-if='isFolder'>
      <items v-for='(cel,index) in model.children' :model='cel' :key="index">
      </items>
    </ul>
  </li>
</template>

<script>
  export default {
    name: 'items',
    props: ['model'],
    data () {
      return {
        open: false,
      }
    },
    created () {

    },
    computed: {
      isFolder() {
        return this.model.children && this.model.children.length
      }
    },

    methods:{
      toggle: function() {
        if(this.isFolder) {
          this.open = !this.open
        }
      }
    },
    components: {
    },
    filters:{
      filename(val){
        return val.substr(val.length-2)
      }
    }
  }
</script>
```

#### 组件的调用

```html
<ul class="items">
    <item :model='model' v-for='(model,index) in list' :key="index"></item>
</ul>
```



