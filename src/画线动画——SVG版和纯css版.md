## 概述

我们常常在网站中看到一些画线的动画效果，非常炫酷，大多数这种画线动画效果是通过**SVG实现**的，也有不少是用**纯css实现**的，下面我总结了一下这2种方法，供以后开发时参考，相信对其他人也有用。

参考资料：
[移动端网页](http://yys.163.com/m/zj/index.html)
[PC端网页](http://yys.163.com/zj/index.html)

### SVG实现画线效果

在svg里面，可以用**stroke-dashoffset属性**来实现画线效果，示例如下：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>svg</title>
  <style>
  path{
      stroke-dasharray: 1000;
      stroke-dashoffset: 1000;
      animation: draw 5s ease 3;
  }
  @keyframes draw{
      0%{
          stroke-dashoffset: 1000;
      }
      100%{
          stroke-dashoffset: 0;
      }
  }
  </style>
</head>
<body>
<svg width="400px" height="400px" viewBox="100 250 400 400" version="1.1" xmlns="http://www.w3.org/2000/svg">
  <path d="M153 334
  C153 334 151 334 151 334
  C151 339 153 344 156 344
  C164 344 171 339 171 334
  C171 322 164 314 156 314
  C142 314 131 322 131 334
  C131 350 142 364 156 364
  C175 364 191 350 191 334
  C191 311 175 294 156 294
  C131 294 111 311 111 334
  C111 361 131 384 156 384
  C186 384 211 361 211 334
  C211 300 186 274 156 274"
  style="fill:white;stroke:red;stroke-width:2"/>
</svg>
</body>
</html>
```

需要注意的是，必须要设置**stroke-dasharray属性**，并且和stroke-dashoffset一样长；其中1000是个自定义的值，只要比画线的总长度长就行了。

### 纯css画线

对于长方形或者正方形这些规则的图形，用css的**transition效果**即可，代码如下：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>svg</title>
  <style>
    #border {
      position: relative;
      width: 300px;
      height: 300px;
    }
    .border-top {
      position: absolute;
      top: 50px;
      left: 50px;
      width: 0;
      height: 8px;
      background-color: #333;
      transition: all .5s 1s;
    }
    .border-bottom {
      position: absolute;
      bottom: 50px;
      right: 50px;
      width: 0;
      height: 8px;
      background-color: #333;
      transition: all .5s 2s;
    }
    .border-right {
      position: absolute;
      top: 50px;
      left: 250px;
      width: 8px;
      height: 0;
      background-color: #333;
      transition: all .5s 1.5s;
    }
    .border-left {
      position: absolute;
      bottom: 50px;
      left: 50px;
      width: 8px;
      height: 0;
      background-color: #333;
      transition: all .5s 2.5s;
    }
    .animate .border-top, .animate .border-bottom {
      width: 200px;
    }
    .animate .border-right, .animate .border-left {
      height: 200px;
    }
  </style>
</head>
<body>
  <div id="border">
    <i class="border-top"></i>
    <i class="border-right"></i>
    <i class="border-bottom"></i>
    <i class="border-left"></i>
  </div>
  <script>
    setTimeout(function() {
      document.getElementById('border').className += ' animate';
    }, 500);
  </script>
</body>
</html>

```

































