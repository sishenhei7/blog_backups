# 腾讯Alloy团队代码规范

## 概述

我个人很看重代码规范，因为代码是写给别人看的，按规范写别人才更容易理解。之前苦于没有代码规范的资料，现在在github上面看到了[腾讯Alloy团队的代码规范](https://github.com/AlloyTeam/CodeGuide)，于是学习了一下，并记录下我自己还**没怎么注意**的地方，供以后开发时参考，相信对其他人也有用。

顺便说下，这里是[腾讯Alloy团队推荐的sublime3配置](http://alloyteam.github.io/CodeGuide/#check)。

### 命名规则

1. 文件命名全部采用小写方式， 以下划线分隔，有复数结构时，要采用负数命名法。

### HTML
1. 属性名，使用双引号，不要使用单引号；全小写，用中划线做分隔符。
2. 不要在自动闭合标签结尾处使用斜线。
3. doctype大写。
4. 在html标签上加上lang属性。
5. 声明一个明确的字符编码，通常指定为'UTF-8'。
6. 用meta标签指定页面应该用什么版本的IE来渲染(比如content="IE=Edge")；
7. 在引入CSS和JS时不需要指明type。
8. 属性应该按照特定的顺序出现以保证易读性，class>id>name>data-*>src等
9. boolean属性不需要声明取值。
10. 避免用js生成标签。
11. 在编写HTML代码时，需要尽量避免多余的父节点。

### css，scss
1. [属性声明顺序](http://alloyteam.github.io/CodeGuide/#css-declaration-order)。
2. 以下几种情况需要换行：(1)'{'后和'}'前(2)每个属性独占一行(3)多个规则的分隔符','后。
3. 注释统一用'/* */'（scss中也不要用'//'）。
4. 最外层统一使用双引号。
5. 类名使用小写字母，以中划线分隔；id采用驼峰式命名；scss中的变量、函数、混合、placeholder采用驼峰式命名。
6. 颜色16进制用小写字母；颜色16进制尽量用简写。
7. 除了margin和padding，都不需要使用属性简写，尽量分开声明。
8. 尽量将媒体查询的规则靠近与他们相关的规则，不要放进独立样式文件，也不要扔在底部。
9. @import 引入的文件不需要开头的'_'和结尾的'.scss'。
10. 声明顺序：@extend；不包含 @content 的 @include；包含 @content 的 @include；自身属性；嵌套规则。
11. 去掉小数点前面的0。
12. 属性值'0'后面不要加单位。
13. 同个属性不同前缀的写法需要在垂直方向保持对齐，无前缀的标准属性应该写在有前缀的属性后面。
14. 用 border: 0; 代替 border: none;
15. 发布的代码中不要有 @import。
16. 尽量少用'*'选择器。

### JavaScript
1. return后面需要加分号。
2. 这些关键字后要留一个空格：if, else, for, while, do, switch, case, try, catch, finally, with, return, typeof。
3. '}'前需要换行。
4. 单行注释缩进与下一行代码保持一致。
5. 多行注释最少三行, '*'后跟一个空格。
6. **最外层统一使用单引号。**
7. 这些字符串一律按这里的写法，不小写或大写：'ID'，'URL'，'Android'， 'iOS'。
8. 构造函数，大写第一个字母
9. 常量全大写，用下划线连接。
10. 对象属性名不需要加引号；数组、对象最后不要有逗号。
11. 永远不要直接使用undefined进行变量判断；使用typeof和字符串'undefined'对变量进行判断。
12. for-in里一定要有hasOwnProperty的判断。
13. 不要在同个作用域下声明同名变量。
14. 不要在一些不需要的地方加括号，例：delete(a.b)。
15. 数组中不要存在空元素。(有疑问？？？)
16. 换行符统一用'LF'，即'/n'。
17. 对上下文this的引用只能使用'_this', 'that', 'self'其中一个来命名。
18. 一个函数作用域中所有的变量声明尽量提到函数首部，用一个var声明，不允许出现两个连续的var声明。比如：
```
function doSomethingWithItems(items) {
    // use one var
    var value = 10,
        result = value + 10,
        i,
        len;

    for (i = 0, len = items.length; i < len; i++) {
        result += 10;
    }
}
```




