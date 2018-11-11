## 概述

在移动端上面，比如说微信上面打开一个页面，如果有video标签的话，常常会出现**video标签默认置顶**的情况，一般的解决方案是在不需要看见它的时候给它加一个**display:none**进行隐藏。今天在浏览[TGideas文档库](http://tgideas.qq.com/doc/)的时候无意中发现了另一个方案，记录下来，供以后开发时参考，相信对其他人也有用。

### 解决方案

在样式里面加入如下样式：

```
.compatibleStyle {
    backface-visibility:hidden;
    -webkit-backface-visibility:hidden; /* Chrome 和 Safari */
    -moz-backface-visibility:hidden;  /* Firefox */
    -ms-backface-visibility:hidden;  /* Internet Explorer */
    -webkit-perspective: 0;
    -webkit-transform: translate3d(0,0,0);
    visibility:visible;
}
```

然后给video标签加上**compatibleStyle类**就可以了。

参考资料：
[TGideas移动端视频组件](http://tgideas.qq.com/doc/frontend/component/m/mmd.html)
