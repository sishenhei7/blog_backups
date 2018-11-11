## 概述

之前觉得2个效果很叼，一个是**3D翻转效果**，另一个是**视差效果**。今天好好的研究一下，把心得记录下来，供以后开发时参考，相信对其他人也有用。

### 3D翻转

**3D翻转效果**其实非常简单，其实就是**perspective属性**的一个运用。perspective属性就是**视距**的意思。下面是一个鼠标放上去图片就会翻转的小示例，看看就会了：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
  <style>
  div {
    margin: 200px 0 0 200px;
    width: 200px;
    height: 200px;
  }
  i {
    display: block;
    width: 100%;
    height: 100%;
    box-shadow: 10px 10px 150px #5fbdff;
    transition: all 2s;
    -webkit-transform: perspective(800px);
    -ms-transform: perspective(800px);
    -o-transform: perspective(800px);
    transform: perspective(800px);
  }
  div:hover i {
    -webkit-transform: perspective(800px) rotateY(180deg);
    -ms-transform: perspective(800px) rotateY(180deg);
    -o-transform: perspective(800px) rotateY(180deg);
    transform: perspective(800px) rotateY(180deg);
  }
  </style>
</head>
<body>
  <div>
    <i></i>
  </div>
</body>
</html>
```

这里有一个更加好看的[开门效果例子](https://www.zhangxinxu.com/study/201806/css3-3d-door-open-single.html)。

### 视差效果

所谓的视差效果，就是**在鼠标或滚轮进行移动的时候，多层次的背景进行不同程度的变换**（有的是位移，有的是翻转等）。

这里有一个鼠标位移进行翻转视差的例子，主要是**监听鼠标进入元素并移动的事件，检查鼠标在元素内部的位置，从而进行不同程度的翻转**。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
  <style>
  html, body {
    height: 100%;
    margin: 0;
    overflow: hidden;
  }

  body {
    color: white;
    font-family: sans-serif;
    font-size: 1.8em;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .content {
    margin: 1px;
    width: 140px;
    height: 140px;
    display: flex;
    align-items: center;
    justify-content: center;
    box-shadow: 0px 0px 10px rgba(0,0,0,0.3);
    transition: all 0.4s cubic-bezier(0.39, 0.575, 0.565, 1);
  }
  </style>
</head>
<body>
  <div class="wowpanel">
    <div class="content">move</div>
  </div>
  <div class="wowpanel">
    <div class="content">your</div>
  </div>
  <div class="wowpanel">
    <div class="content">cursor</div>
  </div>
  <div class="wowpanel">
    <div class="content">over</div>
  </div>
  <script>
    const ANGLE = 45;

    let wowpanels = document.querySelectorAll(".wowpanel");
    let colors = ['#4975FB', '#924DE6', '#EF5252', '#F59500'];

    wowpanels.forEach((element, i) => {
      floatable(element, colors[i]);
    });

    function floatable (panel, color) {
      let content = panel.querySelector(".content");
      content.style.backgroundColor = color;

      panel.addEventListener('mouseout', e => {
        content.style.transform = `perspective(300px)
                       rotateX(0deg)
                       rotateY(0deg)
                       rotateZ(0deg)`;
      });

      panel.addEventListener('mousemove', e => {
        let w = panel.clientWidth;
        let h = panel.clientHeight;
        let y = (e.offsetX - w * 0.5) / w * ANGLE;
        let x = (1 - (e.offsetY - h * 0.5)) / h * ANGLE;

        content.style.transform = `perspective(300px)
                       rotateX(${x}deg)
                       rotateY(${y}deg)`;
      });
    }
  </script>
</body>
</html>
```

类似地，我们也可以不用翻转，而是用**位移**。当鼠标向左移动的时候，图片向右移动；当鼠标向右移动的时候，图片向左移动；当鼠标向上移动的时候，图片向下移动等等，这样就能做出比较好看的**位移视差效果**了。

这里有2篇关于视差效果的文章，很有启发性：

[视差滚动效果原理解析和效果实现](https://www.cnblogs.com/xs-yqz/p/4972902.html)
[小tip: 纯CSS实现视差滚动效果](https://www.zhangxinxu.com/wordpress/2015/03/css-only-parallax-effect/)
