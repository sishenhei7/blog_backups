## 概述

最近项目用到了**canvas转图片**，但是由于canvas对**文字排版**的支持非常弱，一般我们在canvas上画不同排版的文字（比如竖排文字）都是利用js计算横纵坐标，然后一个字一个字地画出来，今天无意中看到一个使用svg的方法，记录下来，供以后开发时参考，相信对其他人也有用。

参考资料：

[SVG <foreignObject>简介与截图等应用](https://www.zhangxinxu.com/wordpress/2017/08/svg-foreignobject/)

### svg和canvas介绍

svg和canvas都支持图形渲染，它们各有侧重：
- svg：更适用于高保真文档，静态图像，可交互图表图形，2D游戏等。
- canvas：更适用于屏幕截图，视频操作，很多对象的复杂场景，web广告等。

### foreignObject元素

foreignObject元素是个非常强大的元素，它可以在其中使用具有其它XML命名空间的XML元素，简单来说，就是**我们可以利用foreignObject元素把html“画在”svg里面！**

示例如下。其中xmlns表示XML命名空间，标记它是一个svg或者一个xhtml。注意：svg和xhtml都属于XML。

```
<svg xmlns="http://www.w3.org/2000/svg">
  <foreignObject width="120" height="50">
      <body xmlns="http://www.w3.org/1999/xhtml">
        <p>文字。</p>
      </body>
    </foreignObject>
</svg>
```

另外，所有的svg都**必须加上这个svg的命名空间标识**，否则它就会以XML文档树的形式渲染。示例如下：

```
//渲染出来的是数字
<svg width="12" height="12" viewBox="0 0 12 12"><path d="M10.263 10.874L6 6.61l-4.264 4.264a.431.431 0 0 1-.61-.61L5.39 6.001 1.128 1.736a.431.431 0 0 1 .61-.61L6 5.391l4.264-4.263a.431.431 0 0 1 .61.61L6.61 6l4.262 4.264a.43.43 0 1 1-.61.609z" stroke="#979797" fill="#999"/></svg>

//渲染出来的是图形
<svg width="12" height="12" viewBox="0 0 12 12" xmlns="http://www.w3.org/2000/svg"><path d="M10.263 10.874L6 6.61l-4.264 4.264a.431.431 0 0 1-.61-.61L5.39 6.001 1.128 1.736a.431.431 0 0 1 .61-.61L6 5.391l4.264-4.263a.431.431 0 0 1 .61.61L6.61 6l4.262 4.264a.43.43 0 1 1-.61.609z" stroke="#979797" fill="#999"/></svg>
```

### canvas中文本换行

由于我们可以利用foreignObject**把html转化为SVG**，然后我们可以**把svg画在canvas里面**，最后**用canvas把它转化为图形**。

示例如下：

```
//首先初始化canvas
var canvas = document.querySelector('#canvas');
var context = canvas.getContext('2d');

//获取文字的长度
context.font = "20px 微软雅黑";
var textLength = context.measureText(text).width;

//画一个svg图片
var img = new Image();

//对文字长度做判断，一行的文字字要大些，二行的文字字要小些
if(textLength <= 135) {
    img.src = 'data:image/svg+xml;charset=utf-8,<svg xmlns="http://www.w3.org/2000/svg"><foreignObject width="270" height="45"><body xmlns="http://www.w3.org/1999/xhtml" style="margin:0;color:#e7793f;text-align:center;font-size:40px;line-height:45px;font:微软雅黑;">'+ text +'</body></foreignObject></svg>';
} else {
    img.src = 'data:image/svg+xml;charset=utf-8,<svg xmlns="http://www.w3.org/2000/svg"><foreignObject width="270" height="60"><body xmlns="http://www.w3.org/1999/xhtml" style="margin:0;color:#e7793f;text-align:left;font-size:25px;line-height:30px;font:微软雅黑;overflow: hidden;display: -webkit-box;text-overflow: ellipsis;--webkit-box-orient: vertical;--webkit-line-clamp: 2;word-break: break-all;">'+ text +'</body></foreignObject></svg>';
}

//把图片绘制上去，并利用canvas生成图片
img.onload = function() {
    context.drawImage(img, 72, 1080);
    document.querySelector('#ticket').src = canvas.toDataURL('image/png');
}
```

### canvas文字竖排

理论上有三种方法来解决：
1.利用js逐个字符逐行绘制，但是这样对英文要做特殊处理，不能单个字符绘制，而应该旋转90度。相关参考代码如下：

```
fillTextVertical: function(context, text, x, y) {
  var canvas = context.canvas;

  var arrText = text.split('');
  var arrWidth = arrText.map(function (letter) {
    return context.measureText(letter).width;
  });

  var align = context.textAlign;
  var baseline = context.textBaseline;

  //emoji的位置
  var emoji_arr = fn.findEmoji(text);

  //emoji第一位的缓存
  var emoji_first = '';

  if (align == 'left') {
    x = x + Math.max.apply(null, arrWidth) / 2;
  } else if (align == 'right') {
    x = x - Math.max.apply(null, arrWidth) / 2;
  }
  if (baseline == 'bottom' || baseline == 'alphabetic' || baseline == 'ideographic') {
    y = y - arrWidth[0] / 2;
  } else if (baseline == 'top' || baseline == 'hanging') {
    y = y + arrWidth[0] / 2;
  }

  context.textAlign = 'center';
  context.textBaseline = 'middle';

  // 开始逐字绘制
  arrText.forEach(function (letter, index) {
    // 确定下一个字符的纵坐标位置
    var letterWidth = arrWidth[index];
    // 是否需要旋转判断
    var code = letter.charCodeAt(0);

    //是否是emoji的第二位
    if(emoji_arr.indexOf(index) != -1) {
      emoji_first = letter;
      return;
    }
    //是否是emoji
    if(emoji_arr.indexOf(index - 1) != -1) {
      letter = emoji_first + letter;
      emoji_first = '';
    } else {
      if (code <= 256) {
          context.translate(x, y);
          // 英文字符，旋转90°
          context.rotate(90 * Math.PI / 180);
          context.translate(-x, -y);
      } else if (index > 0 && text.charCodeAt(index - 1) < 256) {
          // y修正
          y = y + arrWidth[index - 1] / 2;
      }
    }

    context.fillText(letter, x, y);
    // 旋转坐标系还原成初始态
    context.setTransform(1, 0, 0, 1, 0, 0);
    // 确定下一个字符的纵坐标位置
    var letterWidth = arrWidth[index];
    y = y + letterWidth;
  });
  // 水平垂直对齐方式还原
  context.textAlign = align;
  context.textBaseline = baseline;
},
```

上面方法的缺点是，对emoji符号使用正则匹配的，但仍然会有部分emoji符号匹配不到导致乱码。

2.使用foreignObject绘制。但是有一些bug，就是在安卓机上emoji表情会错位，而且在ios上会被莫名截断。

3.使用html2canvas库。还是有一个bug，就是对英文的竖排支持不是很理想，英文会横排显示。

总之上面的方法各有各的优缺点，完美的竖排兼容emoji的实现是不可能的，这辈子都不可能的！0.0























