## 概述

html中有许多用于嵌入各种类型内容的标签，包括：embed，audio，canvas，iframe，img，math，object，svg和video。之前我在很多地方都看到了**object标签**和**embed标签**，现在做一个总结供以后开发时参考，相信对其他人也有用。

参考资料：
[html中object和embed标签的区别](https://blog.csdn.net/byxdaz/article/details/60467224)
[http://www.360doc.com/content/16/0603/11/27834384_564696725.shtml](http://www.360doc.com/content/16/0603/11/27834384_564696725.shtml)

### object和embed标签

object和embed标签常用来嵌入一些对象，比如图像，音频，视频，java applets，activeX，pdf以及flash。

但是由于**IE只支持对Object的解析**；**火狐，谷歌，safari支持对Embed的解析**，所以为了兼容多个浏览器，常在**object标签里面嵌入embed标签**。比如下面的嵌入flash的代码：

```
<OBJECT classid="clsid:D27CDB6E-AE6D-11cf-96B8-444553540000" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0" WIDTH="550" HEIGHT="400" id="myMovieName">
<PARAM NAME=movie VALUE="myFlashMovie.swf">
<PARAM NAME=quality VALUE=high>
<PARAM NAME=bgcolor VALUE=#FFFFFF>
<EMBED src="http://www.doflash.net/"/support/flash/ts/documents/myFlashMovie.swf"" quality=high bgcolor=#FFFFFF WIDTH="550" HEIGHT="400" NAME="myMovieName" ALIGN="" type="application/x-shockwave-flash" PLUGINSPAGE="http://www.macromedia.com/go/getflashplayer">
</EMBED>
</OBJECT>
```

### js插入flash

虽然上面的写法是Macromedia一直以来的官方写法，最大限度的保证了flash的功能，没有兼容问题。但是还是有一些其它的问题，比如：
1. 写法不符合W3C的规范。
2. 需要用户点击才能交互。
3. 没有flash版本检测。

为了解决上述问题，一般**用js插件来嵌入flash**。这就是为什么flash要用js插入的原因。

### 移动端使用flash

由于各种原因，Adobe公司宣布，该公司停止为移动浏览器开发Flash Player。这就导致在**移动设备上并不能播放Flash**。


