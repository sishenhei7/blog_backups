## 概述

这是我学习react的过程中，学到的一些**简便写法**，都是利用了es6的特性，记录下来供以后开发时参考，相信对其他人也有用。

参考资料：[dva.js 知识导图](https://github.com/dvajs/dva-knowledgemap#spread-attributes)

### 析构

我们也可以**析构**传入的函数**参数**。

```
const add = (state, { payload }) => {
  return state.concat(payload);
};
```

还可以**代替apply**。（在es6之前，我们一般都是用apply来把数组类型的参数引用给函数的。）

```
function foo(x, y, z) {}
const args = [1,2,3];

// 下面两句效果相同
foo.apply(null, args);
foo(...args);
```

**对象字面量的改进**，这是析构的反向操作，用于组装一个object。

```
const name = 'duoduo';
const age = 8;

const user = { name, age };  // { name: 'duoduo', age: 8 }
```

还可以**收集函数参数**为数组：

```
function directions(first, ...rest) {
  console.log(rest);
}
directions('a', 'b', 'c');  // ['b', 'c'];
```

### spread attributes

这是jsx从es6借鉴过来的很有用的特性，用于**扩充组件的props**。

```
const attrs = {
  href: 'http://example.org',
  target: '_blank',
};
<a {...attrs}>Hello</a>

//等同于

const attrs = {
  href: 'http://example.org',
  target: '_blank',
};
<a href={attrs.href} target={attrs.target}>Hello</a>
```

### 定义promise

```
const delay = (timeout) => {
  return new Promise(resolve => {
    setTimeout(resolve, timeout);
  });
};

delay(1000).then(_ => {
  console.log('executed');
});
```

### 函数组件

React组件有 3 种定义方式：React.createClass，class和Stateless Functional Component，其中Stateless Functional Component是函数，不是 Object，**没有 this 作用域**，是 pure function。

### 注释

尽量**别用 // 做单行注释**。

```
<h1>
  {/* multiline comment */}
  {/*
    multi
    line
    comment
    */}
  {
    // single line
  }
  Hello
</h1>
```












