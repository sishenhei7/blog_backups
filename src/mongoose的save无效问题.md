## 概述

今天朋友遇到了使用mongoose中的save无效的问题，我通过查找资料帮他解决了，把心得记录下来，供以后开发时参考，相信对其他人也有用。

参考资料：
[Mongoose学习参考文档——基础篇](http://ourjs.com/detail/53ad24edb984bb4659000013)
[Mongoose官方文档](http://mongoosejs.com/docs/api.html#mongoose_Mongoose)

### mixed类型的save

mixed类型=nested类型，也就是**混合类型或者嵌套类型**。这种类型没有特定的约束，可以随意修改，但是修改之后**需要调用markModified()**，然后才能save成功。

```
person.anything = {x:[3,4,{y:'change'}]}
person.markModified('anything');//传入anything，表示该属性类型发生变化
person.save();
```

### promise和await

参考资料：[Built-in Promises](http://mongoosejs.com/docs/promises.html#built-in-promises)

一般来说，mongoose是通过**回调函数**来进行增删改查的，如下列save所示：

```
product.sold = Date.now();
product.save(function (err, product) {
  if (err) ..
})
```

如果不加回调函数，就会返回一个**promise**。

```
product.save().then(function(product) {
   ...
});
```

对于其它一些返回query的操作来说，不加回调函数，会返回一个**带有then方法的对象**，但是它并不是promise。

```
var query = Band.findOne({name: "Guns N' Roses"});
assert.ok(!(query instanceof Promise));

// A query is not a fully-fledged promise, but it does have a `.then()`.
query.then(function (doc) {
  // use doc
});
```

如果想将它变成一个真正的promise，**执行exec()方法**即可。

```
// `.exec()` gives you a fully-fledged promise
var promise = query.exec();
assert.ok(promise instanceof Promise);

promise.then(function (doc) {
  // use doc
});
```

对于async和await来说，直接**在后面加上exec()方法**即可。

```
var query = await MyModel.findOne({}).exec();
```



















