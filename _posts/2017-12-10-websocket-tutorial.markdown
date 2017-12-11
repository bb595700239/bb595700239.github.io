---
layout:     post
title:      "webSocket+nodejs搭建聊天室"
subtitle:   "nodejs实践"
date:       2017-12-10
author:     "Joubn"
header-img: "img/hello2017.jpg"
header-mask: 0.3
catalog: true
tags:
    - 前端开发
    - websocket
    - nodejs
---

> “Eternity is said not to be an extension of time but an absence of time. ”

## 简介

闲来无事用nodejs及websocket搭建一个简易聊天室玩玩，后面有时间丰富ui

##  简易聊天室

这里`templetWidth`就是你拿到的效果图宽度


<form action="javascript:return false">
    <input id="sendtxt" type="text"/>
    <button type="submit" id="send">发送</button>
</form>

<div id="info"></div>
<script>
    var websocket = new WebSocket('ws://api.joubn.com/')
    //var websocket = new WebSocket('ws://localhost:8010/')
    function showMessage(str,type){
        var div = document.createElement('div');
        div.innerHTML = str;
        if(type == 'enter'){
            div.style.color ='blue'
        }else if(type== 'leave'){
            div.style.color = 'red'
        }

        document.getElementById('info').appendChild(div);
    }
    websocket.onopen = function(){
        document.getElementById('send').onclick = function () {
            var txt = document.getElementById('sendtxt').value
            if(txt){
                websocket.send(txt)
            }
        }
        document.getElementById('info').innerHTML =  "connected"
    }
    websocket.onclose = function(){
        console.log('socket close')
    }
    websocket.onmessage = function(e){
        console.log(e.data)
        var mes = JSON.parse(e.data)
        showMessage(mes.data,mes.type)
    }

</script>






