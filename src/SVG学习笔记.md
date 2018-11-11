## 概述

作为一个前端开发人员，时不时的会用到SVG，所以我简略的学习了一下**SVG基础**，总结了一下并记下来，供以后开发时参考，相信对其他人也有用。

参考资料：
[MDN SVG教程](https://developer.mozilla.org/zh-CN/docs/Web/SVG/Tutorial)

### 介绍

SVG是**XML语言**的一种形式，有点类似XHTML，它可以用来绘制矢量图形。它可以成为任何复杂的组合图形。SVG支持渐变、旋转、滤镜效果、JavaScript接口等等功能。

各种SVG浏览器是有差异的，因此很可能你制作了一个SVG图形，并且用一个工具调试正常后，却在另外一个浏览器中无法正常显示。这是因为**不同的浏览器支持SVG标准的程度不同**。

需要记住以下几个重点：

- SVG的元素和属性必须按标准格式书写，因为**XML是区分大小写**的（这一点和html不同）
- SVG里的属性值**必须用引号引起来**，就算是数值也必须这样做。

SVG文件全局有效的规则是“后来居上”，越后面的元素越可见。

svg文件**可以直接在浏览器上展示**，比如直接使用SVG标签，或者用object，iframe等标签引用SVG文件。比如：

```
<object data="image.svg" type="image/svg+xml" />
<iframe src="image.svg"></iframe>
```

SVG采取的坐标系统是，**x轴向右是正的，y轴向下是正的**。SVG标签的width和height属性定义了画布的尺寸，viewBox属性定义了画布上可以显示的区域。示例如下：

```
<svg width="200" height="200" viewBox="0 0 100 100">
```

### 基本形状

**rect元素**表示矩形：
- x表示矩形左上角的x位置
- y表示矩形左上角的y位置
- width表示矩形的宽度
- height表示矩形的高度
- rx表示圆角的x方位的半径
- ry表示圆角的y方位的半径

示例如下：

```
<rect x="10" y="10" width="30" height="30"/>
<rect x="60" y="10" rx="10" ry="10" width="30" height="30"/>
```

**circle元素**表示圆形：
- r表示圆的半径
- cx表示圆心的x位置
- cy表示圆心的y位置

实例如下：

```
<circle cx="25" cy="75" r="20"/>
```

**Ellipse元素**表示椭圆：
- rx表示椭圆的x半径
- ry表示椭圆的y半径
- cx表示椭圆中心的x位置
- cy表示椭圆中心的y位置

实例如下：

```
<ellipse cx="75" cy="75" rx="20" ry="5"/>
```

**Line**表示直线：
- x1表示起点的x位置
- y1表示起点的y位置
- x2表示终点的x位置
- y2表示终点的y位置

实例如下：

```
<line x1="10" x2="50" y1="110" y2="150"/>
```

**Polyline**表示折线：
- points表示点集数列，一个是x坐标，一个是y坐标。

实例如下：

```
<polyline points="60 110, 65 120, 70 115, 75 130, 80 125, 85 140, 90 135, 95 150, 100 145"/>
```

**polygon**表示多边形，它会在路径的最后一个点处自动回到第一个点：
- points表示点集数列，一个是x坐标，一个是y坐标。

实例如下：

```
<polygon points="50 160, 55 180, 70 180, 60 190, 65 205, 50 195, 35 205, 40 190, 30 180, 45 180"/>
```

**path**表示路径，它可能是SVG中最常见的形状，通常用它来画各种图形：
- d表示一个点集数列以及其它关于如何绘制路径的信息

实例如下：

```
<path d="M 20 230 Q 40 205, 50 230 T 90230"/>
```

### 路径

**path元素**有很多命令：
- M x y表示从什么位置开始。（不画线）
- L x y表示移动画笔到什么位置。（画线）
- H x表示画水平线
- V y表示画垂直线
- Z表示闭合图形，返回至起点。
- C x1 y1, x2 y2, x y表示三次贝塞尔曲线
- S x2 y2, x y用来连接三次贝塞尔曲线
- Q x1 y1, x y表示二次贝塞尔曲线
- T x y用来连接二次贝塞尔曲线
- A rx ry x-axis-rotation large-arc-flag sweep-flag x y表示弧形

### 其它属性

**fill属性**设置对象内部的颜色，还有许多扩展属性比如：fill-opacity，fill-rule等。

**stroke属性**设置绘制对象的线条的颜色，还有许多扩展属性比如：stroke-opacity，stroke-width，stroke-linecap，stroke-dasharray等。

不过，有时候我们可以用css的background-color和border用来替换fill和stroke属性。但是支持程度有限，比如SVG里面不支持before和after伪类等。

**defs标签**里面可以定义一些不会再svg图形里面出现，但是可以被其它元素使用的元素，比如：

```
<svg width="200" height="200" xmlns="http://www.w3.org/2000/svg" version="1.1">
  <defs>
    <style type="text/css"><![CDATA[
       #MyRect {
         stroke: black;
         fill: red;
       }
    ]]></style>
  </defs>
  <rect x="10" height="180" y="10" width="180" id="MyRect"/>
</svg>
```

在defs元素内部还可以定义linearGradient节点，表示**线性渐变**；还可以定义radialGradient节点，表示**径向渐变**。用法如下，其中stop节点表示一个中间值，然后使用xlink:href来引用这些节点。

```
<linearGradient id="Gradient1">
   <stop id="stop1" offset="0%"/>
   <stop id="stop2" offset="50%"/>
   <stop id="stop3" offset="100%"/>
 </linearGradient>
 <linearGradient id="Gradient2" x1="0" x2="0" y1="0" y2="1"
    xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#Gradient1"/>
```

除了用渐变填充之外，还可以用**patterns（图案）填充**，示例如下：

```
<svg width="200" height="200" xmlns="http://www.w3.org/2000/svg" version="1.1">
  <defs>
    <linearGradient id="Gradient1">
      <stop offset="5%" stop-color="white"/>
      <stop offset="95%" stop-color="blue"/>
    </linearGradient>
    <linearGradient id="Gradient2" x1="0" x2="0" y1="0" y2="1">
      <stop offset="5%" stop-color="red"/>
      <stop offset="95%" stop-color="orange"/>
    </linearGradient>

    <pattern id="Pattern" x="0" y="0" width=".25" height=".25">
      <rect x="0" y="0" width="50" height="50" fill="skyblue"/>
      <rect x="0" y="0" width="25" height="25" fill="url(#Gradient2)"/>
      <circle cx="25" cy="25" r="20" fill="url(#Gradient1)" fill-opacity="0.5"/>
    </pattern>

  </defs>

  <rect fill="url(#Pattern)" stroke="black" x="0" y="0" width="200" height="200"/>
</svg>
```

**text节点**用来放置文字，其中属性text-anchor来表示文本流的方向。tspan节点用来标记文本的子部分；tref节点用来引用已经定义的文本；textPath节点用来取得一个任意路径（字体会沿着路径进行排列）。示例如下：

```
<path id="my_path" d="M 20,20 C 40,40 80,40 100,20" />
<text>
  <textPath xlink:href="#my_path">This text follows a curve.</textPath>
</text>
```

**g节点**可以把属性赋给一整个元素集合，示例如下：

```
<g fill="red">
  <rect x="0" y="0" width="10" height="10" />
  <rect x="20" y="0" width="10" height="10" />
</g>
```

**transform属性**可以用来进行各种平移，旋转，缩放等。

### 其它

值得注意的是，**SVG可以无缝嵌入别的svg元素中**，实例如下：

```
<svg xmlns="http://www.w3.org/2000/svg" version="1.1">
  <svg width="100" height="100" viewBox="0 0 50 50">
    <rect width="50" height="50" />
  </svg>
</svg>
```

SVG可以使用clipPath和mask进行**剪切和遮罩**。

SVG还可以在**image节点**中嵌入图像元素，比如：PNG、JPG和SVG等格式文件，示例如下：

```
<svg version="1.1"
     xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink"
     width="200" height="200">
  <image x="90" y="-65" width="128" height="146" transform="rotate(45)"
     xlink:href="https://developer.mozilla.org/media/img/mdn-logo.png"/>
</svg>
```

**foreignObject节点**可以用来在SVG中嵌入XHTML内容，在很多时候，这个节点比其它节点更合适。

