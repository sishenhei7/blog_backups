# js中的柯里化

## 概述

今天查询事件绑定资料的时候偶然遇到了柯里化这个词，很感兴趣，于是记录下来供以后开发时参考，相信对其他人也有用。

### 定义

柯里化是**函数式编程**里面的术语，它是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

```
function add(first, second) {
    return (first + second)
}

//柯里化
let add1 = (num) => add(1, num)
```

所以，其实柯里化其实是箭头函数的一个**语法糖**，而箭头函数又是lambda函数的一个语法糖。

语法糖：用前端的话来说，就是polyfill，用低级的已经有的代码实现一个新的概念。

现在js里面的柯里化也不是非要确定几个参数了，只要意思相近即可。

### 简单实例

一个简单的例子如下：

```
for(var i = 0; i < 5; i++){
    setTimeout(function(){
        console.log(i);
        }, 1000);
}
```

因为js没有块级作用域，所以这段代码输出5个5。

如果要输出01234呢？一个方法是利用let，另一个方法是利用柯里化。

```
//柯里化
var cury = (i) => {
    var k = i;
    return setTimeout(function(){
        console.log(k);
        }, 1000)
}

for(var i = 0; i < 5; i++){
    cury(i);
}
```

### 现代实例

一个很现代的例子是react的事件绑定中的bind就是柯里化。

```
onClick={this.handlehistory.bind(this)}
```

它接受参数this，并返回一个函数，这个函数在被window调用的时候还是能够使用当初传进去的这个this。

另一个例子是redux里面的dispatch。（我是看别人介绍说的，redux我还没学，就不写了）
