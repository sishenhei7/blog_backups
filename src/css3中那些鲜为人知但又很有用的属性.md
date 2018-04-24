## 概述

这是我在写**移动端页面**的时候遇到的，css3中鲜为人知但又很有用的属性，记录下来，供以后开发时参考，相信对其他人也有用。

### tap-highlight-color

在**移动端开发**中，我们需要在用户轻按屏幕或按钮的时候给用户一定的反馈，这就需要tap-highlight-color这个属性了，它会在用户轻按一个链接或者JavaScript可点击元素时给元素覆盖一个高亮色，示例如下：

```
//轻按时覆盖红色
-webkit-tap-highlight-color: red;
//取消这个高亮
-webkit-tap-highlight-color: transparent;
```

另外还有很炫酷的点击效果，是用**css houdini**实现的，但由于限制比较多，所以暂时用的不是很广泛。

### 点击穿透

我们经常为这样的事情苦恼，点击某个元素的时候会被另一个元素挡住，从而点击失败。这个时候就需要**点击穿透pointer-events**这个属性了。示例如下：

```
//点击会穿透当前层
pointer-events: none;
//点击不会穿透当前层
pointer-events: auto;
```

另外移动端有一个**点击穿透问题**，这个点击穿透问题是通过给tao或touch事件**延迟300ms**执行实现的，比如jQuery里面的fadeOut使duration大于300ms。


### 自定义系统滚动条

有时候我们觉得浏览器自带的滚动条太丑了，那么可以**自定义浏览器的滚动条**，示例如下：

```
/*定义滚动条高宽及背景 高宽分别对应横竖滚动条的尺寸*/    
{
    width: 6px;
    height: 6px;
    background-color: #F5F5F5;
}

/*定义滚动条轨道 内阴影+圆角*/
::-webkit-scrollbar-track
{
    -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
    border-radius: 10px;
    background-color: #FFF;
}

/*定义滑块 内阴影+圆角*/
::-webkit-scrollbar-thumb
{
    border-radius: 10px;
    -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3);
    background-color: #AAA;
}
```

它的**设置项**如下：
- ::-webkit-scrollbar 滚动条整体部分
- ::-webkit-scrollbar-thumb  滚动条里面的小方块，能向上向下移动（或往左往右移动，取决于是垂直滚动条还是水平滚动条）
- ::-webkit-scrollbar-track  滚动条的轨道（里面装有Thumb）
- ::-webkit-scrollbar-button 滚动条的轨道的两端按钮，允许通过点击微调小方块的位置。
- ::-webkit-scrollbar-track-piece 内层轨道，滚动条中间部分（除去）
- ::-webkit-scrollbar-corner 边角，即两个滚动条的交汇处
- ::-webkit-resizer 两个滚动条的交汇处上用于通过拖动调整元素大小的小控件










