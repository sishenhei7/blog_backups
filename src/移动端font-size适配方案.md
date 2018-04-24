# 移动端font-size适配方案

## 概述

这是我研究移动端页面时的**思考**，记录下来供以后开发时参考，相信对其他人也有用。由于我写移动端页面写的还比较少，一些问题都还没遇到，所以我的这篇博文不免有些错误的地方，还请**大佬多多指正**。

这篇文章是基于[网易的移动端屏幕适配方案](http://res.nie.netease.com/comm/doc/professional/%E7%A7%BB%E5%8A%A8%E7%AB%AF%E5%BC%80%E5%8F%91%E8%A7%84%E8%8C%83.html)而来的。

### 思考

在移动端开发中，对于页面屏幕适配要解决哪些问题？

1. 对于不同的dpr，图片会有模糊的情况，怎么适配？
2. 对于不同的屏幕宽度，怎么适配？
3. 对于不同的内容：容器，文字和图片，怎么适配？

对于移动适配，我个人希望达到的效果是，**对于不同的屏幕，在视觉上为了方便阅读，页面只需要简单地放大或缩小即可。**但是由于流式布局(根据父容器的百分比来决定宽高)具有很大的局限性，所以我们采用rem布局。

### dpr

dpr是什么？简单来说，dpr是**实际像素与看到的像素的比值**。比如说你看到的是1px，那么在dpr为2的设备上，它实际上是由2个像素点组成的，每个像素点的**实际大小是0.5px**。

dpr会造成什么问题？比如一个```200px*300px```的图片，在dpr为1的设备上，显示正常，但是在dpr为2的设备上，由于实际像素变成了```400px*600px```(虽然实际大小仍然只有```200px*300px```)，那些多余的像素是设备**推测出来**的，所以会有**图片变模糊**的问题。

怎么解决？网上的解决方法说的很复杂，真的听不懂。。。但是简单来说，解决方案是**使用```400px*600px```的图片**，但是规定它的大小为```200px*300px```，所以在dpr为1的设备上，图片是被“压缩”过的，但是并不影响视觉效果；而在dpr为2的设备上，因为本身的图片大小是```400px*600px```，所以那些多余的px就从这里取了，**不会通过设备推测**，所以视觉效果会更好。

那么对于dpr>2的设备呢？实际上视觉效果影响不大，可以忽略。等到有足够的影响成为一个问题之后，再来解决。

### 屏幕宽度

**机制**是：对于不同宽度的屏幕，我们用js取到屏幕的宽度，然后根据这个宽度同比缩放font-size，由于我们的css是用rem写的，所以页面内容也会同比缩放，达到我们想要的效果。下面具体来讲实施方案。

首先给html设置viewport：

```
<meta name="viewport" content="width=device-width, maximum-scale=1.0, minimum-scale=1.0, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
```

然后，对于手机端，我们希望以iphone6作为参照，在其它屏幕上同比放大或缩小。

(1)**以iphone6作为参照**，iphone6的宽度是375px，dpr为2，所以对于上面显示的375px的图，我们需要的图片大小是750px，所以我们拿到的psd设计图的宽度必须是750px。为了方便书写rem，我们希望psd设计图上750px对应的rem是7.5rem。而设计图上面750px在iphone6上面的实际大小是375px，所以我们需要设置iphone6的font-size=375/7.5px=50px。更一般地，由于移动端的font-size的默认值是16px，所以我们更倾向于用一个百分比来设置font-size：font-size=50/16=312.5%。(注意：用px和百分比没有本质上的不同。)

(2)**在其它屏幕上进行缩放**，为了解决这个问题，我们用js来读取屏幕的宽度，然后利用这个宽度来进行缩放，代码如下：

```
var initScreen=function(){
    $("html").css("font-size", document.documentElement.clientWidth / 375 * 312.5 + "%");
}
```

最后，我们需要解决横屏问题和用户手动缩放问题，他们本质上都是改变屏幕宽度的问题，所以我们监听resize事件或者onorientationchange事件，当发生的时候，重新调用initScreen方法。代码如下：

```
$(window).on('onorientationchange' in window ? 'orientationchange' : 'resize', function () {
    setTimeout(initScreen, 200);
});
```

*注意：*上面的代码并不是原生js，要引入zepto库！也可以用原生js实现，不过要考虑兼容性问题，我就不贴出代码了。

**另外**，为了增加代码的健壮性，在js加载不成功的时候也能进行适配，建议在css加上媒体查询：

```
html{ font-size: 312.5%; }
@media screen and (max-width:359px) and (orientation:portrait) {
    html { font-size: 266.67%; }
}
@media screen and (min-width:360px) and (max-width:374px) and (orientation:portrait) {
    html { font-size: 300%; }
}
@media screen and (min-width:384px) and (max-width:399px) and (orientation:portrait) {
    html { font-size: 320%; }
}
@media screen and (min-width:400px) and (max-width:413px) and (orientation:portrait) {
    html { font-size: 333.33%; }
}
@media screen and (min-width:414px) and (max-width:431px) and (orientation:portrait){
    html { font-size: 345%; }
}
@media screen and (min-width:432px) and (max-width:479px) and (orientation:portrait){
    html { font-size:360%; }
}
@media screen and (min-width:480px)and (max-width:639px) and (orientation:portrait){
    html{ font-size:400%;}
}
@media screen and (min-width:640px) and (orientation:portrait){
    html{ font-size:533.33%;}
}
```


### 容器，文字和图片

我查了很多资料，对于其它适配方案，比如流式布局，栅格化布局等，都对容器，文字和图片的尺寸有不同的写法。但是由于我这个方案只是缩放，并没有额外的需求，所以对于容器，文字和图片的大小，都**用rem就行了**，暂时没有想出不能用rem的情况，等以后遇到问题再来记录。

**注意**：我这种适配方案中，1rem的实际大小是50px，而不是100px。所以0.12rem的字体，在设计稿上面是12px，但是在手机上的实际大小是6px！

### 其它

还有另外一个很重要的度量单位，**vh和vw**，以前由于兼容性太差而不太适用，现在逐渐步入时代舞台，比如网易新闻的css里面就有下面这样一段代码。（目前vh和vw的适配方案还不成熟，等成熟了我再来记录。）

```
@media screen and (max-width: 320px) {
    html {
        font-size:42.667px;
        font-size: 13.33333vw
    }
}
@media screen and (min-width: 321px) and (max-width:360px) {
    html {
        font-size:48px;
        font-size: 13.33333vw
    }
}
@media screen and (min-width: 361px) and (max-width:375px) {
    html {
        font-size:50px;
        font-size: 13.33333vw
    }
}
@media screen and (min-width: 376px) and (max-width:393px) {
    html {
        font-size:52.4px;
        font-size: 13.33333vw
    }
}
@media screen and (min-width: 394px) and (max-width:412px) {
    html {
        font-size:54.93px;
        font-size: 13.33333vw
    }
}
@media screen and (min-width: 413px) and (max-width:414px) {
    html {
        font-size:55.2px;
        font-size: 13.33333vw
    }
}
@media screen and (min-width: 415px) and (max-width:480px) {
    html {
        font-size:64px;
        font-size: 13.33333vw
    }
}
@media screen and (min-width: 481px) and (max-width:540px) {
    html {
        font-size:72px;
        font-size: 13.33333vw
    }
}
@media screen and (min-width: 541px) and (max-width:640px) {
    html {
        font-size:85.33px;
        font-size: 13.33333vw
    }
}
@media screen and (min-width: 641px) and (max-width:720px) {
    html {
        font-size:96px;
        font-size: 13.33333vw
    }
}
@media screen and (min-width: 721px) and (max-width:768px) {
    html {
        font-size:102.4px;
        font-size: 13.33333vw
    }
}
@media screen and (min-width: 769px) {
    html {
        font-size:102.4px;
        font-size: 13.33333vw
    }
}
```

















