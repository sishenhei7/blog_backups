## 概述

**set和map**属于es6的内容，今天在看书的时候遇到了，所以好好的总结一下，供以后开发时参考，相信对其他人也有用。

参考资料：
[mdn Keyed collections](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Keyed_collections)

### Map和WeakMap

Map是一个键值对对象，它有2个特点：
1. 它的键可以不局限于字符串，可以是**任何类型**。
2. 你可以根据**插入的顺序**来遍历里面的数据。

示例如下：

```
var haha = {a: 'a'};
var sayings = new Map();
sayings.set('dog', 'woof');
sayings.set(haha, 'meow');
sayings.size; // 2
sayings.get('fox'); // undefined
sayings.has('bird'); // false
sayings.delete('dog');
sayings.has('dog'); // false

for (var [key, value] of sayings) {
  console.log(key + ' goes ' + value);
}

sayings.clear();
sayings.size; // 0
```

一般来说，在这些时候使用Map而不是Object:
1. 键的类型**不知道**。
2. 键的类型是**数字，布尔值**等。（在Object中它们会被转化为字符串）

**WeakMap**也是一个键值对对象，它有如下特点：
1. **键只能是对象**。
2. **值可以是任何类型**。
3. 它不能被遍历。

它的用处之一是用来**隐藏数据**：

```
const privates = new WeakMap();

function Public() {
  const me = {
    // Private data goes here
  };
  privates.set(this, me);
}

Public.prototype.method = function() {
  const me = privates.get(this);
  // Do stuff with private data in `me`...
};

module.exports = Public;
```

### Set和WeakSet

Set是一个数据集对象，它有如下特点：
1. 里面的每个元素都**只出现一次**。
2. 你可以根据插入的顺序来遍历里面的数据。

示例如下：

```
var mySet = new Set();
mySet.add(1);
mySet.add('some text');
mySet.add('foo');

mySet.has(1); // true
mySet.delete('foo');
mySet.size; // 2

for (let item of mySet) console.log(item);
// 1
// "some text"
```

Set和**Array**相互转化的方法：

```
//Set转化为Array
Array.from(mySet);
[...mySet2];

//Array转化为Set
mySet2 = new Set([1, 2, 3, 4]);
```

为什么要使用Set？
1. 使用Array的indexOf方法很慢。
2. Set可以根据值来删除元素。
3. Set的元素不重复。

WeakSet是一个对象集对象，它有如下特点：
1. 里面的元素都**只能是对象**。
2. 里面的元素都只出现一次。
3. 不可被遍历。

WeakSet的优点之一是可以**防止内存泄漏**，具体使用方法不明。


































