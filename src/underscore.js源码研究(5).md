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

### 模板引擎

之前就接触过**模板引擎**，比如说template.js、handlebars.js、jade.js、nunjucks.js等等。后来学react的时候也接触过jsx，当时我就感到很不可思议，竟然能够把js中的**变量甚至语句**插入到html里面去，真的十分神奇。今天看underscore.js的源码的时候也发现里面竟然有模板引擎，于是我就来研究研究模板引擎。

### 实现变量替换

说到模板引擎，一个最基本的特性就是能在html代码中**插入js的变量**，下面我们来实现这种效果。

我们需要实现这种效果：

```
//定义一个模板
const tpl = 'hello {{name}}';

//定义值
const data = {name: 'haha'};

//渲染，最后content是name被替换过的html代码
const output = render(tpl, data);
```

其实仔细理了一下效果的流程之后，感觉实现这个效果挺简单的，就是用一个**正则替换**，把```{{ name }}```里面的值替换为data里面的数据就行了。

实现代码如下：

```
//定义替换的正则表达式
const rule = /{{([\s\S]+?)}}/g;

//render函数
function render(tpl, data) {
    return tpl.replace(rule, (matcher, p1) => {
        return data[p1];
    })
}
```

注意，由于rule里面只有一个括号，所以replace第二个参数的函数里面只有p1没有p2。

### 变量替换改进

为了便于阅读，我们需要模板可以写成下面的形式。(name两边有空格)

```
//定义一个模板
const tpl = 'hello {{ name }}';
```

所以我们加一个去空格的函数，整个代码如下：

```
//定义替换的正则表达式
const rule = /{{([\s\S]+?)}}/g;

//render函数
function render(tpl, data) {
    return tpl.replace(rule, (matcher, p1) => {
        return data[p1.trim()];
    })
}
```

注意：trim函数只兼容IE9，如果要兼容IE9以下的话就需要pollyfill了。

### 支持语句

几乎所有的模板引擎都支持写入**语句**，比如像下面的写法：

```
const tpl = 'Students:' +
    //注意这里只有一个大括号！！！
    '{ for(i = 0; i < data.students.length; i++) }' +
    '{{ data.students[i].name }}';
const data = {
    students: [{
        id: 1,
        name: ' haha '
    },{
        id: 2,
        name: ' yaya '
    }]
};
const content = render(tpl, data);
```

我们希望上述代码输出：

```
Students: haha  yaya
```

看起来非常复杂，可是我们用伪代码分解一下**执行过程**就感觉有点简单了：

```
//首先输出Students:
//然后执行下面的代码
for(i = 0; i < data.students.length; i++) {
//这里输出students[i].name
}
//完毕
```

实际写起来是这样的：

```
//定义要输出的内容
content = '';
content += 'Students:';
for(i = 0; i < data.students.length; i++) {
    content += 'data.students[i].name';
}
//输出整个content
return content
```

可以看到，上面有这2个要点：
1. 变量的内容**需要添加**到content里面，但是语句的内容**不需要添加**到content里面。
2. 不是模板的内容要记录位置，然后再通过这个位置添加到content里面。

所以好好整理一下，我们首先需要语句的正则表达式，然后通过这个正则表达式按照上述规则进行替换，代码如下：

```
//为了方便，我们把规则封装在一个对象里面
const rules = {
    //插值，对应变量
    interpolate: /{{([\s\S]+?)}}/,
    //逻辑，对应语句
    evaluate: /{([\s\S]+?)}/
};
//2个正则合在一起，先替换变量，再替换语句
const matcher = new RegExp([
    rules.interpolate.source,
    rules.evaluate.source
].join('|'), 'g');

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
```

它生成的renderFunc函数的代码如下图所示：

```
(function(obj
/*``*/) {
let content = "";
content += "Students:";
 for(i = 0; i < data.students.length; i++) 
content += data.students[i].name ;
return content;
})
```

可以看到有一个缺点，就是for循环**没有大括号**，这就导致它只执行下面的那条语句。如果要加大括号的话，就需要额外的规则，我们这里不讨论。

所以把上面所有的代码加起来就是这样的：

```
//为了方便，我们把规则封装在一个对象里面
const rules = {
    //插值，对应变量
    interpolate: /{{([\s\S]+?)}}/,
    //逻辑，对应语句
    evaluate: /{([\s\S]+?)}/
};

//2个正则合在一起，先替换变量，再替换语句
const matcher = new RegExp([
    rules.interpolate.source,
    rules.evaluate.source
].join('|'), 'g');

//定义模板和数据
const tpl = 'Students:' +
    //注意这里只有一个大括号！！！
    '{ for(i = 0; i < data.students.length; i++) }' +
    '{{ data.students[i].name }}';
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

//输出，结果为Students: haha  yaya 
console.log(render(tpl, data));
```

可以看到，*整个过程实际上是在拼接和替换字符串，然后利用Function接受字符串的情形生成函数，没有其他的任何内容。*

在下一篇博文中我们会对这个小的模板引擎进行优化。

