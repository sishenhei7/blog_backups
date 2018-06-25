## 概述

对于某些网站来说，让用户一键**把网页加入收藏夹**的设计是非常棒的，它能**提醒**用户把网页加入收藏夹，从而增加用户的回访率，使网站获得更多的流量。

在PC端，只有**ie和ff**支持用js把网页加入收藏夹的操作，在移动端目前都不支持把网页加入收藏夹，除了uc手机浏览器，因为uc手机浏览器用的**U4内核**，经过了一些处理。

由于UC浏览器目前的用户数量还是挺多的，所以探讨一下**UC手机浏览器端怎么实现把网页加入收藏夹**是很必要的，我把结果记录下来，供以后开发时参考。

### 结果

直接上**结果**，通过下面的代码即可调出浏览器的加入收藏夹菜单：

```
window.location.href = "ext:add_favorite";
```

可以通过以下步骤方便的测试：
1. 打开uc浏览器，随便输入一个网站，并且进入这个网站。
2. **在地址栏输入ext:add_favorite并点击进入。**
3. 这时就会出现加入收藏夹菜单！

所以对于一键加入收藏夹，只需要在一个按钮上用JQ绑定点击事件即可：

```
$('.button').on('click', function() {
    window.location.href = "ext:add_favorite";
})
```

### uc的酷影模式

一般的网站会启动uc的**酷影模式**，把PC端代码转化为移动端代码，并且会自动加上“点击收藏”的按钮。详细可以参考：[酷影模式](http://www.uc.cn/product/fun_coolmode.shtml)。

值得一提的是，只有以**马克斯程序(MaxCMS)模板建站的站点**，uc才会启动酷影模式。进入酷影模式之后，uc会自动给网站加载[wapmaxcms_ad_filter.min.js](https://image.uc.cn/s/uae/g/14/bfd/wapmaxcms/wapmaxcms_ad_filter.min.js)，这个js里面就有加入收藏的实现，我就是在这个js里面趴的。

### uc的pwa

令人惊喜的是，uc的U4 2.0内核实现了**PWA**，具体可以参考：[U4 2.0 新特性 —— Add to Home Screen](https://zhuanlan.zhihu.com/p/32333646)里面的**小视屏**。

### uc的开发者工具

可以到[UC PLUS](https://plus.ucweb.com/?spm=ucplus.11213647.c-header-logo.1.42472604p0AsOg)里面下载**uc浏览器开发者版本**和**UC浏览器开发者工具**对UC的页面进行调试。

操作方法：
1. 手机安装**uc浏览器开发者版本**，电脑安装**UC浏览器开发者工具**。
2. 手机**打开USB调试**。
3. 用usb把手机连接到电脑。
4. 电脑的UC浏览器开发者工具会自动识别，然后在手机上会弹出授权框，**点击确认**即可。

### PC端的加入收藏夹代码

```
<script type="text/javascript">
function addFavorite(){
    var bookmarkUrl = "http://baidu.com";
    var bookmarkTitle = "baidu";

    if (window.sidebar) { // For Mozilla Firefox Bookmark
        window.sidebar.addPanel(bookmarkTitle, bookmarkUrl,"");
    } else if( window.external || document.all) { // For IE Favorite
        window.external.AddFavorite( bookmarkUrl, bookmarkTitle);
    } else { // for other browsers which does not support
         alert('浏览器不支持,请按 Ctrl+D 手动收藏!');
         return false;
    }
}
</script><a href="#" rel="sidebar" onclick="addFavorite()">加入收藏</a>
```

具体可以参考：[兼容多数浏览器的js添加收藏夹脚本](https://www.cnblogs.com/xfiver/p/4437233.html)
