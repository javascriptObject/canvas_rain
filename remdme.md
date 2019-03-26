### 知识回顾
    1.JS可以怎么写？
    通过script标签的src属性，将外部的JS文件引入到html中；
    在html文件中，创建JS的编辑环境：通过SCRIPT标签来实现
    2.用户交互设计的本质：
     用户对页面元素执行了相关操作之后页面做出了对应的反馈。
```angular2html
<div id="box">
    <img id="beautifulGirl" src="1.jpg" alt="">
    <div id="mask"></div>
</div>
<script>
    window.onload = function () {
        // 1、找对象
        var box = document.getElementById("box"),
            mask = document.getElementById("mask"),
            beuGirl = document.getElementById("beautifulGirl"),   // 3、图片的宽高获取
            imgWidth = beuGirl.width,
            imgHeight = beuGirl.height;
        box.style.cssText = "width:"+ imgHeight +"px;height:"+ imgHeight +"px;position:relative;";
        mask.style.cssText = "width:"+ imgHeight +"px;height:"+ imgHeight +"px;background:rgba(0,0,0,0.15);position:absolute;top:0;display:none";
        // 2、找事件 onmouseover/onmouseenter
        box.onmouseover = function () {
            mask.style.display = "block";
        };
        box.onmouseout = function () {
            mask.style.display = "none";
        }
    }
</script>
```    
    3、交互设计的基本流程：
     (1) 找对象
     (2) 找事件: 用户操作所触发的事件 ;
     (3) 写功能：将需要实现的效果写在事件中；
### 目标
     搞定雨滴效果
### 一、画布的使用
    1. 什么是画布
    就是html中的一个标签：canvas
    2. 如何使用
     通过JS来使用
    3. JS如何与CANVAS关联起来
    (1)通过getContext("2d")方法来给画布配置画笔，使其具备绘画功能；
    var ctx = canvas.getContext("2d")创建画笔 
    (2)通过fillStyle/strokeStyle属性来设置画笔的颜色；
    fillStyle:设置的是填充体的颜色
    strokeStyle:设置的是线条的颜色；     
    (3)通过具体的就去来实现图形的绘制；
    fillRect():用来绘制矩形的；
        有四个参数，第一和第二个参数表示的是下笔的坐标值；第三和第四个参数表示的是所
        绘制的矩形的宽和高；
        strokeRect():用来绘制矩形边框线的;
           参数与fillRect一致；
### 练习1：
    绘制两个出线重叠区域，但颜色不同的的矩形;     
```angular2html
<style>
    body,canvas{
        margin:0;
        padding:0;
        display: block;
    }
</style>
<canvas id="canvas">
    你的浏览器不支持canvas
</canvas>
<script>
    var canvas = document.getElementById("canvas"),
        ctx = canvas.getContext("2d");
    function drawFillReact(color,x,y,w,h) {
        ctx.fillStyle = color;
        ctx.fillRect(x,y,w,h);
        // ctx.globalCompositeOperation = "source-in";//只显示重叠部分
        // ctx.globalCompositeOperation = "source-out";//只显示新图不重叠部分
        // ctx.globalCompositeOperation = "source-atop";//只显示新图重叠部分老图仍显示
        // ctx.globalCompositeOperation = "destination-over";//原图覆盖新图
        // ctx.globalCompositeOperation = "destination-in";//绘制原图和新图重叠部分
        // ctx.globalCompositeOperation = "destination-out";//绘制原图和新图不重叠部分
        // ctx.globalCompositeOperation = "destination-atop";//绘制原图和新图重叠部分以及新图
        // ctx.globalCompositeOperation = "lighter";//绘制新图和原图,重叠部分加色处理
        // ctx.globalCompositeOperation = "darken";//
    }
    new drawFillReact("skyblue",30,30,150,80);
    new drawFillReact("orange",150,80,150,80);
</script>
```      
### 二、面向对象
    1. 对象的表现形式：
    {
        propertyName:propertyValue,
        functionName:function(){ 功能代码; }
    }    
    2.对象的定义方式;
    (1)字面量方式；
```angular2html
    var obj = {
        name:"charles",
        sex:"man",
        teach:function () {
            console.log("talk");
        }
    };
```
    2.构造器方式；
```angular2html
    function Person(name,age) {
        this.name = name;
        this.age = age;
    }
var chen = new Person("charles",39);
    console.log(chen.name);
```    
 ###  穿插一个小demo
 ```angular2html
<p style="width:30px;height:30px;background:orange;"></p>
<script>
    var p = document.getElementsByTagName("p")[0],
        num = 0;
    setInterval(function () {
        if(num<window.innerHeight){
            num += 5;
            p.style.marginTop = num + "px";
        }else {
            num = 0;
        }
    },20)
</script>
```
###  三、雨滴的具体实现
    1. 搭建结构
    新建一个html文件，资助在body中添加咯一个id值为canvas的canvas标签；
    2. 设计脚本
    (1) 新建一个js文件，将将这个文件关联到 html中