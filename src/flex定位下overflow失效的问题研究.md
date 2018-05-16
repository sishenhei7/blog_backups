## 概述

这是我在写移动端页面遇到的问题及解决方法，记录下来供以后开发时参考，相信对其他人也有用。

### 问题

之前写移动端页面，有一个顶条是导航条，需要固定在页面顶部，并且里面的元素需要可以左右滚动。

但是当导航条设置fixed定位时，发现里面的子元素不能横向滚动。

```
position: fixed;
top: 0;
left: 0;
overflow-x: auto;
```

最后通过加一个嵌套元素，给这个**嵌套元素设置absolute定位**解决：

```
//嵌套子元素
position: absolute;
top: 0;
left: 0;
overflow-x: auto;
```

另外还有一个**隐藏系统滚动条**的需求：

```
//less文件
&::-webkit-scrollbar {/*滚动条整体样式*/
    display: none;
}
```

### 延伸1

在移动端其实并不推荐使用fixed定位，除了有一大堆问题之外还有**渲染性能**的问题。

但是如果使用的话，需要注意以下2点：
1. 居中**不要用margin**，而要用translate。否则会出现如下问题：在移动端上滑动页面的时候，fixed定位元素会发生移动。（重排重绘导致）
2. 需要**开启GPU加速**。

开启GPU加速的代码为：
```
//代码1：纯GPU加速
webkit-transform: translateZ(0);
-moz-transform: translateZ(0);
-ms-transform: translateZ(0);
-o-transform: translateZ(0);
transform: translateZ(0);

//代码2：可以带位移的GPU加速
webkit-transform: translate3d(0,0,0);
-moz-transform: translate3d(0,0,0);
-ms-transform: translate3d(0,0,0);
-o-transform: translate3d(0,0,0);
transform: translate3d(0,0,0);
```

### 延伸2

对于滚动，safari上面支持-webkit-overflow-scrolling属性，控制safari的**滚动回弹效果**.

```
/* 当手指从触摸屏上移开，会保持一段时间的滚动 */
-webkit-overflow-scrolling: touch; 

/* 当手指从触摸屏上移开，滚动会立即停止 */
-webkit-overflow-scrolling: auto; 
```

所以为了更好的体验，一般有如下**兼容性写法**：

```
overflow-x: auto;
-webkit-overflow-scrolling: touch; 
```

注意，网上说-webkit-overflow-scrolling会有一些bug，所以建议用插件实现，比如betterscroll.js等。

