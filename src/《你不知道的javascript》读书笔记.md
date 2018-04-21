# 《你不知道的javascript》读书笔记

## 概述

放假读完了[《你不知道的javascript》上篇](https://book.douban.com/subject/26351021/?channel=subject_list&platform=web)，学到了很多东西，记录下来，供以后开发时参考，相信对其他人也有用。

### js的工作原理

- 引擎：从头到尾负责整个js的编译和运行。（很大一部分是查找操作，因此比如二分查找等查找方法才这么重要。）
- 编译器：负责语法分析和代码生成。
- 作用域：收集所有声明的变量，并且确认当前代码对这些变量的访问权限。

LHS查询和RHS查询：
- LHS查询：当变量出现在赋值操作左边时，会发生LHS查询，如果LHS查询不到，那么会新建一个变量。严格模式下，如果这个变量是全局变量，就会报ReferenceError。
- RHS查询：当变量出现在赋值操作右边时，会发生RHS查询，如果RHS查询不到，那么会报ReferenceError错误。

TypeError和Undefined:
- TypeError：当RHS查询成功，但是对变量进行不合理的操作时，就会报TypeError错误，意思是作用域判别成功了，但是操作不合法。
- Undefined：当RHS查询成功，但是变量是在LHS查询中自动新建的，并没有被赋值，就会报Undefined错误，意思是没有初始化。

```
//下面这段代码使用了3处LHS查询和4处RHS查询
function foo(a) {
    var b = a;
    return a + b;
}
var c = foo( 2 );
```

### 欺骗词法

js中使用的作用域是词法作用域，意思是变量和块的作用域是由你把它们写在代码里的位置决定的。还有一种是动态作用域，意思是作用域是程序运行的时候动态决定的，比如Bash脚本，Perl等。下面的代码在词法作用域中会输出2，在动态作用域中会输出3。

```
function foo() {
    console.log( a );
}
function bar() {
    var a = 3;
    foo();
}
var a = 2;
bar();
```

有2种方法欺骗词法作用域，一个是eval，另一个是with，这也是它们被设计出来的目的。需要注意的是，欺骗词法作用域会导致性能下降，因为当编译器遇到它们的时候，会放弃提前设定好他们的作用域，而是需要在运行的时候由引擎来动态推测它们的作用域。

 eval()接受一个字符串，这个字符串是一段代码，执行的时候，这段代码中的变量定义会修改当前eval()函数所在的作用域。在严格模式下，eval()函数有自己的作用域，里面的代码不能修改eval()函数所在的作用域。

```
//修改foo函数中的作用域，使b=3
function foo(str, a) {
    eval( str ); // 欺骗！
    console.log( a, b );
}
var b = 2;
foo( "var b = 3;", 1 ); // 1, 3

//严格模式下，eval()函数有自己的作用域
function foo(str) {
    "use strict";
    eval( str );
    console.log( a ); // ReferenceError: a is not defined
}
foo( "var a = 2" );
```

with可以把一个对象处理为单独的完全隔离的作用域，它的本意是被当做重复引用同一个对象中的多个属性的快捷方式，但是由于LHS查询，如果对象中没有这个属性的时候，会在全局中创建一个这个属性。在严格模式下，with被完全禁止使用。

```
function foo(obj) {
    with (obj) {
        a = 2;
    }
}
var o1 = {
    a: 3
};
var o2 = {
    b: 3
};
foo( o1 );
console.log( o1.a ); // 2
foo( o2 );
console.log( o2.a ); // undefined
console.log( a ); // 2——不好，a 被泄漏到全局作用域上了！
```

### 匿名函数表达式

匿名函数表达式是一个没有名称标识符的函数表达式，比如下面的：

```
setTimeout( function() {
    console.log("I waited 1 second!");
}, 1000 );
```

匿名函数表达式有很多缺点：
1. 匿名表达式不会在栈追踪中显示出有意义的函数名，使得调试很困难。
2. 由于没有函数名，所以当想要引用自身的时候只能用arguments.callee，而这又会倒置很多问题。
3. 匿名函数影响了可读性。一个描述性的名称，可以让代码梗易读。

所以最好始终给函数表达式命名。上面的代码可以改成如下所示：

```
setTimeout( function timeoutHandler() { // <-- 快看，我有名字了！
    console.log( "I waited 1 second!" );
}, 1000 );
```

### IIFE

之前我在博文中说明过IIFE，所以这里只补充一个IIFE的其它用途，就是传入一些特殊的值。

```
//传入undefined
undefined = true; // 给其他代码挖了一个大坑！绝对不要这样做！
(function IIFE( undefined ) {
    var a;
    if (a === undefined) {
        console.log( "Undefined is safe here!" );
    }
})();

//传入this
(function IIFE( this ) {
    console.log( this.a );
}
})(this);
```

### 显式的块作用域

有时候，可以把一段代码显式地用块包起来，这样写能够更易读，也更容易释放内存。如下所示：

```
function process(data) {
    // 在这里做点有趣的事情
}
// 在这个块中定义的内容可以销毁了！
{
    let someReallyBigData = { .. };
    process( someReallyBigData );
}
var btn = document.getElementById( "my_button" );
btn.addEventListener( "click", function click(evt){
    console.log("button clicked");
}, /*capturingPhase=*/false );
```

### 代码缺陷

```
for (var i=1; i<=5; i++) {
    setTimeout( function timer() {
        console.log( i );
    }, i*1000 );
}
```


上面的例子是一个很常见的前端面试题。我们来深入研究一下。

首先是写出这段代码我们期望什么？我们期望每一个循环中都有一个当前i的副本被绑定到setTimeout函数里面，所以当setTimeout函数执行的时候，会输出不同的i值。

但是事实并不是这样的，一个原因是setTimeout函数是异步的，另一个原因是所有的setTimeout函数所在的作用域都是全局作用域，这个全局作用域中只有一个i(只有函数作用域的代码缺陷)。

所以解决方法是给每一个setTimeout函数创建一个独自的作用域，可以用闭包创建函数作用域，也可以用let创建块作用域。

### 现代的模块机制

现代的模块机制有AMD模块机制和CMD模块机制。前者是在模块执行之前加载依赖模块，后者是在模块执行的时候动态加载依赖模块。

下面是AMD模块机制的模块加载器。需要注意的是```deps[i] = modules[deps[i]];```作用是加载依赖模块，```modules[name] = impl.apply( impl, deps );```作用是加载模块impl。

```
//通用的模块加载器
var MyModules = (function Manager() {
    var modules = {};
    function define(name, deps, impl) {
        for (var i=0; i<deps.length; i++) {
            deps[i] = modules[deps[i]];
        }
        modules[name] = impl.apply( impl, deps );
    }
    function get(name) {
        return modules[name];
    }
    return {
        define: define,
        get: get
    };
})();
```

为什么```modules[name] = impl.apply( impl, deps );```不写成```modules[name] = impl( deps );```？为什么要给自己传一个自己的this指针进去？原因是如果不传进去的话，impl( deps )中的this会指向全局作用域！

### 未来的模块机制

最新的es6的模块机制是这样的：

```
bar.js
function hello(who) {
    return "Let me introduce: " + who;
}
export hello;
foo.js
// 从 "bar" 模块导入 hello()
import hello from "bar";
var hungry = "hippo";
function awesome() {
    console.log(
        hello( hungry ).toUpperCase()
    );
}
export awesome;
```

### 闭包

看完本书之后感觉自己对闭包的理解还是不够深刻。闭包真正的理解是：当函数在当前作用域之外执行的时候，它仍然能够访问自己原本所在的作用域，这个时候就出现了闭包。

在哪些地方用到了闭包？闭包在不污染全局变量，定义模块和立即执行函数方面有很多运用，特别要注意的是，*所有异步操作中的回调函数都使用了闭包*。比如定时器，事件监听器，Ajax请求，跨窗口通信，WebWorkers等。因为在异步编程中，回调函数一般是在代码执行完毕之后再执行的，这个时候怎么记住回调函数里面的各种参数(即回调函数的作用域)？当然是用闭包啦。

另一点需要注意的是，*回调函数会丢失this*。比如下面的代码：

```
function foo() {
    console.log( this.a );
}
var obj = {
    a: 2,
    foo: foo
};
var a = "oops, global"; // a 是全局对象的属性
setTimeout( obj.foo, 100 ); // "oops, global"
```

虽然foo函数执行的时候，前面有一个obj，但是foo里面的this指向的却是全局对象，原因是setTimeout()函数的伪代码其实是如下所示的，它执行了这个操作```fn=obj.foo;fn()```。所以实际调用的是fn()函数。(同时也可以很明显的看出，foo函数并没有在它定义的那个作用域执行，而是跑到了setTimeout的作用域，所以出现了闭包。)

```
function setTimeout(fn,delay) {
// 等待 delay 毫秒
fn(); // <-- 调用位置！
}
```

### this

this设计的初衷是提供一种更优雅的方式来隐式“传递”一个对象引用，因此可以将API设计的更加简洁并且易于复用。

判断this的指向：
1. 函数是否在 new 中调用（ new 绑定）？如果是的话 this 绑定的是新创建的对象。```var bar = new foo()```
2. 函数是否通过 call 、 apply （显式绑定）或者硬绑定调用？如果是的话， this 绑定的是
指定的对象。```var bar = foo.call(obj2)```
3. 函数是否在某个上下文对象中调用（隐式绑定）？如果是的话， this 绑定的是那个上
下文对象。```var bar = obj1.foo()```
4. 如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到 undefined ，否则绑定到
全局对象。```var bar = foo()```

值得说明的是，箭头函数并没有使用上面的规则，而是根据外层的作用域来决定this。所以箭头函数常用于回调函数中(因为回调函数丢失了this，会造成很多错误)。

另外Function.prototype.bind()函数使用了上面的规则，只不过强制把this绑定到定义的作用域上面。它与箭头函数有着本质的不同。

### 使用apply展开数组

下面的例子是使用apply把数组展开为参数。es6中可以用...展开数组。

```
function foo(a,b) {
    console.log( "a:" + a + ", b:" + b );
}
// 把数组“展开”成参数
foo.apply( null, [2, 3] ); // a:2, b:3
// 使用 bind(..) 进行柯里化
var bar = foo.bind( null, 2 );
bar( 3 ); // a:2, b:3
```

### 更安全的this

一个非常安全的做法是把this绑定到一个不会对程序造成任何影响的空对象上面，而Object.create(null)和{}很像，但是并不会创建Object.
prototype这个委托，所以它比{}“更空”，所以一般把this绑定到Object.create(null)对象上面。不过es6规定的严格模式对这种情况有缓解。

```
function foo(a,b) {
    console.log( "a:" + a + ", b:" + b );
}
// 我们的 DMZ 空对象
var ø = Object.create( null );
// 把数组展开成参数
foo.apply( ø, [2, 3] ); // a:2, b:3
// 使用 bind(..) 进行柯里化
var bar = foo.bind( ø, 2 );
bar( 3 ); // a:2, b:3
```

### 软绑定

这里介绍一种软绑定，只把代码放在下面，代码我还没有看懂。。。。

```
//软绑定函数softBind
if (!Function.prototype.softBind) {
    Function.prototype.softBind = function(obj) {
        var fn = this;
        // 捕获所有 curried 参数
        var curried = [].slice.call( arguments, 1 );
        var bound = function() {
            return fn.apply(
                (!this || this === (window || global)) ?
                obj : this
                curried.concat.apply( curried, arguments )
            );
        };
    bound.prototype = Object.create( fn.prototype );
    return bound;
    };
}

//软绑定例子
function foo() {
    console.log("name: " + this.name);
}
var obj = { name: "obj" },
obj2 = { name: "obj2" },
obj3 = { name: "obj3" };
var fooOBJ = foo.softBind( obj );
fooOBJ(); // name: obj
obj2.foo = foo.softBind(obj);
obj2.foo(); // name: obj2 <---- 看！！！
fooOBJ.call( obj3 ); // name: obj3 <---- 看！
setTimeout( obj2.foo, 10 );
// name: obj <---- 应用了软绑定
```

### 对象的误区

经常可以在js中听到一句话，**万物皆对象，其实在某种意义上来说，这句话是错的。**因为js中还有很多对象的子类型，比如函数，数组，内置对象等，他们除了有对象的性质之外，还具有一些特别的行为，严格说来，他们不等于对象。

在其它语言中，属于对象的函数通常被称为方法。因此我们经常把对象里面的函数称为方法。但是严格说来，对象里面的**函数并不属于这个对象**，对象中的这个函数在很多情况下都只是个引用而已。

### 怎么复制对象

复制对象有深复制和浅复制。浅复制非常简单，es6定义了如下方法来复制一个对象。

```
var newObj = Object.assign({}, myObject);
```

另外，对于JSON安全的对象来说，有下面这种巧妙的复制方法。**JSON安全**指的是被序列化为一个JSON字符串后再解析回来的对象和原对象完全一样。

```
var newObj = JSON.parse(JSON.stringify(someObj));
```

### 对象遍历

1. 可以用for..in来遍历对象的可枚举属性列表。
2. 可以用map(),some(),every()来遍历数组。
3. es6中定义了for..of来遍历数组的值。

### 在js中实现类

我们引入类这种**设计模式**，就是为了简化代码的书写。类这种设计模式通过下面三种方式简化代码书写：
1. **混入**。把一个类的属性和方法直接扔到另一个类里面去，来复制前一个类的代码。
2. **继承**。使一个子类继承它的父类，让子类复制父类的所有代码。
3. **实例**。通过创建一个实例，让实例复制类的方法。

由于js中最基本的是对象，所以首先来看怎么用对象来*"实现"*一个类，即给对象实现上面的三种代码复制方式。

在对象之间实现混入其实很简单，就是对象的复制。代码如下：

```
// 非常简单的 mixin(..) 例子 :
function mixin( sourceObj, targetObj ) {
    for(key in sourceObj) {
        if(!(key in targetObj)) {
            targetObj[key] = sourceObj;
        }
    }
}
var Vehicle = {
    engines: 1,
    ignition: function() {
        console.log( "Turning on my engine." );
    },
    drive: function() {
        this.ignition();
        console.log( "Steering and moving forward!" );
    }
};
var Car = mixin( Vehicle, {
    wheels: 4,
    drive: function() {
        Vehicle.drive.call( this );
        console.log(
            "Rolling on all " + this.wheels + " wheels!"
        );
    }
} );
```

接下来是继承，继承其实还好，和混入差不多，但是第三种实例呢？怎么通过对象来生成一个实例？由于对象本来就是js中最小的单位，那还怎么生成更小的单位？

所以我们考虑复杂一点的对象，比如函数，然后函数实例化后就可以得到一个对象了。

那么对于函数，怎么实现第一种方法**混入**呢？很简单，用call()函数，代码如下：

```
function Vehicle() {
    this.engines = 1;
    //实例方法
    this.ignition = function() {
        console.log( "Turning on my engine." );
    }
}
//原型方法
Vehicle.prototype.drive = function() {
    this.ignition();
    console.log( "Steering and moving forward!" );
};

function Car() {
    //混入Vehicle的属性和实例方法
    Vehicle.call(this);
    //自己的属性
    this.engines = 2;
    //混入Vehicle的原型方法
    for(key in Vehicle.prototype) {
        Car.prototype[key]=Vehicle.prototype[key];
    }
    //然后可以定义自己的方法
}
```

然后是第二种方法**继承**，继承和混入差不多，可以用上面的来实现，也可以用实例来实现，书上有很多方法，我就不写了。

最后是第三种方法**实例**。用new关键字可以给函数构造一个对象，我们可以叫它为实例，因为这个对象复制了构造函数的代码。代码如下：

```
var car = new Car();
```

这样就完成了！我们用js中的函数对象**实现了一个类**。看起来很美好，但是用起来不是那么的舒服。原因是js中对于对象的复制并不是复制代码，而是复制引用！

也就是说，在用函数对象实现一个类的过程中，混入和继承实际上并没有复制代码，而是在复制引用！这就是js糟糕的继承方式，也是实现类最大的痛点。

值得注意的是，由于第三种方法实例是在复制实例属性和方法的代码(虽然也是在引用原型方法的代码)，所以有些人用第三种方法实例来实现第二种方法继承，**这就是各种继承方法的本质由来**。而这些带有技巧性并且难读的复杂方法，都或多或少的带有另一些痛点。

最后es6推出了class关键字来定义一个类，并规范化了混入，继承和实例，对很多方法进行了一些官方的封装。不得不说这个改变使得类在js中方便书写了很多。

### 原型

函数有一条特殊的性质，那就是可以通过new来获得一个对象。为了实现共享的方法，在获得这个对象的同时，会有一个不可枚举的属性```[[Prototype]]```被附加到了这个对象中，这个属性就是我们所说的**原型**。这个属性被关联到了构造函数的prototype属性。这样，所有通过new的子对象都可以访问构造函数的prototype中的同一个方法。值得注意的是，这里并不是复制，而是引用。

```
function Foo() {
    // ...
}
var a = new Foo();
Object.getPrototypeOf( a ) === Foo.prototype; // true
```

而构造函数的prototype属性是怎么来的呢？答案是，这个属性是所有的函数自带的属性。

值得注意的是，对象的原型的作用不仅仅是为了访问构造函数的共享方法，它也是对象之间**互相访问**的桥梁。

那么这个```[[Prototype]]```到底是什么？它其实有一个名字，叫做**__proto__**。它的实现大致是这样的：

```
Object.defineProperty( Object.prototype, "__proto__", {
    get: function() {
        return Object.getPrototypeOf( this );
    },
    set: function(o) {
        // ES6 中的 setPrototypeOf(..)
        Object.setPrototypeOf( this, o );
        return o;
    }
} );
```

一般来说，我们并不直接调用__proto__来访问对象的原型，那我们怎么对对象的原型进行各种关联操作呢？代码如下：

```
var foo = {
    something: function() {
        console.log( "Tell me something good..." );
    }
};
var bar = Object.create( foo );
bar.something(); // Tell me something good...
```

**Object.create**的简化的源码如下。由于Object.create是在es5中声明的，所以下面的代码也可以当做polyfill代码。

```
if (!Object.create) {
    Object.create = function(o) {
        function F(){}
        F.prototype = o;
        return new F();
    };
}
```

### 委托

委托是另一种设计模式，它非常适合js，示例代码如下：

```
Task = {
    setID: function(ID) { this.id = ID; },
    outputID: function() { console.log( this.id ); }
};

// 让 XYZ 委托 Task
XYZ = Object.create( Task );
XYZ.prepareTask = function(ID,Label) {
    this.setID( ID );
    this.label = Label;
};
XYZ.outputTaskDetails = function() {
    this.outputID();
    console.log( this.label );
};
// ABC = Object.create( Task );
// ABC ... = ...
```

这段代码的**易读性很好**，相比较类这种设计方式来说，可以直接看出我们要干什么。

个人觉得对于小型系统，用委托这种设计模式**远远优于**类设计模式。对于复杂的系统，我还没有体验过，不好妄下判断。
