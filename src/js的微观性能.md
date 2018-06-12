## 概述

js的**微观性能**是指js的某一个表达式或者某一行或者某一块代码的性能。几天前和同事讨论过这方面的内容，今天**深入研究了一下**，记录下来，供以后开发时参考，相信对其他人也有用。

### 从一段代码说起

记得以前看关于js的书的时候，书里面不断的强调，在对数组进行循环的时候，要**预先缓存**数组的长度，不然每次循环都会去拿数组的长度，非常耗时间。比如下面这段代码：

```
//缓存长度(书上面推荐)
for(var i=0,j=arr.length; i<j; i++){
    //do something
}

//没有缓存长度
for(var i=0; i<arr.length; i++){
    //do something
}
```

但是有一篇文章：[How the Grinch stole array.length access](https://mrale.ph/blog/2014/12/24/array-length-caching.html)提到一个惊人的结果，**没有缓存长度的代码会比缓存长度的代码更快。**

原因是：**js引擎**会对这类代码进行**启发式的优化**，它会**自动缓存**数组的长度！所以由于缓存长度的代码会新建一个j变量，这个会带来额外的耗时，所以它执行起来会慢一些。

然后我用**三种方法**对这段代码进行性能测试。

### console.time

代码如下：

```
//没缓存数组长度
var arr2 = Array(10000).fill(0);
console.time('没缓存');
for(var i2=0; i2<arr2.length; i2++){}
console.timeEnd('没缓存');

//缓存了数组长度
var arr1 = Array(10000).fill(0);
console.time('缓存了');
for(var i1=0,j1=arr1.length; i1<j1; i1++){}
console.timeEnd('缓存了');
```

经过多次测试，得出的结果是：
1. 没缓存数组长度的代码执行时间大约是0.15ms
2. 缓存了数组长度的代码执行时间大约是0.11ms

### benchmark.js

可以参考[benchmark.js官网](https://benchmarkjs.com/)。代码如下：

```
var Benchmark = require('benchmark');
var suite = new Benchmark.Suite;

// add tests
suite.add('has not cached', function() {
  var arr2 = Array(100).fill(0);
  for(var i2=0; i2<arr2.length; i2++){}
})
.add('has cached', function() {
  var arr1 = Array(100).fill(0);
  for(var i1=0,j1=arr1.length; i1<j1; i1++){}
})
// add listeners
.on('cycle', function(event) {
  console.log(String(event.target));
})
.on('complete', function() {
  console.log('Fastest is ' + this.filter('fastest').map('name'));
})
// run async
.run({ 'async': true });
```

得出的结果是（ops/sec越大越快）：
1. 没缓存数组长度的代码的执行速度是3834515 ops/sec。
2. 缓存了数组长度的代码的执行速度是3668608 ops/sec。

但是当我调整顺序：

```
var Benchmark = require('benchmark');
var suite = new Benchmark.Suite;

// add tests
suite.add('has not cached', function() {
    var arr1 = Array(100).fill(0);
    for(var i1=0,j1=arr1.length; i1<j1; i1++){}
})
.add('has cached', function() {
    var arr2 = Array(100).fill(0);
    for(var i2=0; i2<arr2.length; i2++){}
})
// add listeners
.on('cycle', function(event) {
  console.log(String(event.target));
})
.on('complete', function() {
  console.log('Fastest is ' + this.filter('fastest').map('name'));
})
// run async
.run({ 'async': true });
```

却得出了相反的结果是：
1. 没缓存数组长度的代码的执行速度是3433692 ops/sec。
2. 缓存了数组长度的代码的执行速度是3737960 ops/sec。

### jsPerf

jsPerf的链接：[https://jsperf.com/](https://jsperf.com/)

我进行了2个测试，[第一个](https://jsperf.com/sishenhei7testjs0)和[第二个](https://jsperf.com/sishenhei7testjs00)也是得出了完全相反的结论。

### 总结

我们看一下上面的三个测试，第一个好像令人信服，但是不能肯定。第二和第三个完全得不出结论，让人感到非常困惑。（所以建议一般都用第一种方法？？？）

通过这三个测试，我**觉得自己好像踩了坑，但是不知道坑在哪里**。

通过读[《你不知道的JavaScript（中卷）》](https://book.douban.com/subject/26854244/)，我总结了一下微观性能测试会遇到的坑：
1. js引擎的启发式优化。
2. 不同的环境结果不一样。
3. 注意测试的精度。（这就是为什么不用new Date()的原因）
4. 测试环境运行其它隐藏程序
5. 不必要的噪音。（创建新的变量？新的匿名函数？）
6. 额外的工作。（强制类型转换）
7. 测试不全面。（number? string?）
8. 不是所有的引擎都类似。

所以要想写好一个好的微观测试用例真的不是那么简单。

然而，计算机界有一句名言：**过早优化是万恶之源**。意思就是非关键路径的优化是万恶之源。特别对于微观性能来说，如果不是在特别大的循环里面，或者不太常执行的代码，这些微观性能的优化根本没多少用，反而会把我们慢慢的带向无尽的深渊，就像上面我做的三个测试那样，总感觉有坑，但是找不出坑在哪里。

正确的做法应该是**先找出影响性能的几个关键瓶颈，然后再针对它们进行优化，这样我们就能保证，一旦优化成功，性能将会上升很大。**





