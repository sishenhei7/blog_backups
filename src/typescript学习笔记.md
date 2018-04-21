# typescript学习笔记

## 概述

这是我学习typescript的笔记。写这个笔记的原因主要有2个，一个是熟悉相关的**写法**；另一个是**理清**其中一些晦涩的东西。供以后开发时参考，相信对其他人也有用。

学习typescript建议直接看[中文文档](https://www.tslang.cn/docs/handbook/basic-types.html)或[英文文档](http://www.typescriptlang.org/docs/handbook/basic-types.html)。我是看的**英文文档**。

### 介绍

我不过多的介绍typescript，因为网上资料一大堆。我只记录下我的**个人见解**。

javascript是一个很灵活的语言，在维护时就会遇到很多坑，所以我们选择用typecript。

typecript的如下几点非常吸引我，并且非常**激动人心**。

1. 代码编写者的**最低权限原则**，又叫[Principle of least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege)。我们编写代码的时候，应该赋给代码编写者最低的权限，而不是过多的权限，否则代码编写者就会使用这过多的权限给别人挖一些坑。
2. **参数注释和检查**。以前我们在写一个函数的时候需要在函数上面写一些关于这个函数的注释：参数是什么，返回什么等。而typescript直接把他们集成到代码中去了，并且能够为这些提供检查。
3. **函数式编程思想**。typescript用到了少许函数式编程思想。比如下面这个例子。

```
function foo() {
    // okay to capture 'a'
    return a;
}

// illegal call 'foo' before 'a' is declared
// runtimes should throw an error here
foo();

let a;
```

### 基本类型

任何语言都有基本类型，typescript也是一种语言，也不例外。在声明这些基本类型的时候需要指定它们的类型。

#### boolean布尔值

```
let myBoolean: boolean = false;
```

#### number数字

```
let myNumber: number = 22;
```

#### string字符串

```
let myString: string = 'haha';
```

#### array数组

Array有2种写法，我个人推荐下面这种。

```
//推荐
let myList: number[] = [1, 2, 3];

//不推荐，仅作熟悉
let myList: Array<number> = [1, 2, 3];
```

#### tuple元组

```
let tuple01: [string, number];

//不止2个
let tuple02: [string, boolean, number];

//继续赋值会使用union type: string | number
tuple01 = ['hello', 10];
tuple01[5] = 'haha';
```

#### enum枚举

```
//注意没有分号，并且后面要加: Color声明
enum Color {Red, Green, Blue}
let c: Color = Color.Green;

//用数字引用元组内容，注意是string?
enum Color {Red = 1, Green = 3, Blue = 7}
let colrName: string = Color[3];
```

#### any

```
//Any无比强大
let notSure: any = 4;
notSure = 'haha';
notSure = false;
notSure.toFixed();

//数组
let list: any[] = [1, true, 'haha'];
```

#### void

```
//Void一般用于函数的返回值，表示没有返回值
function warnUser: void {
    alert('This is my warning message!');
}
```

#### null, underfied和Never

null和underfied是其它任何类型的**子类型**，所以可以把它们赋值给其它类型。

never一般在抛出异常和从不返回的函数中用到。(注意：never是**从不返回**，never returns；而void是**没有返回值**)

```
//抛出异常
function error(message: string): never {
    throw new Error(message);
}

//无限循环，从不返回
function infiniteLoop(): never {
    while (true) {
    }
}
```

#### type assertions类型断言

类型断言有2中形式，我们推荐下面这种。类型断言表示这个类型我能确定是这个，不用类型检查了，一般用于**跳过**typescript的类型检查。

```
//推荐
let someThing: any = 'this is haha';
let strLength: number = (someThing as string).length;

//仅作熟悉，不推荐
let someThing: any = 'this is haha';
let strLength: number = (<string>someThing).length;
```

### var的坑

#### 变量提升和函数作用域

变量提升和函数作用域不过多说明，上一段代码。

```
//块中声明的变量被提升了
function f1(shouldInitialize) {
    if (shouldInitialize) {
        var x = 10;
    }
    return x;
}
f1(true);  // returns '10'
f1(false); // returns 'undefined'

function f2(shouldInitialize) {
    return x;
}
f2(false); // error,Uncaught ReferenceError(未声明)
```

这就导致在块里面写错的话很难发现。比如下面这个例子。

```
function sumMatrix(matrix: number[][]) {
    var sum = 0;
    for (var i = 0; i < matrix.length; i++) {
        var currentRow = matrix[i];
        for (var i = 0; i < currentRow.length; i++) {
            sum += currentRow[i];
        }
    }
    return sum;
}
```

#### IIFE

**IIFE** = an Immediately Invoked Function Expression，**立即执行函数**，闭包的**用途**之一。

原理：由于var的变量声明只有函数作用域，所以每进入一个函数作用域，用var声明的变量都会**重新被声明->初始化**。然而，如果**没有**进入到一个函数作用域，var声明的变量就**不会**被重新声明->初始化。所以为了让i在进入setTimeout之前重新初始化，就需要用一个**函数作用域把它包裹起来**。(虽然setTimeout的参数里面有一个函数，但是这个函数是一个异步的，并不立刻执行。)

```
for (var i = 0; i < 10; i++) {
    // capture the current state of 'i'
    // by invoking a function with its current value
    (function(i) {
        setTimeout(function() { console.log(i); }, 100 * i);
    })(i);
}
```

### let和const

let和const具有块级作用域，它们都没有变量提升，let不能反复声明，const不能反复声明和修改。

因为let具有块级作用域，那就表示每进入一个块，变量都会重新声明和初始化。所以我们经常会看到下面的代码：

```
for (let i = 0; i < 10 ; i++) {
    setTimeout(function() { console.log(i); }, 100 * i);
}
```

能用const尽量用const，否则能用let尽量用let，最后才考虑var。

### destructuring解构

```
//数组解构
let input = [1, 2];
let [first, second] = input;

//交换位置
[first, second] = [second, first];

//函数参数解构
function f([first, second]: [number, number]): void {
    console.log(first);
}

//剩余部分解构
let [first, ...rest] = [1, 2, 3, 4];
console.log(rest); //[2, 3, 4]

//特定位置解构
let [first] = [1, 2, 3, 4];
let [, second, , fouth] = [1, 2, 3, 4];

//对象解构
let o = {
    a: 'foo',
    b: 12,
    c: 'bar'
};
let {a, b} = o;

//赋予别名
let {a: newName1, b: newName2} = o;

//默认值
let {a, b=1001} = o;

//带默认值的函数参数解构
type C = { a: string, b?: number }
function f({ a, b=1001 }: C): void {
    // ...
}
```

### spread展开

```
//数组展开
let first = [1, 2];
let second = [3, 4];
let bothPlus = [0, ...first, ...second, 5];

//对象展开
let defaults = { food: "spicy", price: "$$", ambiance: "noisy" };
let search = { ...defaults, food: "rich" };

//food会被覆盖
let defaults = { food: "spicy", price: "$$", ambiance: "noisy" };
let search = { food: "rich", ...defaults };

//对象展开并不会复制方法
class C {
  p = 12;
  m() {
  }
}
let c = new C();
let clone = { ...c };
clone.p; // ok
clone.m(); // error!
```

### interfaces接口

#### 类接口

```
interface SquareConfig {
    //标准写法
    label: string;
    //可选属性
    color?: string;
    //只读属性
    readonly x: number;
    //缺省属性
    [propName: string]: any;
}

//使用接口
function createSquare(config: SquareConfig): {color: string; area: number} {
    //用config.color这个形式调用
}

//跳过额外属性检查的方法一：类型断言(强制跳过)
let mySquare = createSquare({ width: 100, opacity: 0.5 } as SquareConfig);

//跳过额外属性检查的方法二：赋给变量(自动跳过)
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```

#### 其它接口

```
//函数接口，参数名不重要
interface SearchFunc {
    (a: string, b: string): boolean;
}

//使用函数接口，注意参数中的： string可以省略。
let mySearch: SearchFunc;
mySearch = function(source: string, substring: string): boolean {
    let result = source.search(substring);
    return result > -1;
}

//可索引的接口(数字索引)
interface StringArray {
    [index: number]: string;
}

//使用可索引接口
let myArray: StringArray;
myArray = ['Bob', 'Fred'];
let myStr: string = myArray[0];

//使用数字索引+字符串索引时，数字索引的类型需要是字符串索引的类型的子类型
iterface NumberDictionary {
    [index: string]: number; //字符串索引
    readonly [index: number]: number; //只读数字索引，必须为number的子类型
    length: number; //ok
    name: string; //error
}

```

#### 定义一个类

有以下四种方式定义一个类：
1. es2015规定的，类声明，只能定义一次，不然会报错，没有hoisting，**推荐使用**。
2. es2015规定的，类表达式，可以定义多次，后面覆盖前面的，没有hoisting，不推荐使用。
3. 函数声明，有hoisting，不推荐使用。
4. 函数表达式，没有hoisting，不推荐使用。

(ps:以前的**老书害人**啊，各种讨论函数形式定义一个类的坑，结果原来es2015直接用class搞定了)

参考：[MDN Classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)，[MDN class declaration](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/class)，[MDN class expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/class)，[MDN function declaration](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)，[MDN function expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/function)

```
//类声明，推荐
class Rectangle {
    constructor(height, width) {
        this.height = height;
        this.width = width;
    }
}

//类表达式，不推荐
var Rectangle = class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};

//函数声明，不推荐
function Rectangle() {
    this.height = height;
    this.width = width;
}

//函数表达式，不推荐
var Rectangle = function() {
    this.height = height;
    this.width = width;
}
```

#### 类中的属性和方法

类中有静态和实例的属性和方法。相关的定义如下所示：

```
//es2015中类的定义方式，推荐
class Animal {
  //constructor是静态方法，在里面定义实例属性(我们不需要静态属性)
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }

  //实例方法
  speak() {
    return this;
  }

  //静态方法
  static eat() {
    return this;
  }
}

//函数形式，不推荐
function Animal(x, y) {
    this.x = x;
    this.y = y;
};
Animal.prototype.speak = function() {
    return this;
}
Animal.eat = function() {
    return this;
}
```

需要注意的是，es2015中类的定义方式中的this会指向**undefined**；而函数方式中的this会指向**全局对象**。

```
//es2015中类的定义方式
let obj = new Animal();
obj.speak(); // Animal {}
let speak = obj.speak;
speak(); // undefined

Animal.eat() // class Animal
let eat = Animal.eat;
eat(); // undefined

//函数方式
let obj = new Animal();
let speak = obj.speak;
speak(); // global object

let eat = Animal.eat;
eat(); // global object
```

静态类型和实例类型的区别如下：
1. 静态类型：类可以**直接调用**的类型(不能被实例直接调用)，在内存中只占一块区域，创建实例时**不额外**分配内存。
2. 实例类型：**实例调用**的类型，每创建一个实例，都会在实例中**分配**一块内存。

#### 实现接口

接口只会检查类的实例属性，不会检查类的静态属性。所以不会检查constructor。如果constructor要用接口检查的话，需要额外给它定义一个接口，如下所示：

```
//额外定义constructor接口
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}

interface ClockInterface {
    tick();
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}
class AnalogClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```

#### 继承接口

```
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

//继承用extends，实现用implements。
interface Square extends Shape, PenStroke {
    sideLength: number;
}

//接口的另一种写法
let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

#### 混合接口

```
//既可以当做函数接口，又可以当做类接口
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

//当做函数接口
let counter01 = <Counter>function(start: number) {};

//当做类接口
let counter02 = <Counter>{};
counter02.interval = 2;
counter02.reset();
```

#### 继承类的接口

```
class Control {
    private state: any;
}

interface SelectableControl extends Control {
    select(): void;
}

//error，需要先继承Control才能实现接口SelectableControl
class Image implements SelectableControl {
    select() { }
}

//OK
class Button extends Control implements SelectableControl {
    select() { }
}
```

### 类

#### 基本使用

```
class Greeter {
    //只读，必须在声明的时候或者constructor里面初始化
    readonly greeting: string;
    //constructor里面的只读
    constructor(readonly message: string) {
        this.greeting = message;
    }
    greet() {
        return 'Hello, ' + this.greeting;
    }
}

class Greeter01 extends Greeter {
    constructor(message: string = 'everyone') {super(message);}
    greet() {
        return 'hi, ' + this.greeting;
    }
}

let sam = new Greeter01('world');
sam.greet();
```

#### public, private和protected

1. public是默认的，如果不声明就是public。
2. private指只能内部访问，不能被子类访问。
3. protected指只能被内部或者子类的实例访问。

注意：
1. 2个类相同，除了成员相同之外，private和protected的元素必须来自于同一处代码。
2. 如果一个类的constructor被标记为protected，那么表示这个类不能被实例化，只能通过它生成子类，然后实例化子类。

#### 存取器

存取器能够拦截并重写读写属性的操作，如果只有get没有set就会被认为是只读的。

```
let language = {
  log: ['example', 'test'],
  set current(name) {
    this.log.push(name);
  },
  get current() {
    if (this.log.length === 0) {
        return undefined;
    } else {
        return this.log[this.log.length - 1];
    }
  }
}

language.current = 'EN';
console.log(language.current);
```

#### 静态属性和方法

在属性或方法前面加上static就是静态属性或方法了，实例属性或方法用this访问，静态属性或方法用类名来访问。

#### 抽象类

抽象类和接口类似，只能被继承，不能被实例化，和接口不同的是，抽象类可以定义一些方法，这些方法可以被继承。

抽象类也有抽象属性，抽象属性和接口一样，一定要在子类中实现。

#### 类当做接口使用

很好理解，看下面这段代码即可。

```
class Point {
    x: number;
    y: number;
}

interface Point3d extends Point {
    z: number;
}

let point3d: Point3d = {x: 1, y: 2, z: 3};
```

### 函数

#### 简写形式和完整形式

由于编译的时候可以从简写形式推断出完整形式，所以推荐用简写形式。

```
//简写形式
function add(x: number, y: number): number {
    return x + y;
}
let myAdd = function(x: number, y: number): number { return x + y; };

//完整形式
let myAdd: (x: number, y: number) => number =
    function(x: number, y: number): number { return x + y; };
```

#### 可选参数和默认参数

```
//可选参数
function buildName(firstName: string, lastName?: string) {
    return firstName + " " + lastName;
}
//默认参数
function buildName(firstName: string, lastName = "Smith") {
    return firstName + " " + lastName;
}
```

可选参数和默认参数的函数类型是相同的：```(firstName: string, lastName?: string) => string```。为了书写方便，默认参数写在后面比较好。

参数多的话也可以解构和展开，示例如下：

```
function buildName(firstName: string, ...restOfName: string[]) {
    return firstName + " " + restOfName.join(" ");
}

let buildNameFun: (fname: string, ...rest: string[]) => string = buildName;
```

#### this

关于this的原理请看这里：[理解js中的函数调用和this](http://www.cnblogs.com/yangzhou33/p/8655100.html)。

如果遇到this报错，可以通过添加this: void或者this: 类名 来解决。

注意下面这种直接用等号的写法是一种实验性的写法，es6并不支持，只在es7上做了提案。但是typescript和react都对这种写法做了支持.

```
//用浏览器的审查元素运行，会报错
class Handler {
    onClickGood = () => { console.log(this); }
}
```

一般如果要用等号的话，我们需要写在constructor里面(注意要加this)。

```
//写在constructor里面
class Handler {
    constructor() {
        this.onClickGood = () => { console.log(this); }
    }
}

let myHandler = new Handler();
myHandler.onClickGood();
```


#### 函数重载

看下面的例子，注意前2个才是函数重载，最后一个是函数声明。在编译的时候，会逐一判断函数类型，如果这2个都不符合，就会报错。

```
let suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };
function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}
```




