# js中的内存泄漏

## 概述

之前虽然知道函数作用域, 上下文, 作用域链, 闭包, 引用清除与标记清除等概念, 但是总觉得既然有标记清除, js就不怎么会发生内存泄漏. 今天查了下资料, 梳理了一下, 记录下来供以后开发时参考, 相信对其他人也有用.

### 全局变量

```
function foo(arg) {
bar = "this is a hidden global variable";
}
```

如上代码, 声明了一个**全局变量**, 它不会被自动回收, 所以会造成内存泄漏. 需要手动回收, 改成如下所示:

```
方法一(推荐)
function foo(arg) {
var bar = "this is a hidden global variable";
}

方法二
function foo(arg) {
bar = "this is a hidden global variable";
// Do something;
bar = null;
}
```

### 计时器

```
var someResource = getData();
setInterval(function() {
    var node = document.getElementById('Node');
    if(node) {
    // Do stuff with node and someResource.
    node.innerHTML = JSON.stringify(someResource));
    }
}, 1000);
```

上面的**计时器**不断的执行函数, 在执行的时候会调用外面的变量someResource, 所以someResource一直在内存中无法被释放. 要修正这种内存泄漏只能设置一个计时器的停止条件了.

```
var someResource = getData();
setInterval(function() {
    var node = document.getElementById('Node');
    if(node) {
    // Do stuff with node and someResource.
    node.innerHTML = JSON.stringify(someResource));
    }
}, 1000);
if(timesRun === 60){
clearInterval(interval);
}
```

### dom相关操作

```
<div id="container">  
</div>

$('#container').bind('click', function(){
    console.log('click');
}).remove();
```

上面删除了dom但是**没有删除绑定的事件**, 也会造成内存泄漏. 解决方法是先把事件清除再remove.

```
<div id="container">  
</div>

$('#container').bind('click', function(){
    console.log('click');
}).off('click').remove();
```

### 闭包

```
var leaks = (function(){
    var leak = 'xxxxxx';// 被闭包所引用，不会被回收
    return function(){
        console.log(leak);
    }
})()
leaks();
```

上面的leak被**闭包**所引用, 所以不会被回收.解决方法是在执行操作之后释放掉内存.

```
var leaks = (function(){
    var leak = 'xxxxxx';// 被闭包所引用，不会被回收
    return function(){
        console.log(leak);
        leak = null;
    }
})()
leaks();
```

### 不被注意的内存泄漏

有一种内存泄漏很常见, 但是不是那么明显, 代码如下:

```
window.onload=function outerFunction(){
    var obj = document.getElementById("element");
    obj.onclick=function innerFunction(){};
};
```

这是一个很常见也用的较多的**事件绑定**代码, 它实际上是闭包引起的内存泄漏问题. 首先我们在内层函数中声明了一个变量obj, 然后我们给obj绑定了一个click事件, 这个click事件被外层window引用, 导致innerFunction()函数形成了一个闭包, 虽然这个函数里面没有任何表达式, 但是其中的this指向了obj, 所以obj在代码执行结束之后并不会被清除, 需要手动释放.修改方法如下:

```
方法一(推荐):
window.onload=function outerFunction(){
    var obj = document.getElementById("element");
    obj.onclick=function innerFunction(){};
    obj = null;
};

方法二(不推荐):
function innerFunction(){};
window.onload=function outerFunction(){
    var obj = document.getElementById("element");
    obj.onclick=innerFunction;
    obj = null;
};
```

*值得注意的是*, jQuery封装了解决这种内存泄漏的方法, 它的原理是使用jQuery缓存来绑定事件, 所以存下下面的方法三. 但是为了保险起见, 还是建议使用方法一手动清除声明的变量.

```
window.onload=function outerFunction(){
  var obj = document.getElementById("element");
  $(obj).click(function innerFunction(){});
};
```

### 查看内存泄漏

使用*chrome*能够很方便的查看网页的内存使用情况, 操作如下:

1. 打开开发者工具，选择 Network 右边的 Timeline 面板
2. 在顶部的Capture字段里面勾选 Memory
3. 点击左上角的录制按钮。
4. 在页面上进行各种操作，模拟用户的使用情况。
5. 一段时间后，点击对话框的 stop 按钮，面板上就会显示这段时间的内存占用情况。
