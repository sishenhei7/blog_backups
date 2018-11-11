## 概述

我对使**用js控制下载**非常感兴趣，在网上查资料的时候碰巧看到了相关实现方法，记录下来供以后开发时参考，相信对其他人也有用。

参考资料：

[JS前端创建html或json文件并浏览器导出下载](https://www.zhangxinxu.com/wordpress/2017/07/js-text-string-download-as-html-json-file/)

[理解DOMString、Document、FormData、Blob、File、ArrayBuffer数据类型](https://www.zhangxinxu.com/wordpress/2013/10/understand-domstring-document-formdata-blob-file-arraybuffer/)

### 实现方法

一种实现方法是利用H5中的**download属性**。如果给a标签加了这个属性的话，点击a标签不会跳到链接或者打开图片，而是会直接下载资源。示例如下：

```
<a href="large.jpg" download>下载</a>
```

注意：这个属性的**兼容性很差**，貌似不兼容safara，并且仅适用于同源URL。

于是我们的实现方法是，在用户点击的时候，**给html添加一个拥有href和download属性的a标签，然后用js对a标签进行点击**，然后就可以自动下载了。相关代码如下：

```
var link = document.createElement('a');
//设置下载的文件名
link.download = filename;
link.style.display = 'none';
//设置下载路径
link.href = src;
//触发点击
document.body.appendChild(link);
link.click();
//移除节点
document.body.removeChild(link);
```

然后怎么来获得这个下载路径src呢？下面介绍2种方法。

### 利用canvas转base64下载图片

我们都知道，利用**canvas**可以把画布转化为一个base64的图片，这个base64代码就是图片源。示例如下：

```
var canvas = document.createElement('canvas');
var context = canvas.getContext('2d');
//这里在canvas上面进行一些操作

//这里导出src，然后把这里的src赋给上面的src即可
var src = context.toDataURL('image/jpeg');
```

### 利用Blob对象下载文本

我们可以把文本用**Blob对象**转化为二进制，然后利用上面的方法下载。示例如下：

```
//content是文本或字符串内容
var blob = new Blob([content]);

//这里导出src，然后把这里的src赋给上面的src即可
var src = URL.createObjectURL(blob);
```

对于创建blob对象，有下面几个示例：

```
//创建字符串对象
var blob1 = new Blob([JSON.stringify(obj)]);

//创建一个DOMString对象
var s = '<div>Hello World!!</div>'
var blob2 = new Blob([s], {type: 'text/xml'});

//创建一个ArrayBuffer对象
var abf = new ArrayBuffer(8);
var blob3 = new Blob([abf], {type: 'text/plain'});
```

另外canvas有一个**toBlob**的api，使用示例如下：

```
var canvas = document.getElementById('canvas');

canvas.toBlob(function(blob) {
  var newImg = document.createElement('img'),
      url = URL.createObjectURL(blob);

  newImg.onload = function() {
    // no longer need to read the blob so it's revoked
    URL.revokeObjectURL(url);
  };

  newImg.src = url;
  document.body.appendChild(newImg);
});
```
























