# js中的严格模式

## 概述

以前看过严格模式,但是没怎么用所以逐渐忘了,现在整理一下,把**我认为需要用到的**记录在下面,供以后开发时参考,相信对其他人也有用.

严格模式主要消除了js语法的不合理之处,保证代码安全,提高编译效率,为新版本的js做准备.

### 使用

在代码前面加上下面语句即可.一般使用方法是直接**在顶层**加下面的代码,或者用一个**无参数**闭包把所有的代码封装起来,然后在里面的第一行加下面的代码.

```
'use strict';
```

### 严格模式的一些特点

#### 报错更严格

1. 全局变量未声明会报错.
2. delete不能删除的属性会报错.
3. 同名的对象属性或函数参数会报错.
4. 8进制会报错(数字的第一个是0).
5. 给基本属性加属性会报错.

示例:

```
'use strict';
mistypeVariable = 17;
delete Object.prototype;
var o = { p: 1, p: 2 };
function sum(a, a, c) {};
var sum = 015;
(14).sailing = 'home';
```

#### 使js更安全

1. this在没有调用者的时候被赋值undefined,而不是window.(因为可以利用这个漏洞获取全局属性而造成安全问题)
2. 禁止在函数内部遍历调用栈

示例:

```
'use strict';
function fun() { return this; }
console.assert(fun() === undefined);

function restricted() {
  restricted.arguments;
}
```

#### 为后续版本做准备

1. 预留一些关键字:implements, interface, let, package, private, protected, public, static, 和 yield 等.
2. 不允许在块中声明函数.

```
'use strict';
function package(protected) {
  'use strict';
  var implements;
}

if (true) {
  function f() { }
  f();
}
```

### 其它

1. 在有参数的函数内部声明严格模式会报错.
2. 类和模块的内部，默认就是严格模式，所以不需要使用use strict指定运行模式。只要你的代码写在类或模块之中，就只有严格模式可用。
3. 考虑到未来所有的代码，其实都是运行在模块之中，所以 ES6 实际上把整个语言升级到了严格模式。



