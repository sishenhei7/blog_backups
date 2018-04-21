# 功能强大的swiper插件

## 概述

今天体验了一下swiper,真是太强大了,无论是PC端还是移动端,各种轮播滑块效果随便实现.美中不足的是,有些实现需要自己想办法.下面我记录下我的需求和我的实现,供以后开发时参考,相信对其他人也有用.

这里是[swpier.js官网](http://www.swiper.com.cn/).这里是[swiper的官方demo](http://www.swiper.com.cn/demo/index.html).

### 自己的需求

1. 全屏且没有滑动条
2. 点击链接可以跳转到其它slide.
3. 自定义分页器.

### 初始demo

这是初始demo代码,直接复制到html文件中就可以打开了(需要联网).

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>Swiper demo</title>
    <!-- Link Swiper's CSS -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/Swiper/4.x.x/css/swiper.min.css">

    <!-- Demo styles -->
    <style>
    body {
        background: #eee;
        font-family: Helvetica Neue, Helvetica, Arial, sans-serif;
        font-size: 14px;
        color:#000;
        margin: 0;
        padding: 0;
    }
    .swiper-wrapper{
    transition-delay:.3s;
    }
    .swiper-container {
        width: 500px;
        height: 300px;
        margin: 20px auto;
    }
    .swiper-slide {
        text-align: center;
        font-size: 18px;
        background: #fff;
        
        /* Center slide text vertically */
        display: -webkit-box;
        display: -ms-flexbox;
        display: -webkit-flex;
        display: flex;
        -webkit-box-pack: center;
        -ms-flex-pack: center;
        -webkit-justify-content: center;
        justify-content: center;
        -webkit-box-align: center;
        -ms-flex-align: center;
        -webkit-align-items: center;
        align-items: center;
    }
    .swiper-slide:nth-child(2){
        background:#3183ff;
        color:#fff;}
    .swiper-slide p{
        transform:translateX(-200px);
        opacity:0;
        transition:all .4s;}
    .ani-slide p{
        transform:translateX(0);
        opacity:1;
        }
    </style>
</head>
<body>
    <!-- Swiper -->
    <div class="swiper-container">
        <div class="swiper-wrapper">
            <div class="swiper-slide"><p>Slide 1</p></div>
            <div class="swiper-slide"><p>Slide 2</p></div>
            <div class="swiper-slide"><p>Slide 3</p></div>
        </div>
        <div class="swiper-pagination"></div> 
    </div>

<!-- Swiper JS -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/Swiper/4.x.x/js/swiper.min.js"></script>

<!-- Initialize Swiper -->
<script>
    var swiper = new Swiper('.swiper-container',{
        direction : 'vertical',
        followFinger : false,
        speed:800,
        mousewheel: true,
        pagination : {
            el:'.swiper-pagination',
        },
        on:{
            init:function(swiper){
                slide=this.slides.eq(0);
                slide.addClass('ani-slide');
            },
            transitionStart: function(){
                for(i=0;i<this.slides.length;i++){
                    slide=this.slides.eq(i);
                    slide.removeClass('ani-slide');
                }
            },
            transitionEnd: function(){
                slide=this.slides.eq(this.activeIndex);
                slide.addClass('ani-slide');
            },
        }
    });
</script>
</body>
</html>
```

### 过程

挖坑,待填.

### 实现需求的test文件

需要说明的是,swiper不兼容ie.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>Swiper demo</title>
    <!-- Link Swiper's CSS -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/Swiper/4.2.0/css/swiper.min.css">

    <!-- Demo styles -->
    <style>
    ::-webkit-scrollbar {display:none}
    body {
        background: #eee;
        font-family: Helvetica Neue, Helvetica, Arial, sans-serif;
        font-size: 14px;
        color:#000;
        margin: 0;
        padding: 0;
    }
    .swiper-wrapper{
    transition-delay:.3s;
    }
    .swiper-container {
        width: 100%;
        height: 942px;
        margin: 20px auto;
    }
    .swiper-slide {
        text-align: center;
        font-size: 18px;
        background: #fff;

        /* Center slide text vertically */
        display: -webkit-box;
        display: -ms-flexbox;
        display: -webkit-flex;
        display: flex;
        -webkit-box-pack: center;
        -ms-flex-pack: center;
        -webkit-justify-content: center;
        justify-content: center;
        -webkit-box-align: center;
        -ms-flex-align: center;
        -webkit-align-items: center;
        align-items: center;
    }
    .swiper-slide:nth-child(2){
        background:#3183ff;
        color:#fff;}
    .swiper-slide p{
        transform:translateX(-200px);
        opacity:0;
        transition:all .4s;}
    .ani-slide p{
        transform:translateX(0);
        opacity:1;
        }
    .swiper-pagination-bullet:nth-child(1) {
      height: 20px;
      width: 30px;
    }
    .test {
      cursor: pointer;
      color: blue;
      text-decoration: underline;
    }
    </style>
</head>
<body>
    <!-- Swiper -->
    <div class="swiper-container">
        <div class="swiper-wrapper">
            <div class="swiper-slide">
              <p>Slide 1</p>
              <div class="test">点我翻到第二页</div>
            </div>
            <div class="swiper-slide"><p>Slide 2</p></div>
            <div class="swiper-slide"><p>Slide 3</p></div>
        </div>
        <div class="swiper-pagination"></div>
    </div>

<!-- Swiper JS -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/Swiper/4.2.0/js/swiper.min.js"></script>
<!-- Jquery -->
<script
  src="http://code.jquery.com/jquery-3.3.1.min.js"
  integrity="sha256-FgpCb/KJQlLNfOu91ta32o/NMZxltwRo8QtmkMRdAu8="
  crossorigin="anonymous"></script>

<!-- Initialize Swiper -->
<script>
    $('.test').click(function() {
      swiper.slideTo(1, 800, false);
    });
    var swiper = new Swiper('.swiper-container',{
        direction : 'vertical',
        followFinger : false,
        speed:800,
        mousewheel: true,
        pagination : {
            el:'.swiper-pagination',
        },
        on:{
            init:function(swiper){
                slide=this.slides.eq(0);
                slide.addClass('ani-slide');
            },
            transitionStart: function(){
                for(i=0;i<this.slides.length;i++){
                    slide=this.slides.eq(i);
                    slide.removeClass('ani-slide');
                }
            },
            transitionEnd: function(){
                slide=this.slides.eq(this.activeIndex);
                slide.addClass('ani-slide');
            },
        }
    });
</script>
</body>
</html>

```

