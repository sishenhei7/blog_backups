## 概述

像Mysql和Mongodb这样的数据库，一般都是在命令行或者工具里面进行操作，如果想在node搭建的服务器上面操作，就必须要利用特殊的模块的。其中操作Mongodb数据库需要用到mongoose模块，下面记录我学习mongoose模块的过程，供以后开发时参考，相信对其他人也有用。

参考资料：[mongoose文档](http://www.nodeclass.com/api/mongoose.html)

### 准备工作

需要先安装MongDB数据库和Node.js。

然后在npm里面输入如下命令：

```
//初始化
npm init

//安装mongoose依赖
npm install mongoose
```

### 连接数据库

如果要操作数据库，肯定需要先连接数据库（并不需要额外在mongdb数据库里面新建，如果node发现不存在这个数据库会自动创建一个，但是需要*先启动mongodb服务*）：

```
// getting-started.js
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/test');
```

然后用node运行getting-started.js即可连接到mongodb数据库。但是上面的代码并没有做任何事，所以我们需要加入如下代码进行操作：

```
var db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', function (callback) {
  console.log('成功连接数据库');
});
```

用node运行getting-started.js之后会在命令行打印“成功连接数据库”，这就表示，*之后所有的操作都必须在那个回调函数里面写。*

### 创建数据库和条目

mongoose里面的三个概念很重要：
1. **schema**，对应于mongodb里面的collection（集合），在这里定义键和类型（schematypes）。
2. **model**，对应于schemas的一个类，通过它来创建条目。
3. **document**，对应于mongodb里面的entry，就是条目。

一般通过下面这个过程创建数据库的条目：

```
//创建Schema
var personSchema = mongoose.Schema({
    name: String,
    height: Number
});

//通过Schema创建model，由于是类，所以首字母大写
var PersonModel = mongoose.model('Person', personSchema);

//创建document
var tim = new PersonModel({ name: 'Tim', height: 150});

//保存(这是一个异步动作，回调会在同步代码完毕后再运行)
tim.save(function(err, tim) {
    console.log(tim.name + '保存成功');
})

//输出tim
console.log(tim);
```

**注意**：如果数据库中有Person这个collection，则会自动连接到这个collection，否则会自动创建一个。

### schema

对于schema，我们可以对它做三件事：**增加或删除键；定义主键；增加方法**。

我们可以用add方法增加（删除）键：

```
personSchema.add({ weight: 'number', nickname: 'string' });
```

可以通过下面的示例感受一下：

```
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/test');

var db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', function(callback) {
  console.log('开始进行MongoDB数据库操作');

  var personSchema = mongoose.Schema({
      name: String,
      height: Number
  });

  var PersonModel = mongoose.model('Person', personSchema);

  var tim = new PersonModel({ name: 'Tim', height: 150});

  personSchema.add({ weight: 'number', nickname: 'string' });

  var bim = new PersonModel({ name: 'bim', height: 160, weight: 60});

  //输出tim的名字
  console.log(tim); //{_id: 5aed9a70595695188446035d, name: 'Tim', height: 150}
  console.log(bim); //{_id: 5aed9a70595695188446035e, name: 'Bim', height: 160, weight: 60}
  console.log(bim.nickname); //undefined
});
```

从上面的例子可以看到，没有定义的属性是undefined，并且不会输出。我们还可以看到，有一个默认的属性_id，它是schema的默认主键，如果想修改主键的话，可以用下面的方法：

```
var personSchema = mongoose.Schema({
    id: mongoose.Schema.ObjectId,
    name: String,
    height: Number
});

var PersonModel = mongoose.model('Person', personSchema);

var tim = new PersonModel({ id: new mongoose.Types.ObjectId, name: 'Tim', height: 150});
```

但是上面只是定义了一个类型为ObjectId的主键而已，它会自动增加，但是它并不是主键。mongodb不允许自定义主键（貌似是这样？），但是可以用虚拟键解决这个问题。

可以通过下面的代码给schema增加方法：

```
personSchema.methods.speak = function() {
    var greeting = this.name ? 'My name is ' + this.name : 'I have not a name';
    console.log(greeting);
}
var PersonModel = mongoose.model('Person', personSchema);
var tim = new PersonModel({ name: 'Tim', height: 150});
tim.speak();
```

### model

model是创建条目的类，对于model我们想做这几件事：**增删改查**。

增操作：利用model的create方法。（这个方法会自动保存条目）

```
PersonModel.create({name: sim}, function(err, person) {
        console.log('增加' + person.name);
});
```

删除操作：利用model的remove方法。

```
PersonModel.remove({name: Tim}, function(err) {
        console.log('删除成功');
});
```

改操作：有很多种方法，但是所有方法**都需要加一个回调函数，不然不会成功**。

```
var condition = {name: 'bim'},
    query = {$set:{name: 'time', nickname: 'haha'}};

//使用update方法
PersonModel.update(condition, query, function() {
    console.log('修改成功');
});

//使用findOneAndUpdate方法
PersonModel.findOneAndUpdate(condition, query, function() {
    console.log('修改成功');
});
```

查操作：利用model的find方法。

```
PersonModel.find({ name: 'bim' }, function(err, person){
    console.log(person[0]);
})
```

注意：增操作和改操作都是**异步的**，所以在增操作和改操作之后立即查询是**查不到的**。

### document

document就是条目entry，对于document我们想对它**做一些处理**，比如进行回调等。

这个时候就可以利用exec方法了，实例如下：

```
var query = PersonModel.findOne({ 'name': 'time' });
query.select('name nickname');
query.exec(function (err, person) {
  if (err) return handleError(err);
  //输出：time is also called haha.
  console.log('%s is also called %s.', person.name, person.nickname) 
})
```
