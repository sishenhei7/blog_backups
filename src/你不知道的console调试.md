# 你不知道的console调试

## 概述

浏览器的开发者工具我们经常用，console.log我们也经常用，但是console还有其它一些方便调试的命令，我总结了几个常用的记录在下面，供以后开发时参考，相信对其他人也有用。

### 获取js执行时间

以前我经常用下面的命令获取程序执行时间：

```
var start = new Date();
//执行js
var end = new Date();
console.log('运行时间: ' + (end - start));
```

但是感觉总有点不准或者不精确，其实可以用console.time()来获取精确的程序执行时间：

```
console.time('执行时间');
//执行js
console.timeEnd('执行时间');
```

### $命令

jQuery里面的```$```可以很方便的让我们取得html中的Dom元素。其实有一个好消息，console原生支持```$```，也就是说你可以在console里面直接执行下面的代码。(只不过返回的是dom节点而不是jquery对象)

```
$('body')
$('.title')
$('#haha')
```

### console.table()

有时我们用$('.class')选取的节点有很多，如果用console.log()输出的话，会输出一个矩阵，不太易读。这个时候可以用console.table()来进行表格化输出。

### console.profile()

console.profile()的用法和console.time()的用法有些类似，不过它生成了一个profile，里面有运行时间的各种信息。这个profile在开发者工具的profile栏里面，可能有点不太好找，有的浏览器需要从开发者工具右上角的点里面找到profile。

这个命令可以精确的反映每个函数的执行时间，对于页面性能优化有很大的参考价值。

### $_命令

$_会返回最近一次表达式执行的结果，而```$0,$1,$2,$3,$4```则表示最近5个选择过的dom节点。通过点击审查元素，然后在Elements里面选择dom节点，可以用$0引用到console里面去。

### copy()

copy()能把里面的东西复制进剪切板里面去。我经常就用copy(document.body)来把body节点的代码复制到剪切板里去。(虽然查看源代码可以达到同样的效果，但是操作有点麻烦。)







