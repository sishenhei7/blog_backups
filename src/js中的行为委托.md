## 概述

[《你不知道的JavaScript》](https://book.douban.com/subject/27620408/)中有这么一段话：不幸的是，**将类和继承的设计模式思维带入Javascript的想法是你所做的最坏的事情**，因为语法可能会让你迷惑不已，让你以为真的有类这样的东西存在，实际上原型机制与类的行为特性是完全相反的。

**js的原型机制本质上是行为委托机制。**行为委托认为对象之间是**兄弟关系**，互相委托，而不是父类和子类的关系。

### 面向类的编程

```
class Polygon {
    constructor(height, width) {
        this.name = 'Polygon';
        this.height = height;
        this.width = width;
    }
}

class Rectangle extends Polygon {
    constructor(height, width) {
        super(height, width);
        this.name = 'Rectangle';
    }
}

class Square extends Rectangle {
    constructor(length) {
        super(length, length);
        this.name = 'Square';
    }
}
```

如上所示，是一个四边形——矩形——正方形的类关系图。看起来设计的很好，但是由于他们的层级关系，他们有这些缺陷：
1. 每次都要写constructor，有时还要去写prototype。
2. 如果矩形这个类要增加一个行为，但是这个行为在正方形中不需要怎么办？
3. 如果要在矩形和正方形之间再增加一层，要怎么办？

用面向类的设计模式，如果遇到上面3个问题，处理起来会很麻烦，这就导致整个架构非常难扩展。原因是在设计整个类的架构的时候，**我们已经规定好了每个类的行为以及层级结构，如果后续它们有变动，就会触及到架构问题，所以修改起来会很麻烦。**

### 行为委托

怎么解决上述的问题1呢？方法是利用行为委托。

```
let polygon = {
    init: function(width, height){
        this.width = width;
        this.height = height;
        this.setName();
    },
    setName: function() {
        this.name = 'polygon';
    }
}

let rectangle = Object.create(polygon);

rectangle.setName = function() {
    this.name = 'rectangle';
}

//初始化一个polygon
const a = Object.create(polygon);
a.init(2,3);

//初始化一个rectangle
const b = Object.create(rectangle);
b.init(3,4);

//初始化一个rectangle
const c = Object.create(rectangle);
c.init(4,5);
```

可以看到，利用**Object.create**方法，使rectangle的prototype委托了polygon对象，从而能够使用polygon对象的各种方法。这样做的优点是，整个过程非常简洁，我们**不需要去探究prototype，也不需要去管constructor。**

值得一提的是，我们可以通过**Object.assign**来扩充rectangle对象的方法，而不需要一条条去写rectangle的方法。实例如下：

```
Object.assign(rectangle, {
    setName: function() {
        this.name = 'polygon';
    },
    setWidth: function(width) {
        this.width = width;
    },
    setHeight: function(height) {
        this.height = height;
    }
});
```

### 无类编程

为了解决问题2和3，我们打算降低层级，对所有的类进行**“扁平化”管理**，即是说，让Polygon作为最底层的类，然后所有的类都继承自它，这样我们就非常好扩展了。实例如下：

```
class Polygon {
    constructor(height, width) {
        this.name = 'Polygon';
        this.height = height;
        this.width = width;
    }
}

class Rectangle extends Polygon {
    constructor(height, width) {
        super(height, width);
        this.name = 'Rectangle';
    }
}

const rectangleMixinPublic = {
    rectanglePublic1: function(){

    },
    rectanglePublic2: function(){

    }
}

const rectangleMixinPrivate = {
    rectanglePrivate1: function(){

    },
    rectanglePrivate2: function(){

    }
}

Object.assign(Rectangle.prototype, rectangleMixinPublic, rectangleMixinPrivate);

class Square extends Polygon {
    constructor(length) {
        super(length, length);
        this.name = 'Square';
    }
}

const squareMixinPublic = {
    squarePublic1: function(){

    },
    squarePublic2: function(){

    }
}

const squareMixinPrivate = {
    squarePrivate1: function(){

    },
    squarePrivate2: function(){

    }
}

Object.assign(Square.prototype, 
    rectangleMixinPublic, 
    rectangleMixinPrivate,
    squareMixinPublic,
    squareMixinPrivate);
```

从上面的代码可以看到，通过利用**扁平化使类都继承于基类**，然后利用**Object.assign**方法进行扩展prototype，使整个架构非常灵活。

### 其它

当然，我上面只是举了一个例子来说明这几种编程方式，它们的具体应用还有很多方面，比如行为委托还用于**封装行为**，无类继承还可以通过函数封装成**函数式编程**的形式。

