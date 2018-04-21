# jScrollPane.js的使用

## 概述

jScrollPane.js是一个轻量级的滑块插件, 非常方便使用. 在前端工业界(写页面)使用非常广泛, 下面我记录下用法, 供以后开发时参考, 相信对其他人也有用.

PS: 想起之前我用impress.js写了一个PPT, 当时觉得有多了不起, 现在用的插件一多, 就觉得以前真是好笑, impress.js也就是一个一般的插件罢了...

### 不用jScrollPane.js

先来看看不用jScrollPane.js, 给一个wrap设定高度, 再给这个wrap加上内容, 内容的高度大于wrap的高度, 会发生什么?

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>demo</title>
    <style type="text/css">
    * {
        padding: 0;
        margin: 0;
    }
    .scroller {
        width: 240px;
        height: 140px;
        background-color: #666;
    }
    .app {
        width: 240px;
        height: 140px;
        background-color: green;
    }
    <style>
  </head>
  <body>
    <div class="scroller" style="">
        <p>我知道我是一个没车没房得人，颜值还是那么低，也不说话。</p>
        <p>我不敢靠近你，这就是理由</p>
        <p>等到我靠近你，才发现我，喜欢上你了。</p>
        <p> 等我想真正得追你得时候，才发现已经晚了</p>
        <p>不知道还有没有机会，成为你得另一半了。</p>
        <p>陪伴到老</p>
    </div>
    <div class="app"></div>
  </body>
</html>
```

<textarea id="text_test" rows="10" cols="100" style="display: none;">

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>demo</title>
    &lt;style type="text/css">
    * {
        padding: 0;
        margin: 0;
    }
    .scroller {
        width: 240px;
        height: 140px;
        background-color: #666;
    }
    .app {
        width: 240px;
        height: 140px;
        background-color: green;
    }
    &lt;/style>
  </head>
  <body>
    <div class="scroller" style="">
        <p>我知道我是一个没车没房得人，颜值还是那么低，也不说话。</p>
        <p>我不敢靠近你，这就是理由</p>
        <p>等到我靠近你，才发现我，喜欢上你了。</p>
        <p> 等我想真正得追你得时候，才发现已经晚了</p>
        <p>不知道还有没有机会，成为你得另一半了。</p>
        <p>陪伴到老</p>
    </div>
    <div class="app"></div>
  </body>
</html>
</textarea>

<input onclick="runCode('text_test')" value="运行测试一下" type="button">

可以看到, scroller元素里面的内容超出了它的范围并且伸到外面去了.

### 使用overflow

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>demo</title>
    <style type="text/css">
    * {
        padding: 0;
        margin: 0;
    }
    .scroller {
        width: 240px;
        height: 140px;
        background-color: #666;
        overflow: scroll;
    }
    .app {
        width: 240px;
        height: 140px;
        background-color: green;
    }
    <style>
  </head>
  <body>
    <div class="scroller" style="">
        <p>我知道我是一个没车没房得人，颜值还是那么低，也不说话。</p>
        <p>我不敢靠近你，这就是理由</p>
        <p>等到我靠近你，才发现我，喜欢上你了。</p>
        <p> 等我想真正得追你得时候，才发现已经晚了</p>
        <p>不知道还有没有机会，成为你得另一半了。</p>
        <p>陪伴到老</p>
    </div>
    <div class="app"></div>
  </body>
</html>
```

<textarea id="text_test2" rows="10" cols="100" style="display: none;">

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>demo</title>
    &lt;style type="text/css">
    * {
        padding: 0;
        margin: 0;
    }
    .scroller {
        width: 240px;
        height: 140px;
        background-color: #666;
        overflow: scroll;
    }
    .app {
        width: 240px;
        height: 140px;
        background-color: green;
    }
    &lt;/style>
  </head>
  <body>
    <div class="scroller" style="">
        <p>我知道我是一个没车没房得人，颜值还是那么低，也不说话。</p>
        <p>我不敢靠近你，这就是理由</p>
        <p>等到我靠近你，才发现我，喜欢上你了。</p>
        <p> 等我想真正得追你得时候，才发现已经晚了</p>
        <p>不知道还有没有机会，成为你得另一半了。</p>
        <p>陪伴到老</p>
    </div>
    <div class="app"></div>
  </body>
</html>
</textarea>

<input onclick="runCode('text_test2')" value="运行测试一下" type="button">

可以看到, 只用css就给scroller元素加了一个滚动条, 不过这个滚动条是系统自带的滚动条. 如果要自定义滚动条的话, 可以使用jScrollPane.js.(注意, 需要和jquery一起使用.)

### 使用jScrollPane.js

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>demo</title>
    <link href="https://cdn.bootcss.com/jScrollPane/2.1.3-rc.1/style/jquery.jscrollpane.css" rel="stylesheet">
    <style type="text/css">
    * {
        padding: 0;
        margin: 0;
    }
    .scroller {
        width: 240px;
        height: 140px;
        background-color: #666;
        overflow: scroll;
    }
    .app {
        width: 240px;
        height: 140px;
        background-color: green;
    }
    </style>
</head>
    <body>
    <div class="scroller" style="">
        <p>我知道我是一个没车没房得人，颜值还是那么低，也不说话。</p>
        <p>我不敢靠近你，这就是理由</p>
        <p>等到我靠近你，才发现我，喜欢上你了。</p>
        <p> 等我想真正得追你得时候，才发现已经晚了</p>
        <p>不知道还有没有机会，成为你得另一半了。</p>
        <p>陪伴到老</p>
    </div>
    <div class="app"></div>
    <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
    <script src="https://cdn.bootcss.com/jScrollPane/2.1.3-rc.1/script/jquery.jscrollpane.js"></script>
    <script type="text/javascript">
        $(function() {
            $('.scroller').jScrollPane();
        });
    </script>
    </body>
</html>
```

<textarea id="text_test3" rows="10" cols="100" style="display: none;">

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>demo</title>
    <link href="https://cdn.bootcss.com/jScrollPane/2.1.3-rc.1/style/jquery.jscrollpane.css" rel="stylesheet">
    &lt;style type="text/css">
    * {
        padding: 0;
        margin: 0;
    }
    .scroller {
        width: 240px;
        height: 140px;
        background-color: #666;
        overflow: scroll;
    }
    .app {
        width: 240px;
        height: 140px;
        background-color: green;
    }
    &lt;/style>
    &lt;script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js">&lt;/script>
    &lt;script src="https://cdn.bootcss.com/jScrollPane/2.1.3-rc.1/script/jquery.jscrollpane.js">&lt;/script>
    &lt;script type="text/javascript">
        $(function() {
            $('.scroller').jScrollPane();
        });
    &lt;/script>
</head>
    <body>
    <div class="scroller" style="">
        <p>我知道我是一个没车没房得人，颜值还是那么低，也不说话。</p>
        <p>我不敢靠近你，这就是理由</p>
        <p>等到我靠近你，才发现我，喜欢上你了。</p>
        <p> 等我想真正得追你得时候，才发现已经晚了</p>
        <p>不知道还有没有机会，成为你得另一半了。</p>
        <p>陪伴到老</p>
    </div>
    <div class="app"></div>
    </body>
</html>
</textarea>

<input onclick="runCode('text_test3')" value="运行测试一下" type="button">

可以看到,自定义了一个滚动条, jScrollPane还有许多其它的参数配置, 这些参数配置才是它真正的强大之处. 在此就不介绍了, 自行百度~






