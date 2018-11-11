## 概述

html中的**link标签**一般用来引入css文件。但是也可以通过**rel属性**来进行**预加载**。本文记录下相关方法，供以后开发时参考，相信对其他人也有用。

参考资料：
[mdn 通过rel="preload"进行内容预加载](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Preloading_content)

### 基本方法

一般我们用下面的代码进行**预加载背景图**：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div style="display:none;">
        <img src="pic1.jpg" alt="预加载图片1">
        <img src="pic2.jpg" alt="预加载图片2">
    </div>
</body>
</html>
```

但是上面的方法很繁琐，代码也不容易看懂。值得庆幸的是，可以向下面的示例那样利用**link标签进行预加载css，js，image，MP4等资源**。

```
<head>
  <meta charset="utf-8">
  <title>Video preload example</title>
  <link rel="preload" href="style.css" as="style">
  <link rel="preload" href="main.js" as="script">
  <link rel="preload" href="sintel-short.mp4" as="video" type="video/mp4">
</head>
<body>
</body>
```

其中**type是MIME类型**，最好写一下。

基本上你能想到的**资源**都能放到href里面进行预加载：audio, document, embed, font, image, js, css, worker, video等。

### 跨域预加载

可以通过设置**crossorigin属性**进行跨域预加载。（同时需要服务器那边进行相应设置）

```
<head>
  <meta charset="utf-8">
  <title>Web font example</title>

  <link rel="preload" href="fonts/cicle_fina-webfont.eot" as="font" type="application/vnd.ms-fontobject" crossorigin="anonymous">
</head>
<body>

</body>
```

### 媒体查询预加载

在预加载的时候还可以进行**媒体查询**，当屏幕宽度达到某个条件的时候才预加载。

```
<head>
  <meta charset="utf-8">
  <title>Responsive preload example</title>

  <link rel="preload" href="bg-image-narrow.png" as="image" media="(max-width: 600px)">
  <link rel="preload" href="bg-image-wide.png" as="image" media="(min-width: 601px)">

  <link rel="stylesheet" href="main.css">
</head>
<body>
  <header>
    <h1>My site</h1>
  </header>
</body>
```

### 脚本化预加载

下面的示例将预加载一个js文件，但**并不执行**。

```
var preloadLink = document.createElement("link");
preloadLink.href = "myscript.js";
preloadLink.rel = "preload";
preloadLink.as = "script";
document.head.appendChild(preloadLink);
```

下面的示例将**执行**这个脚本：

```
var preloadedScript = document.createElement("script");
preloadedScript.src = "myscript.js";
document.body.appendChild(preloadedScript);
```

### 与onload进行配合

下面的例子**很巧妙**，代码也很精简，值得学习。

```
<link rel="preload" href="mystyles.css" as="style" onload="this.rel='stylesheet'">
```







