# vue中的双向绑定

## 概述

今天对双向绑定感兴趣了,于是去查了下相关文章,发现有用**脏检查**的(angular.js),有用**发布者-订阅者模式**的(JQuery),也有用Object.defineProperty的(vue),其中用**Object.defineProperty**的(vue)特别简单,今天顺便记录下供以后开发时参考,相信对其他人也有用.

我参考了这篇文章:[Vue.js双向绑定的实现原理](https://www.cnblogs.com/kidney/p/6052935.html).

### 类似双向绑定的效果

其实用**事件代理**就可以实现类似双向绑定的效果,原理是当检测到数据改动时会触发一个**keyup**事件或者表单的**change**事件,通过监听这个事件做出响应,对应改变dom的内容.

代码如下:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
</head>
<body>
<input type="text" id="a">
<span id="b"></span>

<script>
  var b = document.getElementById('b');

  document.addEventListener('keyup', function (e) {
    b.innerText = e.target.value;
  });
</script>
</body>
</html>
```

通过在输入框里面输入内容,内容会在右边同步显示或改变.

**需要注意的是,react其实是一种单向数据流,那么怎么用react实现双向绑定呢?就是用的这个原理!**

可以点击下面的按钮体会一下(在输入框里面输入内容,右边会即时更新):

<textarea id="text_test" rows="10" cols="100" style="display: none">
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
</head>
<body>
<input type="text" id="a">
<span id="b"></span>

&lt;script>
  var b = document.getElementById('b');

  document.addEventListener('keyup', function (e) {
    b.innerText = e.target.value;
  });
&lt;/script>
</body>
</html>
</textarea>

<input onclick="runCode('text_test')" value="运行测试一下" type="button">

### vue的双向绑定

但是所谓双向绑定,所谓MVC,所谓MVVM,都强调的是数据的改变,数据(model)即是MVC里面的M,所以我们在双向绑定中必须有**数据(model)**.怎么加进去呢?

原理就是**getter和setter函数**,重写setter函数,使数据改变的同时进行一些其它的操作(改变视图),在视图改变的时候触发事件改写数据.

而怎么把数据和setter结合在一起呢?那就是利用**Object.defineProperty**方法,给对象定义一个属性(数据),同时重写setter方法.

代码如下:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
</head>
<body>
<input type="text" id="a">
<span id="b"></span>

<script>
  var obj = {};
  Object.defineProperty(obj, 'hello', {
    set: function (newVal) {
      document.getElementById('a').value = newVal;
      document.getElementById('b').innerText = newVal;
    }
  });

  document.addEventListener('keyup', function (e) {
    obj.hello = e.target.value;
  });
</script>
</body>
</html>
```

可以点击下面的按钮体会一下(在输入框里面输入内容,右边会即时更新):

<textarea id="text_test" rows="10" cols="100" style="display: none">
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Document</title>
</head>
<body>
<input type="text" id="a">
<span id="b"></span>

&lt;script>
  var obj = {};
  Object.defineProperty(obj, 'hello', {
    set: function (newVal) {
      document.getElementById('a').value = newVal;
      document.getElementById('b').innerText = newVal;
    }
  });

  document.addEventListener('keyup', function (e) {
    obj.hello = e.target.value;
  });
&lt;/script>
</body>
</html>
</textarea>

<input onclick="runCode('text_test')" value="运行测试一下" type="button">

