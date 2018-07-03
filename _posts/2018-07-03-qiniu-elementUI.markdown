---
layout:     post
title:      "elementUI+七牛实现js图片服务器"
subtitle:   "图片上传"
date:       2018-07-03
author:     "Joubn"
header-img: "img/contact-bg.jpg"
header-mask: 0.3
catalog: true
tags:
    - js
---

> “Too much experience is a dangerous thing.”

## 前言

图片上传是我们前端必须了解的，相信大家也是各有办法。这里介绍一个比较实用快速的上传到七牛云的方法。

因为这个方法主要是在前端把图片上传到七牛所以和之前我们调后台获取`TOKEN`又不一样。这样以后就不用依赖后端，直接自己搞定了。


ps:当然这是有风险的，因为你的`ak/sk`全部暴露了。

## DEMO

<iframe frameborder="0" width="100%" height="264px" src="http://static.joubn.com/2018-07-03-qiniu-elementUI/index.html"></iframe>

你可以点添加图片试试（最多只能添加两张），然后点地址复制到浏览器地址栏就可以看到图片了，此时说明图片已经上传到服务器了！

ps:别玩坏了



## 代码

为了使用方便我已经把它模块化了，需要的地方引入一下就行了

qiniuToken.js:

```js
import CryptoJS from 'crypto-js'

/* 提交地址
 * 华东空间使用 upload.qiniup.com
 * 华北空间使用 upload-z1.qiniu.com
 * 华南空间使用 upload-z2.qiniu.com
 * 北美空间使用 upload-na0.qiniu.com
 */

let action = "https://upload.qiniup.com"

// 空间名

let bucketName = 'bucketdemo';

//	AccessKey

let AK = 'n2phvIqT_49KuUKCxdFEiUFM8-GIYTaTFPzy1QTb';

//  SecretKey

let SK = 'u7GnAvumfWyMo6yXw-mSXLztEn0PPSE4PmBwDRwG';


//空间绑定域名

let domain = 'http://pbafbo2ye.bkt.clouddn.com/'




/* utf.js - UTF-8 <=> UTF-16 convertion
 *
 * Copyright (C) 1999 Masanao Izumo <iz@onicos.co.jp>
 * Version: 1.0
 * LastModified: Dec 25 1999
 * This library is free. You can redistribute it and/or modify it.
 */
/*
 * Interfaces:
 * utf8 = utf16to8(utf16);
 * utf16 = utf8to16(utf8);
 */

function utf16to8(str) {
  let out, i, len, c;
  out = "";
  len = str.length;
  for (i = 0; i < len; i++) {
    c = str.charCodeAt(i);
    if ((c >= 0x0001) && (c <= 0x007F)) {
      out += str.charAt(i);
    } else if (c > 0x07FF) {
      out += String.fromCharCode(0xE0 | ((c >> 12) & 0x0F));
      out += String.fromCharCode(0x80 | ((c >> 6) & 0x3F));
      out += String.fromCharCode(0x80 | ((c >> 0) & 0x3F));
    } else {
      out += String.fromCharCode(0xC0 | ((c >> 6) & 0x1F));
      out += String.fromCharCode(0x80 | ((c >> 0) & 0x3F));
    }
  }
  return out;
}

function utf8to16(str) {
  let out, i, len, c;
  let char2, char3;
  out = "";
  len = str.length;
  i = 0;
  while (i < len) {
    c = str.charCodeAt(i++);
    switch (c >> 4) {
      case 0:
      case 1:
      case 2:
      case 3:
      case 4:
      case 5:
      case 6:
      case 7:
        // 0xxxxxxx
        out += str.charAt(i - 1);
        break;
      case 12:
      case 13:
        // 110x xxxx 10xx xxxx
        char2 = str.charCodeAt(i++);
        out += String.fromCharCode(((c & 0x1F) << 6) | (char2 & 0x3F));
        break;
      case 14:
        // 1110 xxxx 10xx xxxx 10xx xxxx
        char2 = str.charCodeAt(i++);
        char3 = str.charCodeAt(i++);
        out += String.fromCharCode(((c & 0x0F) << 12) | ((char2 & 0x3F) << 6) | ((char3 & 0x3F) << 0));
        break;
    }
  }
  return out;
}

/*
 * Interfaces:
 * b64 = base64encode(data);
 * data = base64decode(b64);
 */
let base64EncodeChars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-_";
let base64DecodeChars = new Array(-1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, 62, -1, -1, -1, 63,
  52, 53, 54, 55, 56, 57, 58, 59, 60, 61, -1, -1, -1, -1, -1, -1, -1, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14,
  15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, -1, -1, -1, -1, -1, -1, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40,
  41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, -1, -1, -1, -1, -1);

function base64encode(str) {
  let out, i, len;
  let c1, c2, c3;
  len = str.length;
  i = 0;
  out = "";
  while (i < len) {
    c1 = str.charCodeAt(i++) & 0xff;
    if (i == len) {
      out += base64EncodeChars.charAt(c1 >> 2);
      out += base64EncodeChars.charAt((c1 & 0x3) << 4);
      out += "==";
      break;
    }
    c2 = str.charCodeAt(i++);
    if (i == len) {
      out += base64EncodeChars.charAt(c1 >> 2);
      out += base64EncodeChars.charAt(((c1 & 0x3) << 4) | ((c2 & 0xF0) >> 4));
      out += base64EncodeChars.charAt((c2 & 0xF) << 2);
      out += "=";
      break;
    }
    c3 = str.charCodeAt(i++);
    out += base64EncodeChars.charAt(c1 >> 2);
    out += base64EncodeChars.charAt(((c1 & 0x3) << 4) | ((c2 & 0xF0) >> 4));
    out += base64EncodeChars.charAt(((c2 & 0xF) << 2) | ((c3 & 0xC0) >> 6));
    out += base64EncodeChars.charAt(c3 & 0x3F);
  }
  return out;
}

function base64decode(str) {
  let c1, c2, c3, c4;
  let i, len, out;
  len = str.length;
  i = 0;
  out = "";
  while (i < len) {
    /* c1 */
    do {
      c1 = base64DecodeChars[str.charCodeAt(i++) & 0xff];
    } while (i < len && c1 == -1);
    if (c1 == -1) break;
    /* c2 */
    do {
      c2 = base64DecodeChars[str.charCodeAt(i++) & 0xff];
    } while (i < len && c2 == -1);
    if (c2 == -1) break;
    out += String.fromCharCode((c1 << 2) | ((c2 & 0x30) >> 4));
    /* c3 */
    do {
      c3 = str.charCodeAt(i++) & 0xff;
      if (c3 == 61) return out;
      c3 = base64DecodeChars[c3];
    } while (i < len && c3 == -1);
    if (c3 == -1) break;
    out += String.fromCharCode(((c2 & 0XF) << 4) | ((c3 & 0x3C) >> 2));
    /* c4 */
    do {
      c4 = str.charCodeAt(i++) & 0xff;
      if (c4 == 61) return out;
      c4 = base64DecodeChars[c4];
    } while (i < len && c4 == -1);
    if (c4 == -1) break;
    out += String.fromCharCode(((c3 & 0x03) << 6) | c4);
  }
  return out;
}
let safe64 = function(base64) {
  base64 = base64.replace(/\+/g, "-");
  base64 = base64.replace(/\//g, "_");
  return base64;
};

let genUpToken = function(accessKey, secretKey, putPolicy) {
  //SETP 2
  let put_policy = JSON.stringify(putPolicy);
  //SETP 3
  let encoded = base64encode(utf16to8(put_policy));
  //SETP 4
  let hash = CryptoJS.HmacSHA1(encoded, secretKey);
  let encoded_signed = hash.toString(CryptoJS.enc.Base64);
  //SETP 5
  let upload_token = accessKey + ":" + safe64(encoded_signed) + ":" + encoded;
  return upload_token;
};


let token = () => {
  let policy = {};
  let deadline = Math.round(new Date().getTime() / 1000) + 3600;
  policy.scope = bucketName;
  policy.deadline = deadline;
  return genUpToken(AK, SK, policy);
}



export {
  token, action, domain
}

```


