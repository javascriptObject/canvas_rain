# 雨滴demo
    效果如图：
![](rain.gif)    

    核心代码
```angular2html
   window.onload = function () {
       var can = document.getElementById("canvas"),//获取canvas
           ctx = can.getContext("2d"),//画笔
           au_rain = document.getElementById("audio_rain"),
           w = can.width = window.innerWidth,//赋值canvas的宽同时赋值给变量
           h = can.height = window.innerHeight;//赋值canvas的高同时赋值给变量
       /**
        * 当页面发生变化时重新赋值can的宽高并重新赋值w h
        */
       window.onresize = function () {
           w = can.width = window.innerWidth;
           h = can.height = window.innerHeight;
       };
       // 通过函数构造器来创建一个雨滴对象
       function Rain() {}
       /**
        * 给新创建的雨滴对象添加功能，实际上就是添加方法、
        * 通过原型链的方式来添加，本质就是找到Rain的模型来修改模型
        */
       Rain.prototype = {
           /**
            * 雨滴的初始化思路：雨滴是从页面顶部随机x位置落到页面
            * 底部的一个范围内 然后变成由变成透明度
            * 慢慢变淡半径慢慢变大的一个波纹而结束
            * 涉及到的参数有 ：页面顶部的x坐标 页面顶部的y坐标 从上往下掉落的速度
            * 掉落到页面底部的范围值 波纹的的半径 波纹半径变大的速度 波纹的透明度 波纹透明度变淡的速度
            */
           init:function () {
               this.x = Math.random()*w;
               this.y = 0;
               this.speed_y = Math.random()*4 +3;
               this.max_y = (Math.random()*2/10 + 0.6)*h;
               this.r = 1;
               this.speed_r = 1;
               this.opacity = 1;
               this.speed_opacity = 0.96;
           },
           /**
            * 画图的思路：
            * 雨滴只有两种形式、一种是矩形一种是圆形
            * 当其Y值大于其下落的最大的y值时候则画圆否则画矩形
            */
           draw:function () {
               if(this.y > this.max_y){
                   // 此时画圆
                   ctx.beginPath();//开始一个新的绘制路径
                   ctx.strokeStyle = "rgba(0,255,255"+ this.opacity + ")";// 设置弧线的颜色
                   ctx.arc(this.x,this.max_y,this.r,0,Math.PI*2,false);//x y r startAngle endAngle 是否逆时针绘图
                   ctx.stroke();//关闭
                   au_rain.play();
               }else {
                   ctx.fillStyle = "rgb(0,255,255)";
                   ctx.fillRect(this.x,this.y,5,10)
               }
               this.update();//因为此时画的图是不动的，必须不停重绘才能改变其对应的值
           },
           /**
            * 更新的思路：
            * 如果y的值小于最大的y的值此时水滴还是矩形
            * 则需要不更更新其y值，若其y值大于其max_y值则其已变成
            * 咯圆形，则需要更新圆形的半径和透明度
            * 并且需要在圆形的透明度在小于某个值时（肉眼看不太清时）
            * 做特殊处理---->重新初始化
            */
           update:function () {
               if(this.y < this.max_y){
                   this.y += this.speed_y;
               }else {
                   this.opacity *= this.speed_opacity;
                   this.r += this.speed_r;
                   if(this.opacity < 0.01){
                       this.init();
                       au_rain.pause();
                   }
               }
           }
       };
       /**
        * 每过200ms生成一个雨滴并存放在容器中
        * @type {Array}
        */
       var Rains = [];//定义一个存放雨滴的容器
       for(var i = 0;i < 30;i++){
           setTimeout(function () {
               var obj = new Rain();
               obj.init();
               Rains.push(obj);
           },i*200)
       }
       /**
        * 根据屏幕刷新频率调用draw方法即改变y值生成雨滴下落的动画
        * http://www.cnblogs.com/xiaohuochai/p/5777186.html
        */
       ~function move() {
           ctx.fillStyle = "rgba(0,0,0,0.1)";
           ctx.fillRect(0,0,w,h);
           for(var i = 0;i<Rains.length;i++){
               Rains[i].draw();
           }
           requestAnimationFrame(move);
       }();
   };
```    

