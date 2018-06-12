## 概述

前几天偶然看到一个pc端网页，发现用手机打开竟然**同比缩放**了，作为一个前端从业者，我自然想要弄清它到底是怎么缩放的。之后查了它的meta信息，css和js，发现**没有任何兼容手机端的代码**，那它到底是怎么缩放的呢？百思不得其解，最后无意中看别人说**viewport的默认值是980px**，才知道原来是viewport的锅。

于是我深入的复习了一下viewport相关知识，把心得记录下来，供以后开发时参考，相信对其他人也有用。

### 基础概念

本来看了[移动前端开发之viewport的深入理解](http://www.cnblogs.com/2050/p/3877280.html)，但是仍然有一些概念不理解，于是去看了[PPK大神](https://www.quirksmode.org/)对于viewport的研究（[第一篇](https://www.quirksmode.org/mobile/viewports.html)，[第二篇](https://www.quirksmode.org/mobile/viewports2.html)，[第三篇](https://www.quirksmode.org/mobile/metaviewport/)），算是理清了。

首先从css像素和实际像素说起，css像素就是我们写的css的像素，如果没有进行任何缩放的话，css像素就和实际像素是一样的，如果用浏览器对页面进行了缩放，那么css像素不变，**实际像素会根据缩放比例变化**。

值得一提的是，缩放是个很重要的概念，在pc端的网页一般没有进行缩放，所以1px的css像素常常等于1px的实际像素。

1. 取viewport宽高，用document.documentElement.clientWidth和document.documentElement.Height。
2. 取设备宽高，用screen.width和screen.height。
3. 取窗口可视区域宽高，用window.innerWidth和window.innerHeight。
4. 取可视区域偏移量，用window.pageXOffset和window.pageYOffset。

### viewport宽高

viewport宽高是用document.documentElement.clientWidth和document.documentElement.Height来取到的。细心观察可以发现，document.documentElement就是**html元素**，而document.documentElement.clientWidth就是html的宽度。

我们知道，css里面的包含块的宽度会自动延伸至父元素的宽度，所以最外层包含块的宽度会延伸到body的宽度，而body的宽度会延伸到html的宽度，那html的宽度延伸到什么宽度呢？答案是**viewport的宽度**！所以可以用html的宽度来度量viewport的宽度。

另外，这里有一个小坑，就是html的宽度是可以修改的，但是即使修改了html的宽度，document.documentElement.clientWidth和document.documentElement.Height的值**仍然是viewport的宽高**，这是html元素的这2个属性的不同之处。

那当我们改了html的宽度之后，**怎么获得html的宽高**呢？方式是利用**document.documentElement.offsetWidth和document.documentElement.offsetHeight**.

### 移动端viewport

明白了上面viewport的宽高机制之后就很好理解移动端的viewport了。

移动端由于设备的像素很小，所以如果不进行缩放的话，就只能看到pc端网页的部分内容，所以移动端浏览器在兼容的时候，会对页面进行缩放，但是**为了避免在缩放的时候页面发生重排和重绘**，浏览器就有2个viewport，一个是我们常说的viewport，它用来放置页面，它的默认值宽度是980px；另一个是可视区域的visual viewport，它是移动端的可视区域，用来放大或缩小。

在pc端，我们常说的viewport就等于可视区域的visual viewport，所以css像素和实际像素比是1比1。

在移动端，由于放置页面的viewport的宽度默认为980px，但是设备的宽度常常小于980px，所以可视区域的viewport会对它进行**缩放**，直到**页面内容的宽度正好达到屏幕的宽度**。所以我们用手机端看到的pc端页面都是已经缩放过的（如果页面没有设置viewport的话）。

注意，有些情况下，**可视区域的viewport的宽度不会等于放置页面的viewport的宽度**。比如有一个页面，我们设置它的最外层包含块的最小宽度为1250px，那么放置页面的viewport的宽度仍是默认值980px，但是可视区域的viewport的宽度会达到1250px。


那怎么得到可视区域的**缩放比例**呢？它等于设备的宽度除以可视区域的宽度，即：screen.width/window.innerWidth。（注意不是viewport的宽度）

**其他宽度信息**也可以按照上面列出的方法来取。

### 修改viewport

如果是专门为移动端做的页面，那么常常要设置viewport，使它的**两个viewport都等于设备宽度**。代码如下：

```
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=0">
```

viewport的其它设置方法可以参考：[移动前端开发之viewport的深入理解](http://www.cnblogs.com/2050/p/3877280.html)



















