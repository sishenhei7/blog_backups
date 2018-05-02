## 概述

前几天刚好看到一个用了**CSS3帧动画**的页面，对它非常感兴趣，就研究了一下，记录在下面，供以后开发时参考，相信对其他人也有用。

PS：以后别人问我用过什么CSS3属性的时候，我也可以不用说常见的animation，transform这些了，我可以说**帧动画**这个高大上的术语了哈哈。

本篇文章参考了：[CSS3 animation的steps方式过渡](https://www.web-tinker.com/article/20679.html)，[CSS遮罩mask](https://www.cnblogs.com/xiaohuochai/p/7182507.html?utm_source=itdadao&utm_medium=referral)。

### 帧动画

帧动画其实就是CSS3中animation中的**steps**。它能够把静态图片处理为动态图片。**原理是不断地切换图片**，从而让图片“动”起来。

下面我通过2个示例来进行说明。

### 兔斯基

你可以从我的博客左上方的公告下面那个**会动的兔斯基**看到这个示例。(暂不支持移动端)(这个兔斯基不是gif！！！)

它的代码如下：

```
/* 兔斯基帧动画 */
@-webkit-keyframes rabbit {
  0% {background-position:0px -0%;}
  100% {background-position:0px -400%;}
}
@keyframes rabbit {
  0% {background-position:0px -0%;}
  100% {background-position:0px -400%;}
}
div.myRabbit {
  height:35px;width:32px;
  -webkit-animation:rabbit 400ms steps(4) infinite;
  animation:rabbit 400ms steps(4) infinite;
  background:url(http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_%e5%85%94%e6%96%af%e5%9f%ba%e6%8f%89%e8%84%b8.png);
}
```

其中rabbit是一个动画，整个过程和一般的animation定义动画没什么区别，除了animation里面用了一个steps(4)，它的意思是把动画0%-100%这个过程分为4段来播放。

有以下三点需要说明：
1. 本来这个用steps(4)的地方是用linear的，linear表示整个动画过程是平滑的，而用steps(4)表示整个动画过程分为4帧，**不是平滑**的。
2. steps(4)还可以接受**第二个参数**，值是step-start或step-end，前者表示第一帧是从```0px -0%```开始的，然后逐渐递增；后者表示第一帧是从```0px -100%```开始的，然后逐渐递增。详细的可以参考：[深入理解CSS3 Animation 帧动画](https://www.cnblogs.com/aaronjs/p/4642015.html)。
3. steps(4)是**对每一段动画而言**的，如果有一个2段的动画，那么每一段将会分为4帧。于是我们可以把上面的代码改为如下。（这个时候就是steps(2)了）

```
/* 兔斯基帧动画 */
@-webkit-keyframes rabbit {
  0% {background-position:0px -0%;}
  50% {background-position:0px -200%;}
  100% {background-position:0px -400%;}
}
@keyframes rabbit {
  0% {background-position:0px -0%;}
  50% {background-position:0px -200%;}
  100% {background-position:0px -400%;}
}
div.myRabbit {
  height:35px;width:32px;
  -webkit-animation:rabbit 400ms steps(2) infinite;
  animation:rabbit 400ms steps(2) infinite;
  background:url(http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_%e5%85%94%e6%96%af%e5%9f%ba%e6%8f%89%e8%84%b8.png);
}
```

### 泼墨效果

每次打开我的博客或者打开我的博客的文章都会有一个**“泼墨”的效果**(暂不支持移动端)，这里也用到了帧动画，代码如下。

```
/* 泼墨帧动画 */
@-webkit-keyframes masky {
  0% {
  mask-position: 0 0;
  -webkit-mask-position: 0 0;
  }
  100% {
    mask-position: 100% 0;
    -webkit-mask-position: 100% 0;
  }
}
@keyframes masky {
    0% {
    mask-position: 0 0;
    -webkit-mask-position: 0 0;
    }
    100% {
      mask-position: 100% 0;
      -webkit-mask-position: 100% 0;
    }
}
div#home{
    -webkit-mask: url(http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_masky.png);
    mask: url(http://images.cnblogs.com/cnblogs_com/yangzhou33/1158868/o_masky.png);
    -webkit-mask-size: 4000% 100%;
    mask-size: 4000% 100%;
    -webkit-animation:masky 1s steps(39) forwards;
    animation:masky 1s steps(39) forwards;
}
```

原理和兔斯基大致相同。但有几点需要说明：
1. 里面用到了**mask属性**，这个是css3的遮罩属性。
2. css3的遮罩属性mask必须写在**整个包含块**里面，比如我就写在了home里面。不能建立一个position为absolute/fixed的遮罩层来专门放它！
















