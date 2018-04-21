# 用jQuery修改右键菜单

## 概述

以前在网上找过屏蔽右键菜单的代码，也找过屏蔽F12的代码，今天无意之中看到别人的右键菜单很有意思，我也想来搞一个。

### 思路

1. 建立一个菜单并且隐藏起来。
2. 用window.oncontextmenu屏蔽鼠标右键的默认事件。
3. 判断鼠标点击事件的类型，如果为右键则在把菜单显示并且移动到鼠标位置。
4. 点击鼠标左键时，隐藏菜单。

### 代码示例

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.js"></script>
    <style>
        li {
            cursor: pointer;
        }
        li:hover {
            text-decoration: underline;
        }
    </style>
</head>
<body style="position: relative;">
<div style="background-color: grey; width: 500px; height: 500px;"></div>
<ul class="right" style="display: none;position: absolute;border: 1px solid green">
    <li>喵喵！！</li>
    <li>不准偷看</li>
    <li>关于本站</li>
</ul>

<script type="text/javascript">
    window.oncontextmenu = function() { return false; };
    $('body').mousedown(function(event) {
        if (event.which === 3) {
            $('.right').css({ 'display': 'block', 'top': event.pageY + 'px', 'left': event.pageX + 'px' });
        }
    });
    $('body').click(function(event) {
        $('.right').css({ 'display': 'none' });
    });
</script>
</body>
</html>
```

### 其它

有一个小bug，就是当把那个长宽500px的div删掉时，就不会触发事件了，原因不明。

不是position的问题，因为我把这个div的长宽设置为1px后，在远离它的地方点击不会触发事件，在靠近它的地方点击则会触发事件。










































