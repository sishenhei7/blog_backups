## 概述

很早就想研究underscore源码了，虽然underscore.js这个库有些过时了，但是我还是想学习一下库的架构，函数式编程以及常用方法的编写这些方面的内容，又恰好没什么其它要研究的了，所以就了结研究underscore源码这一心愿吧。

[underscore.js源码研究(1)](http://www.cnblogs.com/yangzhou33/p/8859331.html)
[underscore.js源码研究(2)](http://www.cnblogs.com/yangzhou33/p/8886992.html)
[underscore.js源码研究(3)](http://www.cnblogs.com/yangzhou33/p/8910945.html)
[underscore.js源码研究(4)](http://www.cnblogs.com/yangzhou33/p/8922700.html)
[underscore.js源码研究(5)](http://www.cnblogs.com/yangzhou33/p/8972394.html)
[underscore.js源码研究(6)](http://www.cnblogs.com/yangzhou33/p/8975205.html)
[underscore.js源码研究(7)](http://www.cnblogs.com/yangzhou33/p/8977090.html)
[underscore.js源码研究(8)](http://www.cnblogs.com/yangzhou33/p/8983245.html)

参考资料：[underscore.js官方注释](http://underscorejs.org/docs/underscore.html)，[undersercore 源码分析](https://yoyoyohamapi.gitbooks.io/undersercore-analysis/content/supply/%E9%9D%A2%E5%90%91%E5%AF%B9%E8%B1%A1%E9%A3%8E%E6%A0%BC%E7%9A%84%E6%94%AF%E6%8C%81.html)，[undersercore 源码分析 segmentfault](https://segmentfault.com/u/hanzichi/articles?page=2)

### 模板引擎的改进

这篇博文主要是对之前建立的小模板引擎做**一些改进**。

### 支持大括号

理论上来说，如果按照下面的写法写tpl，就能够实现输出大括号了：

```
//定义模板和数据
const tpl = 'Students:' +
    //注意这里后面有一个大括号
    '{ for(i = 0; i < data.students.length; i++){ }' +
    '{{ data.students[i].name }}' +
    //插入语句，这个语句是一个大括号
    '{ } }';
```

但是实际匹配的时候，会匹配```{}```而不是```{}}```，所以我们需要改一下正则表达式的规则，按照underscore.js的写法，我们使用**ERB风格**的规则：

```
const rules = {
    //插值，对应变量
    interpolate: /<%=([\s\S]+?)%>/,
    //逻辑，对应语句
    evaluate: /<%([\s\S]+?)%>/
};

//2个正则合在一起，先替换变量，再替换语句
const matcher = new RegExp([
    rules.interpolate.source,
    rules.evaluate.source
].join('|'), 'g');
```

这样就可以实现大括号了，代码如下。（需要先导入上面的代码）

```
//定义模板和数据
const tpl = 'Students:' +
    //注意这里只有一个大括号！！！
    '<% for(i = 0; i < data.students.length; i++){ %>' +
    '<%= data.students[i].id %>' +
    '<%= data.students[i].name %>' +
    '<% } %>';
const data = {
    students: [{
        id: 1,
        name: ' haha '
    },{
        id: 2,
        name: ' yaya '
    }]
};

//render函数
function render(tpl, data) {
    let concating = 'let content = "";\n';
    let index = 0;
    //仍然是replace里面的第二个参数是函数的形式
    tpl.replace(matcher, (match, interpolate, evaluate, offset) => {
        //添加非模板的内容
        if (tpl.slice(index, offset)) {
            concating += 'content += "' + tpl.slice(index, offset) + '";\n';
        }
        //记录偏移量
        index = offset + match.length;
        //变量需要添加到content里面
        if (interpolate) {
            concating += 'content +=' + interpolate + ';\n';
        //语句不需要添加到content里面，而且不要分号
        } else if (evaluate) {
            concating += evaluate + '\n';
        }
    })
    concating += 'return content;';
    //以concating为内容，定义一个函数，参数是obj
    const renderFunc = new Function('obj', concating);
    return renderFunc(data);
}

//输出，结果为Students:1 haha 2 yaya
console.log(render(tpl, data));
```

它生成的**renderFunc函数**的代码如下面所示：

```
(function(obj
/*``*/) {
let content = "";
content += "Students:";
 for(i = 0; i < data.students.length; i++){ 
content += data.students[i].id ;
content += data.students[i].name ;
 }
return content;
})
```

### 字符串转义

有时候我们希望加入换行符等字符串来**调整输出的格式**，也就是说我们希望能解析如下所示的模板：

```
//注意下面的/n换行符
const tpl = 'Students: \n' +
    //注意这里只有一个大括号！！！
    '<% for(i = 0; i < data.students.length; i++){ %>' +
    '<%= data.students[i].id %>' +
    '<%= data.students[i].name %>' +
    '\n' +
    '<% } %>';
```

然而报错了，原因是我们在用字符串构建函数的过程中，```\n```导致了换行，所以语句被中断了，导致了报错。解决办法是对```\n```进行转义为```\\n```。

首先我们定义转义规则：

```
//定义转义规则
var escapes = {
    "'": "'",
    '\\': '\\',
    '\r': 'r',
    '\n': 'n',
    '\u2028': 'u2028',
    '\u2029': 'u2029'
}

//定义转义正则
var escapeRegExp = /\\|'|\r|\n|\u2028|\u2029/g;

//定义转义函数
var escapeChar = function(match) {
    return '\\' + escapes[match];
}
```

然后我们重写render函数，使它在添加**非模板内容的时候进行转义**：

```
function render(tpl, data) {
    let concating = 'let content = "";\n';
    let index = 0;
    tpl.replace(matcher, (match, interpolate, evaluate, offset) => {
        //添加非模板的内容
        if (tpl.slice(index, offset)) {
            //这里进行转义
            concating += 'content += "' + tpl.slice(index, offset).replace(escapeRegExp, escapeChar) + '";\n';
        }
        //记录偏移量
        index = offset + match.length;
        //变量需要添加到content里面
        if (interpolate) {
            concating += 'content +=' + interpolate + ';\n';
        //语句不需要添加到content里面，而且不要分号
        } else if (evaluate) {
            concating += evaluate + '\n';
        }
    })
    concating += 'return content;';
    //以concating为内容，定义一个函数，参数是obj
    const renderFunc = new Function('obj', concating);
    return renderFunc(data);
}
```

然后我们再来执行，输出如下，正是我们想要的：

```
Students:
1 haha
2 yaya
```

我们来看下它生成的renderFunc函数：

```
(function(obj
/*``*/) {
let content = "";
content += "Students: \n";
 for(i = 0; i < data.students.length; i++){ 
content += data.students[i].id ;
content += data.students[i].name ;
content += "\n";
 }
return content;
})
```

### 预编译

上面我们在使用这个模板的时候是这么使用的：

```
console.log(render(tpl, data));
```

很明显，render函数，tpl模板和data数据**耦合**在一起了，这就表明假如我们需要修改data数据的话，就要重新调用render函数**重新渲染tpl一次**，非常的消耗时间。

所以我们打算进行预编译。原理是，在render函数里面，我们**利用一个闭包储存concating（即render函数解析tpl的结果）**，然后下次data改动的时候，直接使用储存的concating数据，而不需要重新编译生成concating数据。代码如下：

```
//只接受tpl参数
function render(tpl) {
    let concating = 'let content = "";\n';
    let index = 0;
    tpl.replace(matcher, (match, interpolate, evaluate, offset) => {
        //添加非模板的内容
        if (tpl.slice(index, offset)) {
            concating += 'content += "' + tpl.slice(index, offset).replace(escapeRegExp, escapeChar) + '";\n';
        }
        //记录偏移量
        index = offset + match.length;
        //变量需要添加到content里面
        if (interpolate) {
            concating += 'content +=' + interpolate + ';\n';
        //语句不需要添加到content里面，而且不要分号
        } else if (evaluate) {
            concating += evaluate + '\n';
        }
    })
    concating += 'return content;';
    //以concating为内容，定义一个函数，参数是obj
    const renderFunc = new Function('obj', concating);
    //这里我们返回一个函数，形成一个闭包
    return function(data){
        return renderFunc(data);
    };
}
```

使用起来整个代码是这样的：

```
//定义转义规则
var escapes = {
    "'": "'",
    '\\': '\\',
    '\r': 'r',
    '\n': 'n',
    '\u2028': 'u2028',
    '\u2029': 'u2029'
}

//定义转义正则
var escapeRegExp = /\\|'|\r|\n|\u2028|\u2029/g;

//定义转义函数
var escapeChar = function(match) {
    return '\\' + escapes[match];
}

//为了方便，我们把规则封装在一个对象里面
const rules = {
    //插值，对应变量
    interpolate: /<%=([\s\S]+?)%>/,
    //逻辑，对应语句
    evaluate: /<%([\s\S]+?)%>/
};

//2个正则合在一起，先替换变量，再替换语句
const matcher = new RegExp([
    rules.interpolate.source,
    rules.evaluate.source
].join('|'), 'g');

//定义模板
const tpl = 'Students: \n' +
    //注意这里只有一个大括号！！！
    '<% for(i = 0; i < data.students.length; i++){ %>' +
    '<%= data.students[i].id %>' +
    '<%= data.students[i].name %>' +
    '\n' +
    '<% } %>';

//定义数据
let data = {
    students: [{
        id: 1,
        name: ' haha '
    },{
        id: 2,
        name: ' yaya '
    }]
};

//render函数
function render(tpl) {
    let concating = 'let content = "";\n';
    let index = 0;
    tpl.replace(matcher, (match, interpolate, evaluate, offset) => {
        //添加非模板的内容
        if (tpl.slice(index, offset)) {
            concating += 'content += "' + tpl.slice(index, offset).replace(escapeRegExp, escapeChar) + '";\n';
        }
        //记录偏移量
        index = offset + match.length;
        //变量需要添加到content里面
        if (interpolate) {
            concating += 'content +=' + interpolate + ';\n';
        //语句不需要添加到content里面，而且不要分号
        } else if (evaluate) {
            concating += evaluate + '\n';
        }
    })
    concating += 'return content;';
    //以concating为内容，定义一个函数，参数是obj
    const renderFunc = new Function('obj', concating);
    //这里我们返回一个函数，形成一个闭包
    return function(data){
        return renderFunc(data);
    };
}

//先进行编译
var tplCompile = render(tpl);
//输出
console.log(tplCompile(data));
//修改数据
data = {
    students: [{
        id: 1,
        name: ' haha '
    },{
        id: 2,
        name: ' yaya '
    },{
        id: 3,
        name: ' jaja '
    }]
};
//输出
console.log(tplCompile(data));
```

输出结果如下：

```
Students: 
1 haha 
2 yaya 

Students: 
1 haha 
2 yaya 
3 jaja 
```

### 其它

有时候，我们需要对向模板中**插入的值进行转义**，或者称为escape。比如说我们插入的是```<script>```标签，那么我们就需要对<进行转义，这个时候就必须在最开始声明的正则规则中添加第三条规则，我们这里就不讨论了，感兴趣的话可以参考[underscore.js源码](http://underscorejs.org/docs/underscore.html)。
