## 概述

在FF浏览器中有这样一个bug，就是**当鼠标hover在flash区域的时候，滚轮会失效**。原因是ff浏览器没有把滚轮事件嵌入到flash里面去。如果这个flash很小的话，比如直播的视频，会很容易解决；如果这个flash很大占满全屏的话，就会严重影响到体验了。下面是我查资料实践出来的2个解决方案，供以后开发时参考。

### 加一层蒙版

第一种解决方法是在flash的上层**加一层透明的蒙版**。一般来说，会在flash上面放各种内容，这个时候把这些内容放在一个蒙版里面，并且把这个蒙版的大小设置为和flash一般大就可以了。

这个方法的**优点**是，滑动非常顺畅，改动方便；**缺点**是如果flash要触发一些hover事件的话就不能实现了。因为上面有一层蒙版。

### 代码模拟滚轮

可以用js监测滚轮事件，并且**控制滚轮滚动**。

代码如下：

```
var isFF = navigator.userAgent.toLowerCase().indexOf('firefox');
var scroll = $(window).scrollTop();
var delta = event.detail / 3;
var mousewheel=function(event) {
    var event = event ? event : window.event;
    var obj = event.srcElement;
    if (!obj) {
        obj = event.target;
    }
    if(isFF > 0) {
        $(window).scrollTop(scroll + delta*20);
        event.preventDefault();
        event.stopPropagation();
    } else {
        return false;
    }
};
if(isFF > 0) {
    document.body.addEventListener("DOMMouseScroll", mousewheel, false);
    document.body.onmousewheel=mousewheel;
}
```

这种方法的**优点**是没有什么副作用，**缺点**是滚动很僵硬，不够顺滑。

### 思考

受上面jq模拟滚轮的启发，有如下思考：

1. 是不是可以自己来实现**滚动区域**？但是怎么实现滚动顺滑？
2. 如果滚动滚轮触发的是滚动一整个页面，是不是可以做**单屏**了？

以后再探究了。



