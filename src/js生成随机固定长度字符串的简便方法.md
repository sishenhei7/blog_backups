## 概述

碰到一个需求：用js生成固定长度的字符串。在网上查了很多资料，网上的方法都**比较麻烦**。我自己灵光一现，实现了一个**比较简单**的方法。记录下来，供以后开发时参考，相信对其他人也有用。

### js生成随机字符串

js生成随机字符串有一个**奇妙的写法**：

```
//输出随机字符串
const randStr = () => Math.random().toString(36).substr(2);
```

浏览器开发者工具输入5次，输出如下：

```
"4cc9gd4sbwd"
"ox9r8g6g7h"
"fejq6b7up7e"
"c1w7r88tnx"
"cokjhpxsycq"
"jn5eue1vmp"
```

可以看到，字符串是随机的，但是**长度不固定**。

### 生成长度固定的字符串

昨天学习js函数式编程，所以灵机一动，利用递归的方法，并且传入一个函数，来简便的把一个字符串变成想要的长度：

```
//字符串调整为len位
const supplyFunc = (str, len) => {
  if(str.length > len) return str.substr(0, len);
  if(str.length < len) return supplyFunc(str + randStr(), len);
  return str;
}
```

逻辑是：
1. 如果字符串长度超过len位，那么**直接截取**就行。
2. 如果字符串长度不足len位，用**randStr()**再生成一段随机字符串进行**拼接**，然后**递归**，直到长度超过len位。

然后写一个函数来自定义长度，比如输出一个**长度为10**的随机字符串的函数：

```
//随机len位的字符串
const str10 = () => supplyFunc(randStr(), 10);
```

输入5次，输出如下：

```
"y3tas9bqzi"
"2pmi80fs0e"
"i5dcde4g7q"
"uyoa9q3rmj"
"ty7ymuxm0m"
```

可以看到，全是长度为10的字符串。

### 建议加密解密函数

然后里面上面的函数，可以自制一个**js加密解密函数**：

```
//加密函数
const encode = str => str10() + escape(str) + str10();

//解密函数
const decode = str => unescape(str.substr(10, str.length - 20));
```

比如作如下试验：

```
encode('馒头加梨子')
//输出 "qnhbj5yo9k%u9992%u5934%u52A0%u68A8%u5B50069keq6dy8"

encode('馒头加梨子')
//输出 "3vz6tr2shp%u9992%u5934%u52A0%u68A8%u5B50l4f8mva6bn"

encode('馒头加梨子')
//输出 "f6qqsauzek%u9992%u5934%u52A0%u68A8%u5B505g64gndpuk"

decode("qnhbj5yo9k%u9992%u5934%u52A0%u68A8%u5B50069keq6dy8")
//输出 "馒头加梨子"

decode("3vz6tr2shp%u9992%u5934%u52A0%u68A8%u5B50l4f8mva6bn")
//输出 "馒头加梨子"

decode("f6qqsauzek%u9992%u5934%u52A0%u68A8%u5B505g64gndpuk")
//输出 "馒头加梨子"
```

加密后密文都不同，但是解密后**都是"馒头加梨子"**。