upload.vue

```vue
<template>
  <div>
    <div class="title">图片上传：</div>
    <el-form>
      <el-upload
        list-type="picture-card"
        :file-list="fileList"
        :limit="2"
        accept="image/jpeg,image/png"
        :on-preview="handlePictureCardPreview"
        :on-exceed="handleExceed"
        :on-change="handleChange"
        :on-remove="handleRemove"
        :action="action"
        :data="postData">
        <i class="el-icon-plus"></i>
      </el-upload>
    </el-form>
    <ul class="info">
      <li v-for="(item,index) in fileList" v-if="item.response" v-clipboard:copy="domain+item.response.hash" v-clipboard:success="onCopy" v-clipboard:error="onError">{{domain}}{{item.response.hash}}</li>
    </ul>
    <el-dialog
      :visible.sync="bigImgDialog"
      width="40%"
      center>
      <div class="bigproimg">
        <img :src="url" alt="" class="bigImg">
      </div>
    </el-dialog>
  </div>
</template>

<script>
  import {token,action,domain} from "../plugins/qiniuToken";
  export default {
    data() {
      return {
        input:'',
        maxlength:10,
        fileList:[],
        postData:{
          token: token()
        },
        action: action,
        bigImgDialog: false,
        url: '',
        domain: domain
      };
    },
    mounted(){
    },
    methods: {
      handleChange(file, fileList){
        this.fileList = fileList

      },
      handleRemove(file, fileList){
        this.fileList = fileList
      },
      handlePictureCardPreview(file) {
        this.bigImg(file.url)
      },
      handleExceed(files, fileList) {
        this.$message.warning(`当前限制选择 1 个文件，本次选择了 ${files.length} 个文件，共选择了 ${files.length + fileList.length} 个文件`);
      },
      bigImg(url) {
        this.url = url;
        this.bigImgDialog = true;
      },
      onCopy: function (e) {
        this.$message({
          message: '复制成功',
          type: 'success'
        });
      },
      onError: function (e) {
        this.$message.error('复制失败');
      }
    }
  }

</script>

<style lang="scss">
  .title{
    margin-bottom: 10px;
  }
  .el-upload-list__item{
    img{
      object-fit: contain;
    }
  }
  .info{
    li{
      margin-bottom: 10px;
    }
  }
</style>

```

原码地址：[element-UI-qiniu](https://github.com/bb595700239/element-UI-qiniu.git)


### 七牛上传图片步骤

1.利用`AccessKey`、`SecretKey`、`bucketName`(存储空间名) 通过一系列加密算法得到token.

2.把得到的token和文件一并提交到七牛（这里的提交地址是七牛提供的，每个地区的空间都不一样 ,这里的token提交的时候属性名就叫token）地址如下：
    <div style="color:red;margin-left:40px">* 华东空间使用 upload.qiniup.com <br/>* 华北空间使用 upload-z1.qiniu.com  <br/>* 华南空间使用 upload-z2.qiniu.com  <br/>* 北美空间使用 upload-na0.qiniu.com</div>

3.获取提交返回的地址（可以再提交给后端用）







