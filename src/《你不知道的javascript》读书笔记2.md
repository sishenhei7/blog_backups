# 《你不知道的javascript》读书笔记2

## 概述

放假读完了[《你不知道的javascript》上篇](https://book.douban.com/subject/26351021/?channel=subject_list&platform=web)，学到了很多东西，记录下来，供以后开发时参考，相信对其他人也有用。

这篇笔记是这本书的下半部分，上半部分请见[《你不知道的javascript》读书笔记1](http://www.cnblogs.com/yangzhou33/p/8729349.html)。

### 误区

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






