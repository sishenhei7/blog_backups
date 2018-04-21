# 全屏使用swiper.js过程中遇到的坑

## 概述

swiper.js确实是一个很好用的插件，下面记录下我在**全屏**使用过程中遇到的一些坑和解决办法，供以后开发时参考，相信对其他人也有用。

### 通用方案

一般来说，swiper需要放在body的下一层，虽然也可以用一个div包裹它，无论怎样，它的上级和上上级父节点都需要加上如下代码：

```
//有div.wrap包裹
body, html, .wrap, .swiper-container {
    width: 100%;
    height: 100%;
}


//无div包裹
body, html, .swiper-container {
    width: 100%;
    height: 100%;
}
```

### 屏幕自适应

有2种方案，各有优劣：
- **屏幕自适应**，滑动一下滑块即到了下一张slider，优点是体验很好，缺点是slider中超过屏幕的内容会自动被隐藏且不能被看到。
- **屏幕不自适应**，滑动一下会滑动到当前slider的底部，再滑一下才能滑到下一页。优点是能看到整页内容，缺点是体验不好。

以上两种方案其实就是height是具体**数值**还是**百分比**的问题，实现代码如下：

```
//屏幕自适应
.swiper-slide {
    height: 100%; //这里是终点
    width: 100%;
    position: relative;
    overflow: hidden;
}


//屏幕不自适应
.swiper-slide {
    height: 950px; //具体数值，随意填
    width: 100%;
    position: relative;
    overflow: hidden;
}
```

所以如果每一个slider的内容很多，就只能用屏幕不自适应的方法，否则建议选用屏幕自适应的方法。

需要注意的是：

1. 由于height：100%的机制是对比父标签的，所以屏幕自适应方案中可能需要将**所有父容器**的height都设置为100%，甚至有必要使用！important。
2. 屏幕自适应方案安排**页脚**的方法是：把页脚单独放在一个slider里面。

### 消除滚动条

一般情况下使用swiper浏览器右侧是没有滚动条的，但是一些特殊情况或者兼容其它浏览器时就会突然**有滚动条**，这个时候可以用如下css代码解决：

```
overflow-y:hidden;
```

这行代码有些时候有**兼容问题**，但是可以通过设置position：relative解决，具体方法自行百度。

### 兼容问题

如果要**兼容IE7**的话，请使用[swiper2](http://2.swiper.com.cn/)。否则的话，在IE7上面swiper不能切换slider。














