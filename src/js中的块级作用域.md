# js中的块级作用域

## 概述

函数是js中最常见的作用域单元, 声明在一个函数内部的变量或函数会在所处的作用域中**隐藏起来**, 这是有意为之的**非常好**的设计原则.

但是随着js的发展, 我们有了某个代码块(通常指{..}内部)**隐藏**变量或函数的需求, 这就是块级作用域的**由来**.

下面是不用es6**实现块级作用域**的三种方法, 供以后开发时参考, 相信对其他人也有用.

### IIFE

IIFE, 即**立即执行函数**, 用一个函数作用域(闭包)来模拟块级作用域.示例如下:

```
//es6中的块级作用域
{let a = 1;
 console.log(a);}
console.log(a); //ReferenceError

//IIFE实现
(function() {
    var a = 1;
    console.log(a);
})();
console.log(a); //Undefined
```

可以看到, 由于变量提升, a不再是未初始化, 而是未定义. 这是使用IIFE不好的一方面, 但还可以接受. 另一方面由于闭包拥有很多不好的特性, 比如this指向改变啊, return,break和continue发生变化啊, 内存泄漏问题啊等等, 所以IIFE并不是一个普适的方案.

### throw

从**es3**开始, js中就已经定义了一种块级作用域, 它就是**throw函数**. 示例如下:

```
//es6中的块级作用域
{let a = 1;
 console.log(a);}
console.log(a); //ReferenceError

//throw函数实现
{
    try {
        throw undefined;
    } catch(a) {
        a = 2;
        console.log(a);
    }
}
console.log(a); //ReferenceError
```

之前我在看别人的代码的时候觉得用throw好像很高级, 可能是为了实现块级作用域吧.

当有多个变量时, 我们一般不用a, 而是用err123456等.

### 优雅的方案

如果用**babel**编译一下如上代码, 会发现编译出来的es5与上面的方案都不同, 它是这么编译的.

```
//es6中的块级作用域1
{let a = 1;
 console.log(a);}
console.log(a); //ReferenceError

//babel编译1
"use strict";
{
  var _a = 1;
  console.log(_a);
}
console.log(a); //ReferenceError

//es6中的块级作用域2
{let a = 1;
 console.log(a);
 let _a = 2;
 console.log(_a);
}
console.log(a);
console.log(_a);

//babel编译2
"use strict";
{
  var _a2 = 1;
  console.log(_a2);
  var _a3 = 2;
  console.log(_a3);
}
console.log(a);
console.log(_a);
```

哈哈, 是不是很*优雅*? 虽然会在全局初始化_a等变量, 但是没有太大的问题.












