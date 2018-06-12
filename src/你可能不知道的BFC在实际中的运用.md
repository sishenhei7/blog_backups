## 概述

BFC是**块级格式化上下文**，它的一个令人熟知的运用是双飞翼布局或者两列布局。但其实它在其它地方也有很**巧妙的运用**。我把研究的心得记录下来，供以后开发时参考，相信对其他人也有用。

参考资料：
[mdn块格式化上下文](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)
[为什么BFC可以解决margin叠加问题？](https://segmentfault.com/q/1010000009250299)
[BFC——一个我们容易忽视掉的布局神器](https://www.cnblogs.com/lzbk/p/6057097.html)

### BFC精髓

触发BFC的元素会变成一个独立的盒子，这个独立盒子里的布局**不受外部影响**，**也不会影响到外面的元素！**这就是BFC的**精髓**所在！

常用的触发BFC的方法：
1. 设置除 float:none 以外的属性值（如：left | right）。
2. 设置除 overflow: visible 以外的属性值（如： hidden | auto | scroll）。
3. 设置 display属性值为: inline-block。
4. 设置 position 属性值为：absolute | fixed。

### 解决margin叠加问题

如下面的代码所示：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
  <style>
    .div1 {
      margin-bottom: 150px;
      width: 200px;
      height: 200px;
      background-color: green;
    }
    .div2 {
      margin-top: 100px;
      width: 200px;
      height: 200px;
      background-color: purple;
    }
  </style>
</head>
<body>
  <div class="div1"></div>
  <div class="div2"></div>
</body>
</html>
```

两个块级元素div1和div2会发生**上下边距重叠**的情况。但是如果我们把div的display改为inline-block，就不会发生上下边距重叠了。代码如下所示：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
  <style>
    .div1 {
      margin-bottom: 150px;
      width: 200px;
      height: 200px;
      background-color: green;
      display: inline-block;
    }
    .div2 {
      margin-top: 100px;
      width: 200px;
      height: 200px;
      background-color: purple;
    }
  </style>
</head>
<body>
  <div class="div1"></div>
  <div class="div2"></div>
</body>
</html>
```

其实严格说来，并不是由于display: inline-block;触发了BFC从而使上下边距不叠加的，因为BFC的精髓是：触发BFC的元素会变成一个独立的盒子，这个独立盒子里的布局**不受外部影响**，也**不会影响到外面的元素**！而div1的margin-bottom**不能算是盒子内部的布局**。

那是什么原因导致上下边距不叠加的？

因为W3C官方文档规定，上下边距叠加必须满足2个条件：一个是**都是块级元素**；另一个是都在**同一个BFC**里面。参见：[w3.org关于margin合并的规范说明](https://www.w3.org/TR/CSS2/box.html#collapsing-margins)

所以我们把div1设置为inline-block之后，它就**不是块级元素**了，不满足条件1，自然就不会发生上下边距叠加。

### 解决margin影响父元素问题

我们给父元素的第一个子元素设置margin-top值，就会发现它影响到了父元素的margin值，**原因是它和父元素的margin-top值合并了**。实例如下：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
  <style>
    .div1 {
      width: 200px;
      height: 200px;
      background-color: green;
    }
    .div2 {
      margin-top: 100px;
      width: 100px;
      height: 100px;
      background-color: purple;
    }
  </style>
</head>
<body>
  <div class="div1">
    <div class="div2"></div>
  </div>
</body>
</html>
```

我们一般是通过**设置父元素的padding-top值为1px**来解决的。但是利用BFC有更加简便的解决方法，那就是利用**overflow: hidden**或者**overflow: auto**，使父元素形成一个BFC，从而它里面的元素**不受外面的元素影响**，也**不会影响外面的元素**。实例如下：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
  <style>
    .div1 {
      width: 200px;
      height: 200px;
      background-color: green;
      overflow: auto;
    }
    .div2 {
      margin-top: 100px;
      width: 100px;
      height: 100px;
      background-color: purple;
    }
  </style>
</head>
<body>
  <div class="div1">
    <div class="div2"></div>
  </div>
</body>
</html>
```

### 清除浮动

如下面代码所示，如果我们给子元素加一个浮动，那么子元素就**不能撑开父元素**了。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
  <style>
    .div1 {
      width: 200px;
      background-color: green;
      border: 5px solid red;
    }
    .div2 {
      float: left;
      width: 100px;
      height: 100px;
      background-color: purple;
    }
  </style>
</head>
<body>
  <div class="div1">
    <div class="div2"></div>
  </div>
</body>
</html>
```

原因是，子元素通过float触发BFC，成为了一个独立的盒子，根据BFC的精髓，它不会影响外部元素，也不会受外部元素影响，所以**它不会影响父元素的高度，所以不能撑开父元素**。

这个时候，如果给父元素触发BFC，就可以强制把子元素拉到它形成的独立盒子里面（在计算BFC的高度时，浮动元素也参与计算），这样就清除浮动了。而触发BFC最好用的还是**overflow: hidden**或者**overflow: auto**。实例如下：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
  <style>
    .div1 {
      width: 200px;
      background-color: green;
      border: 5px solid red;
      overflow: auto;
    }
    .div2 {
      float: left;
      width: 100px;
      height: 100px;
      background-color: purple;
    }
  </style>
</head>
<body>
  <div class="div1">
    <div class="div2"></div>
  </div>
</body>
</html>
```

### 其它

我们在写css的时候，有时会遇到莫名其妙的问题，这个时候可以考虑给元素**加一个overflow: hidden或者overflow: auto**，使元素触发BFC成为独立盒子，往往能意想不到的解决问题呢！

