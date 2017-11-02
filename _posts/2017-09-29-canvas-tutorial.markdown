---
layout:     post
title:      "canvas-基本用法"
subtitle:   "canvas，js归纳笔记"
date:       2017-09-29
author:     "Joubn"
header-img: "img/hello2017.jpg"
header-mask: 0.3
catalog: true
tags:
    - 前端开发
    - canvas
---

> “A little bit of progress every day. ”

## 前言

刚好这几天公司要画一些图表，个人还没有怎么去认真用canvas画过什么东西，这里就把基本图形画法统计了下。


```js
 function draw(){
    var canvas = document.getElementById('tutorial');
    if (canvas.getContext){
      var ctx = canvas.getContext('2d');

      ctx.fillStyle = "#ff5256"; //设置填充颜色

      ctx.strokeStyle ="#999"; //设置描边颜色 

      ctx.lineWidth = 4 //设置描边宽度

      ctx.lineCap = "round" //设置描边末端为圆滑

      ctx.lineJoin = "round" //设置连部分末端为圆滑

      

      //画正方形
      ctx.fillRect(25,25,100,100);//绘制一个边长为100px的黑色正方形（距离上25px 左25px）
      ctx.clearRect(45,45,60,60); //擦除了一个60*60px的正方形
      ctx.strokeRect(50,50,50,50);//画一个50*50px的黑色边框正方形

      //添加线性渐变
      var lingrad = ctx.createLinearGradient(0,0,0,125); //添加一个0，0到0，125高度的线性渐变
      lingrad.addColorStop(0 , '#ff0000');
      lingrad.addColorStop(1 , '#0000ff');
      ctx.fillStyle = lingrad;

      //画三角形
      ctx.beginPath();
      ctx.moveTo(160,125);
      ctx.lineTo(220,25);
      ctx.lineTo(280,125);
      ctx.fill();

      //画圆形
      ctx.beginPath();
      ctx.arc(375,75,50,0,Math.PI*2,true);//画一个圆心坐标（375*75px）半径50px的圆弧，从0开始到Math.PI*2结束，按照顺时针方向。
      ctx.stroke();

      //二次贝赛尔曲线
      ctx.beginPath();
      ctx.moveTo(460,125);
      ctx.quadraticCurveTo(520,0,580,125);//控制点坐标（520*0px) 结束点坐标（580*125px)的二次贝赛尔曲线
      ctx.stroke();

      //三次贝赛尔曲线
      ctx.beginPath();
      ctx.moveTo(610,125);
      ctx.bezierCurveTo(620,70,720,70,730,125);//控制点1坐标（620*70px) 控制点2坐标（720*70px) 结束点坐标（730*125px)的三次贝赛尔曲线
      ctx.stroke();


      // 添加径向渐变
      var radgrad = ctx.createRadialGradient(827,73,10,827,73,60);//起始圆心坐标（827*73px）半径10px    结束圆心坐标（827*73px）半径60px 的径向渐变
      radgrad.addColorStop(0, '#ff0000');
      radgrad.addColorStop(.5, '#0000ff');
      radgrad.addColorStop(1, '#00ff00');
      ctx.fillStyle = radgrad;

      //导入SVG里path
      var path = new Path2D("M 760 75 A 60 60 0 1 1 821 136");
      ctx.fill(path);

      //写入文字
      ctx.fillStyle = "#ff5256"; //设置填充颜色
      ctx.strokeStyle ="#ff5256"; //设置描边颜色 
      ctx.lineWidth = 2 //设置描边宽度
      ctx.font = "48px -apple-system";
      ctx.fillText("Jou", 925, 110);//实心文字
      ctx.strokeText("bn", 1008, 110);//空心文字





    var raf;
    var running = false;

    var ball = {
      rectx: 0,
      rectlength:100,
      x: 100,
      y: 300,
      vx: 12,
      vy: 12,
      radius: 5,
      color: 'blue',
      draw: function() {
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2, true);
        ctx.closePath();
        ctx.fillStyle = this.color;
        ctx.fill();
        ctx.fillStyle = 'red'
        ctx.fillRect(this.rectx,canvas.height-4,this.rectlength,4);
      }
    };

    function clear() {
      ctx.fillStyle = 'rgba(255,255,255,1)';
      ctx.fillRect(0,130,canvas.width,canvas.height-130);
    }

    function drawball() {
      clear()
      ball.draw();
      ball.x += ball.vx;
      ball.y += ball.vy;
      console.log(ball.x,ball.y,ball.rectx)
      if((ball.y) >= canvas.height){
        console.log(ball.x,ball.y,ball.rectx)
      }
      if(((ball.x>=ball.rectx+ball.rectlength)||(ball.x<=ball.rectx)) &&((ball.y) >= canvas.height)){

        alert('game over')
      }

      if (ball.y > canvas.height || ball.y < 150) {
        ball.vy = -ball.vy;
      }
      if (ball.x > canvas.width || ball.x  < 0) {
        ball.vx = -ball.vx;
      }
      
      raf = window.requestAnimationFrame(drawball);
    }

    canvas.addEventListener('mousemove', function(e){

      ball.rectx = e.clientX-canvas.offsetLeft-ball.rectlength/2;
      ball.rectx=ball.rectx/706*1200
    });

    canvas.addEventListener('mouseenter',function(e){
      if (!running) {
        raf = window.requestAnimationFrame(drawball);
        running = true;
      }
    });
    canvas.addEventListener('mouseout', function(e){
      window.cancelAnimationFrame(raf);
      running = false;
    });
    ball.draw();
    }
  }
```
<canvas id="tutorial" width="1200" height="700" style="width: 706px;border: 1px solid #ccc;"></canvas>
<script type="text/javascript">
    window.onload = draw();
    function draw(){
        var canvas = document.getElementById('tutorial');
        if (canvas.getContext){
          var ctx = canvas.getContext('2d');

          ctx.fillStyle = "#ff5256"; //设置填充颜色

          ctx.strokeStyle ="#999"; //设置描边颜色 

          ctx.lineWidth = 4 //设置描边宽度

          ctx.lineCap = "round" //设置描边末端为圆滑

          ctx.lineJoin = "round" //设置连部分末端为圆滑

          

          //画正方形
          ctx.fillRect(25,25,100,100);//绘制一个边长为100px的黑色正方形（距离上25px 左25px）
          ctx.clearRect(45,45,60,60); //擦除了一个60*60px的正方形
          ctx.strokeRect(50,50,50,50);//画一个50*50px的黑色边框正方形

          //添加线性渐变
          var lingrad = ctx.createLinearGradient(0,0,0,125); //添加一个0，0到0，125高度的线性渐变
          lingrad.addColorStop(0 , '#ff0000');
          lingrad.addColorStop(1 , '#0000ff');
          ctx.fillStyle = lingrad;

          //画三角形
          ctx.beginPath();
          ctx.moveTo(160,125);
          ctx.lineTo(220,25);
          ctx.lineTo(280,125);
          ctx.fill();

          //画圆形
          ctx.beginPath();
          ctx.arc(375,75,50,0,Math.PI*2,true);//画一个圆心坐标（375*75px）半径50px的圆弧，从0开始到Math.PI*2结束，按照顺时针方向。
          ctx.stroke();

          //二次贝赛尔曲线
          ctx.beginPath();
          ctx.moveTo(460,125);
          ctx.quadraticCurveTo(520,0,580,125);//控制点坐标（520*0px) 结束点坐标（580*125px)的二次贝赛尔曲线
          ctx.stroke();

          //三次贝赛尔曲线
          ctx.beginPath();
          ctx.moveTo(610,125);
          ctx.bezierCurveTo(620,70,720,70,730,125);//控制点1坐标（620*70px) 控制点2坐标（720*70px) 结束点坐标（730*125px)的三次贝赛尔曲线
          ctx.stroke();


          // 添加径向渐变
          var radgrad = ctx.createRadialGradient(827,73,10,827,73,60);//起始圆心坐标（827*73px）半径10px    结束圆心坐标（827*73px）半径60px 的径向渐变
          radgrad.addColorStop(0, '#ff0000');
          radgrad.addColorStop(.5, '#0000ff');
          radgrad.addColorStop(1, '#00ff00');
          ctx.fillStyle = radgrad;

          //导入SVG里path
          var path = new Path2D("M 760 75 A 60 60 0 1 1 821 136");
          ctx.fill(path);

          //写入文字
          ctx.fillStyle = "#ff5256"; //设置填充颜色
          ctx.strokeStyle ="#ff5256"; //设置描边颜色 
          ctx.lineWidth = 2 //设置描边宽度
          ctx.font = "48px -apple-system";
          ctx.fillText("Jou", 925, 110);//实心文字
          ctx.strokeText("bn", 1008, 110);//空心文字





        var raf;
        var running = false;

        var ball = {
          rectx: 0,
          rectlength:100,
          x: 100,
          y: 300,
          vx: 12,
          vy: 12,
          radius: 5,
          color: 'blue',
          draw: function() {
            ctx.beginPath();
            ctx.arc(this.x, this.y, this.radius, 0, Math.PI * 2, true);
            ctx.closePath();
            ctx.fillStyle = this.color;
            ctx.fill();
            ctx.fillStyle = 'red'
            ctx.fillRect(this.rectx,canvas.height-4,this.rectlength,4);
          }
        };

        function clear() {
          ctx.fillStyle = 'rgba(255,255,255,1)';
          ctx.fillRect(0,130,canvas.width,canvas.height-130);
        }

        function drawball() {
          clear()
          ball.draw();
          ball.x += ball.vx;
          ball.y += ball.vy;
          console.log(ball.x,ball.y,ball.rectx)
          if((ball.y) >= canvas.height){
            console.log(ball.x,ball.y,ball.rectx)
          }
          if(((ball.x>=ball.rectx+ball.rectlength)||(ball.x<=ball.rectx)) &&((ball.y) >= canvas.height)){

            alert('game over')
          }

          if (ball.y > canvas.height || ball.y < 150) {
            ball.vy = -ball.vy;
          }
          if (ball.x > canvas.width || ball.x  < 0) {
            ball.vx = -ball.vx;
          }
          
          raf = window.requestAnimationFrame(drawball);
        }

        canvas.addEventListener('mousemove', function(e){

          ball.rectx = e.clientX-canvas.offsetLeft-ball.rectlength/2-541;
          ball.rectx=ball.rectx/706*1200
        });

        canvas.addEventListener('mouseenter',function(e){
          if (!running) {
            raf = window.requestAnimationFrame(drawball);
            running = true;
          }
        });
        canvas.addEventListener('mouseout', function(e){
          window.cancelAnimationFrame(raf);
          running = false;
        });
        ball.draw();
        }
      }
</script>
