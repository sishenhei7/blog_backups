## 概述

之前写过[css3 动画与display:none冲突的解决方案](https://www.cnblogs.com/yangzhou33/p/9119596.html)，但是最近却发现，**使用animation效果比transition好得多**，而且不和display:none冲突。下面我把相关新的记录下来，供以后开发时参考，相信对其他人也有用。

### animation

css3的animation动画除了比transition动画多耗费一点资源之外，在其它方面真的碾压transition动画。比如：
1. 不与display:none冲突。
2. 能够自由设定循环次数。
3. animation-fill-mode属性控制动画完成后的位置。

比如下面的结合display:none的淡入效果：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>animation</title>
  <style>
    button {
      width: 100px;
      height: 40px;
    }
    div {
      display: none;
      width: 200px;
      height: 200px;
      background-color: green;
    }
    button:hover + div {
      display: block;
      animation: fadeOut 1s 1;
      animation-fill-mode: forwards;
    }
    @keyframes fadeOut
    {
    from {opacity: 0;}
    to {opacity: 1;}
    }
    @keyframes fadeIn
    {
    from {opacity: 1;}
    to {opacity: 0;}
    }
  </style>
</head>
<body>
  <button></button>
  <div></div>
  <p>占位</p>
</body>
</html>
```

从上面可以看到，在机制上，animation和transition有一个最大的不同，**就是当元素的display变成block的时候，会自动触发animation效果**，不管元素的display从none变成block还是一开始出现就是block。

所以，一般animation的用法是，**通过给元素添加带有动画的类，来实现动画效果**。

另外，当元素的display从block到none需要执行动画的时候，仍然需要利用**setTimeout**来实现。

### animation.css中的用法

上面我们得出结论，通过给元素添加带有动画的类，来实现动画效果。那么来看看animation.css是怎么做的。

animation通过利用animated类和动画类来控制动画效果：

```
.animated {
    -webkit-animation-duration: 1s;
    animation-duration: 1s;
    -webkit-animation-fill-mode: both;
    animation-fill-mode: both;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }

  to {
    opacity: 1;
  }
}

.fadeIn {
  -webkit-animation-name: fadeIn;
  animation-name: fadeIn;
}
```

很显然，分开2个类是为了更方便的展示，但是当动画的动画时间不一样时，会很难维护，所以在用的时候，**建议用一个类写**，示例如下：

```
@keyframes fadeIn {
  from {
    opacity: 0;
  }

  to {
    opacity: 1;
  }
}

.fadeIn {
    animation: fadeIn 1s both;
}
```

注意：为了方便观看，我在上面的例子中并没有给keyframes和animation添加**-webkit-这些浏览器兼容前缀**，在实际运用上一定要带上这些前缀。



