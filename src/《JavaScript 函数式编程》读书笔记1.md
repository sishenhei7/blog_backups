## 概述

这是我读[《javascript函数式编程》](https://book.douban.com/subject/26579320/)的读书笔记，供以后开发时参考，相信对其他人也有用。

说明：虽然本书是**基于underscore.js库写的**，但是其中的理念和思考方式都讲的很好，**值得一读**。不过如果不熟悉underscore.js库的话，读起来会有点困难。

### 从一段代码说起

由于parsInt函数有2个参数，所以下面的代码会得出**意想不到的结果**：

```
[3.5, 5.3, 9.8, 13.4, 16.9].map(parseInt);
//输出 [3, NaN, NaN, 1, 1]
```

原因是map里面的函数可以带有3个参数，第一个是值，第二个是index，第三个是map方法的调用者。

所以如果要对数组的每一个元素取整要怎么做呢？一个方法是把parseInt封装起来，实例如下：

```
const curry1 = func => (x) => func(x);

[3.5, 5.3, 9.8, 13.4, 16.9].map(curry1(parseInt));
//输出： [3, 5, 9, 13, 16]
```

这种以**函数为一等公民**的编程就是函数式编程。其中curry1接受一个函数参数，并且返回一个函数，这样的函数称为**高阶函数**。谷歌的闭包编译器能够极大的优化函数式编程的代码。

### 函数式编程

什么是函数式编程？
1. 一个对**“存在”**的抽象函数的定义。
2. 一个建立在存在函数之上的，对**“真”**的抽象函数的定义。
3. 通过其它函数来使用上面两个函数，以实现更多的行为。

```
//对“存在”的定义
function existy(x) { return x!= null; }

//对“真”的定义
function truthy(x) { return (x !== false) && existy(x); }
```

### 动态作用域

js中的**this引用的就是动态作用域**，在不同的函数中，被不同的对象调用，它所指向的对象不同。它是通过apply和call直接操作的，比如如下代码：

```
const banana = 'banana';

function returnThis() {
    return this;
}

returnThis.apply(banana); //输出'banana'

returnThis.call(banana); //输出'banana'
```

但是在事件中，常常会丢失this，所以我们用bind来绑定this到当前作用域。

```
const bindThis = returnThis.bind(banana);

bindThis(); //输出'banana'
```

值得注意的是，bind是**惰性**的，不像apply或者call那样会立即执行函数。另外，bind还可以用来**制造柯里化**：

```
function add(x, y) {
    return x + y;
}

const add3 = add.bind(null, 3);

add3(7); //输出10
```

### 函数作用域

函数作用域会隐式地把var声明移到函数的顶部，这样做的意义是，**函数内定义的变量对函数内任何一段代码都是可见的。**

接下来我们来展示，如何用this这个动态作用域来**模拟函数作用域**。技巧在于用call或者apply绑定函数的作用域。

```
function f() {
    this['a'] = 200;
    return this['a'] + this['b'];
}

var global = {'b': 2};

f.call({ ...global }); //输出202
global; //输出{'b': 2}
```

可以看到，我们复制了一份当前的作用域，并且把它传给了f函数，这样f函数可以调用外部作用域的变量，并且不会污染外部作用域。

这其实就是函数作用域的**深层机制**。

### 闭包

这个观点非常有趣：闭包背后的原理是，如果一个函数包含内部函数，那么它们都可以看到其中声明的变量，这些变量被称为“自由变量”。这些变量可以被内部函数**“捕获”**，然后从renturn中实现**“越狱”**，以供以后使用。

值得一提的是，利用闭包可以捕获的不仅仅是**变量**，**参数**甚至是**函数**都可以**被捕获**。

既然闭包捕获了变量，参数或者函数，那么如果在闭包捕获他们之后，他们的值发生了改变，闭包里面的捕获的值会跟着改变吗？我们来看看下面的代码：

```
function complement(func) {
    return function() {
        return !func.apply(null, arguments);
    }
}

function isEven(n) { return (n%2) === 0}

const isOdd = complement(isEven);

isOdd(23); //输出true
isOdd(24); //输出false

//改变isEven函数
function isEven(n) { return false}

//isOdd不变
isOdd(23); //输出true
isOdd(24); //输出false
```

上面这段代码表明，**捕获的函数不会改变**。如果捕获的是变量呢？

```
var a = 2;

function f(x) {
    return function() {
        return x;
    }
}

var g = f(a);
g(); //输出2

a = 5;
g(); //输出2


var b = {c:3};
var h = f(b);
h(); //输出{c:3}

b.c = 5;
h(); //输出{c:5}
```

从上面的代码可以看出，如果捕获的是基本类型变量，则不会改变；**如果捕获的是对象，那么会改变。**

思考一下原因。其实闭包捕获的是**函数作用域**，而根据上面函数作用域的讨论，函数作用域会把外部作用域和内部作用域的变量**浅复制**一遍，所以出现了上面的情况。

