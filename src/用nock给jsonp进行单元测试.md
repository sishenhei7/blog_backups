## 概述

前几天学习用Jest和nock.js对异步api进行单元测试。在项目中，我用到了jsonp，自然想到对jsonp进行单元测试。

过程很折腾，结果很有趣。

### jsonp.js

首先axios或者fetch都不支持jsonp，就连nodejs内置的http也不支持jsonp，所以要去找一个能发jsonp的库，翻了一下github，觉得jsonp这个库甚好，就用它了。

```
npm i jsonp
```

### 浏览器端与node端

Jest和nock都不支持浏览器端，只能用node端进行单元测试。

写好代码用node index.js运行，发现node端报错，在jsonp里面找不到document对象。

好吧，这是node端，jsonp的原理是用```script```标签跨域，没有html没有document怎么添加```script```标签跨域呢？

只好建html然后在浏览器端进行```script```标签跨域。

好吧，就用create-react-app，还自带jest。

```
reate-react-app jsonp-test
npm i nock
```

### 测试100%通过？

安装完之后就在app.test.js里面写测试代码：

```
import jsonp from 'jsonp';
import nock from 'nock';

var jsonpData = () => {
    return jsonp(host + url, opts, (err, data) => {
      return data;
    })
}

describe('test-jsonp', () => {

    // 测试nock是否能模拟jsonp
    test('should get successful status', () => {

        const host = '//dora.webcgi.163.com';
        const url = '/api/213_792_2018_09_14/active_check';
        const opts = {
            account: '316547491'
        };

        nock(host)
            .get(url)
            .reply(function(uri, requestBody, cb) {
                setTimeout(function() {
                    cb(null, [201, 'THIS IS THE REPLY BODY'])
                });
            });

        function callback(err, data) {
            expect(data.status).toBe(201);
            done();
        }

        jsonp('//dora.webcgi.163.com/api/213_792_2018_09_14/active_check', opts, callback);
    })
});
```

然后运行测试，发现100%通过，一片绿字，哇，心里很高兴，nock竟然能测试jsonp，哈哈！！！

再玩玩，修改了一下参数：

```
expect(1+1).toBe(3);
```

测试还是100%通过？不可能吧，哪里出了问题。

google没有找到，Jest的资料很少，只能看文档了，文档应该有。把Jest异步api测试文档仔细看了一下，才发现需要在箭头函数的括号里面加入done标记：

```
import jsonp from 'jsonp';
import nock from 'nock';

var jsonpData = () => {
    return jsonp(host + url, opts, (err, data) => {
      return data;
    })
}

describe('test-jsonp', () => {

    // 测试nock是否能模拟jsonp
    test('should get successful status', done => {

        const host = '//dora.webcgi.163.com';
        const url = '/api/213_792_2018_09_14/active_check';
        const opts = {
            account: '316547491'
        };

        nock(host)
            .get(url)
            .reply(function(uri, requestBody, cb) {
                setTimeout(function() {
                    cb(null, [201, 'THIS IS THE REPLY BODY'])
                });
            });

        function callback(err, data) {
            expect(data.status).toBe(201);
            done();
        }

        jsonp('//dora.webcgi.163.com/api/213_792_2018_09_14/active_check', opts, callback);
    })
});
```

### 测试超时？

然后再进行测试，发现，测试超时？为什么http请求发不出去呢？

用react进行本地调用api试了好几次，明明可以啊。

非常摸不着头脑，找不到问题所在，明明发出去了http请求，react里面也能够接收http响应，证明没有被拉黑名单，为什么测试就接收不到http响应呢？

想了半天快要放弃的时候才突然想到，script？是的，jsonp的原理是在html加一个script标签进行跨域，而在node端根本就没有html好吗？自然就接受不到http响应啊啊啊啊。

### 我学到了什么？

1. 对jsonp的原理有了更深的理解。jsonp不能在node端进行单元测试。
2. 就算单元测试100%通过了，也有可能实际上是错的。
3. 要注意找问题的方法，把过程详细的一条条写出来，然后思考在这些过程中哪里会出问题？



