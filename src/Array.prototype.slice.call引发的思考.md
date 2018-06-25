## 概述

今天在看书的时候看到Array.prototype.slice.call(arguments)，有点看不懂，所以认真研究了一下，记录下来，供以后开发时参考，相信对其他人也有用。

### call

每一个函数都有一个call方法，它可以接受很多参数，其中第一个是这个函数的**上下文**，后面的是函数的参数。比如：

```
function add(a, b) {
    return a + b;
}

add(3, 4); //7
add.call(null, 3, 4); //7
add.call(this, 3, 4); //7
```

如果call的第一个参数为null的时候，此时他的上下文会自动是函数的上下文。

事实上，函数调用的**深层原理**就是通过call方法调用的。

并且我们看到，call方法第一个参数后面的参数都会传递给前面的那个函数。

### Array.prototype.slice.call(arguments)

上面的很好懂，但是如果call方法只接一个参数呢？这个上下文是怎么回事？应该把怎样的上下文传递进去？比如说下面的代码应该怎么理解？

```
function Haha() {
    this.id = 2;
}

function Yaya() {
    Haha.call(this);
}
```

其实很简单，如果只有一个参数的情况，那么call前面那个函数里面应该有this，否则就相当于没有参数。这个时候就会**给Yaya中的this绑定值**。

还有一种情况，不是绑定值，而是**直接调用**：

```
Array.prototype.slice.call(arguments);
```

这个时候，就**相当于使用arguments.slice()**，即是用**参数来调用这个函数**，只不过只是相当于而已，因为这个参数里面没有call前面的方法。

### 函数借用

这是一种借用方法的形式。这里列举几个常见的：

对一个对象使用**Object.prototype.toString.call**，来判断这个对象的类型：

```
Object.prototype.toString.call([2, 3, 4]); //返回'[object Array]'
```

由于字符串没有join方法，所以用**Array.prototype.join.call**对字符串执行连接：

```
Array.prototype.join.call('foo', '-'); //返回'f-o-o'
```

由于字符串没有map方法，所以用**Array.prototype.map.call**对字符串的每一个字符执行操作：

```
Array.prototype.map.call('foo', (item) => {
    return item.toUpperCase(item);
}).join(''); //返回FOO
```

### Function.prototype.call.bind

如果我们嫌Array.prototype.slice.call()太长想要简写怎么办？如果按照下面的代码就会报错：

```
const aa = Array.prototype.slice.call;
aa([2, 3, 4]);
```

原因是传递给aa的是**slice方法的call函数**，它**并没有**被"绑定到"Array.prototype.slice。

这个时候就需要bind函数了，它的参数完全和call一样，只不过它是**延迟执行**的，只有在被用到的时候才执行，而call会立即执行这个函数。

```
const aa = Function.prototype.call.bind(Array.prototype.slice);
aa([2, 3, 4]);
```

上面的代码很好理解，call也是一个函数，它也有一个bind方法，这个bind方法把call的上下文绑定给Array.prototype.slice，而这个call是哪里来的？是Function.prototype里面定义的。

值得一提的是，bind函数兼容所有主流浏览器，并且**兼容ie>=9**，所以在移动端或者ie只兼容到9的网页里面可以毫不犹豫的使用。

