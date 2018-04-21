# 理解js中的函数调用和this

## 概述

这是我看typescript的时候看引用资源看到的，原文在这里：[Understanding JavaScript Function Invocation and "this"](http://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)，我简单地总结一下记下来供以后开发时参考，相信对其他人也有用。

### 机制

js中的函数**调用机制**是这样的：
1. 建立一个表argList，从索引1开始塞入函数的参数。
2. 表的索引0的值时thisValue。
3. 把this赋给thisValue，然后调用func.call(argList)。

说这么多，其实就是想说明，函数调用f(x,y)其实就是f.call(this, x, y)的语法糖。内部是通过f.call(this, x, y)来调用的。

可以看到，虽然f(x,y)里面没有this，但是f.call(this, x, y)里面有this，所以非常好理解为什么函数调用中会**有this**了。

那么this是从哪里来的呢？当f(x,y)没有调用者的时候，this**自动被赋值**为window；当f(x,y)有调用者的时候，this被赋值为指向调用者。

### 例子

举几个**实例**感受一下：

```
//例子1
function hello(thing) {
  console.log(this + " says hello " + thing);
}
hello.call("Yehuda", "world"); //输出Yehuda says hello world

//例子2
function hello(thing) {
  console.log(this + " says hello " + thing);
}
//相当于hello.call(window, "world");
hello("world"); // 输出[object Window] says hello world

//例子3
function hello(thing) {
  console.log(this + " says hello " + thing);
}
let person = { name: "Brendan Eich" };
person.hello = hello;
//相当于hello.call(person, "world");
person.hello("world"); //输出[object Object] says hello world
```

**注意：**我们这里不是strict mode。

### 更优雅的写法

有时候，我们希望函数没有调用者，但是this却不指向window对象。有以下2种**优雅的**解决方案：

#### 箭头函数

es6里面定义了箭头函数，不只是为了简化函数的写法，而且还有这么一条**有用的规定**：箭头函数的this不会随调用者的不同而变化，它的this**永远**是被定义的函数域中的this。

```
//不用箭头函数
let person = {
    hello: function(thing) {
        return function() {
            console.log(this + " says hello " + thing);
        }
    }
}
let helloTest = person.hello('world');
helloTest(); //输出[object Window] says hello world


//使用箭头函数
let person = {
    hello: function(thing) {
        return () => {
            console.log(this + " says hello " + thing);
        }
    }
}
let helloTest = person.hello('world');
helloTest(); //输出[object Object] says hello world
```

需要注意的是，需要用一个function()把箭头函数包裹起来，因为如果不这样的话，它被定义的**函数域**是window。

#### bind

用箭头函数有点麻烦，我们可以这么写一个bind函数达到效果。

```
let person = {
    hello: function(thing) {
        console.log(this + " says hello " + thing);
    }
}

let bind = function(func, thisValue) {
    return function() {
        return func.apply(thisValue, arguments)
    }
}

let boundHello = bind(person.hello, person);
boundHello('world'); //输出[object Object] says hello world
```

**es5**给所有Function**封装了**上面的bind方法，所以我们只需要这么写：

```
let person = {
    hello: function(thing) {
        console.log(this + " says hello " + thing);
    }
}

let boundHello = person.hello.bind(person);
boundHello('world'); //输出[object Object] says hello world
```

这就是bing()方法的*来历*0.0
