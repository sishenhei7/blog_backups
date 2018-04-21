# 微信小程序：scroll滑到指定位置

## 概述

这是我开发微信小程序遇到的坑中的一个，专门记录下来，供以后开发时参考，相信对其他人也有用。

scroll滑到指定位置，有两种解决方案，一种是用scroll-view标签，另一种是用```wx.pageScrollTo```这个api。

### 用scroll-view标签

这个标签适合于屏幕内的**小范围滚动和锚点滚动**，分别通过scroll-top和scroll-into-view这两个标签属性来实现。注意这是标签内属性，所以需要动态改变的话，就需要用```this.setData```动态设置scroll-top和scroll-into-view的值。例子如下：

```
//wxml
<scroll-view scroll-y style="height: 200px;"" scroll-top="{{scrollTop}} scroll-into-view="{{toView}}">
    <view id="yellow" class="scroll-view-item bc_yellow"></view>
    <view id="blue" class="scroll-view-item bc_blue"></view>
  </scroll-view>

//wxs
Page({
  data: {
    toView: 'blue',
    scrollTop: 100
  }
})
```

这里有一个坑就是scroll-view一定要设置**高度属性**，而且不能是百分比。所以为了使scroll-view**自适应屏幕高度**，我们一般用```wx.getSystemInfo```获取屏幕高度，然后动态设置。例子如下：

```
//wxml
<scroll-view scroll-y="true" style="height:{{scrollHeight}}px;">

//wxs
var that = this;
wx.getSystemInfo({
      success: function (res) {
        that.setData({
          scrollHeight: res.windowHeight
        });
      }
    });
```

如果有其它需求的话，可以把上面的res.windowHeight换成各种运算。

### 用pageScrollTo方法

这种方法适用于屏幕的**大范围滚动**，并且不支持锚点滚动。

wx.pageScrollTo方法和原生js方法有点类似，不过它是接受一个**对象**为参数。用法如下,把100改成想要的值即可。

```
wx.pageScrollTo({
  scrollTop: 100
})
```

但是这里有一个坑，就是这个方法不管是放在onLoad还是放在onReady或者onShow里面都是无效的，具体原因不明。所以一般运用就是用**事件触发**。














