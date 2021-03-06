## 概述

最近对http很感兴趣，于是开始看[《http权威指南》](https://book.douban.com/subject/10746113/)。别人都说这本书有点老了，而且内容太多。我个人觉得这本书**写的太好了**，非常长知识，让你知道关于http的很多概念，不仅告诉你怎么做，还告诉你为什么这么做。于是我把学到的知识点记录下来，供以后开发时参考，相信对其他人也有用。

### 缓存

1.**缓存**有以下优点：
- 减少了冗余的数据传输，节省了你的网络费用。
- 缓解了网络瓶颈的问题，不需要更多的带宽就能够更快的加载页面。
- 降低了对原始服务器的要求，服务器可以更快地响应，避免过载的出现。
- 降低了距离时延，因为从较远的地方加载页面会更慢一些。

2.可以用已有的副本为某些到达缓存的请求提供服务，这被称为**缓存命中**；其它一些到达缓存的请求可能会由于没有副本可用，而被转发给原始服务器，这被称为**缓存未命中**。原始服务器的内容可能会发生变化，缓存要不时对其进行检测，看看它们保存的副本是否仍是服务器上最新的副本。

3.缓存对缓存的副本进行再验证时，会向原始服务器发送一个小的再验证请求，如果内容没有变化，服务器会以一个小的**304 Not Modified**进行相应。只要缓存知道副本仍然有效，就会再次将副本标识为暂时新鲜的，并将副本提供给客户端，这被称为**再验证命中**或**缓存命中**。

4.由于文档并不全是同一尺寸的，所以**文档命中率**并不能说明一切。有些大型对象被访问的次数可能较少，但由于尺寸的原因，对整个数据流量的贡献却更大，因此有些人更愿意使用**字节命中率**作为度量值。

5.客户端有一种方法来判断响应是否来自缓存，就是使用**Date首部**。将响应中Date首部的值与当前时间进行比较，如果响应中的日期值比较早，客户端通常就可以认为这是一条缓存的响应。

6.**私有缓存**是单个用户专用的，通常内建在web浏览器中；**公有缓存**是特殊的共享代理服务器。

7.在实际中，缓存实现了**层次化**。在较小缓存中未命中的请求会被导向较大的父缓存。有些网络会构建复杂的**网状缓存**，这种代理缓存会决定选择何种路由对内容进行访问、管理和传送，因此可将其称为**内容路由器**。

8.缓存的处理**步骤**：
1. 接收，从网络中读取抵达的请求报文。
2. 解析，对报文进行解析，提取出URL和各种首部。
3. 查询，查看是否有本地副本可用，如果没有，就获取一份副本。
4. 新鲜度检测，查看已缓存的副本是否足够新鲜，如果不是就询问服务器是否有更新。
5. 创建响应，用新的首部和已缓存的主体来构建一条响应。
6. 发送，通过网络将响应发送给客户端。
7. 日志，可选地创建一个日志文件。

9.缓存**不应该调整Date首部**，Date首部表示的是原始服务器最初产生这个对象的日期。

10.**文档过期**：通过http/1.0+的Expires首部和http/1.1的Cache-Control：max-age首部，http让原始服务器向每个文档加了一个过期时间。

11.仅仅是已缓存文档过期了并不意味着它和原始服务器上的文档有什么不同。所以在过期的时候需要**服务器再验证**，询问原始服务器文档是否发生了变化。

12.所有以“If-”开头的条件首部，都是**缓存再验证条件首部**。其中最重要的是If-Modified-Since和If-None-Match。

13.有时，服务器希望在对文档进行一些非实质性或不重要的修改时，不要使所有的已缓存副本都失效。HTTP/1.1支持**弱验证器**，如果只对内容有少量修改，就允许服务器声明那是足够好的等价体。服务器会用**前缀“W/”来标识弱验证器**。比如：ETAG:W/"v2.6"或者If-None-Match: W/"v2.6"

14.Cache-Conrol:no-store会**禁止缓存**；Cache-Conrol:no-cache表示可以缓存，但是每次使用缓存前需要**进行新鲜度再验证**。

15.**Refresh按钮**会发布一个附加了Cache-Control请求首部的GET请求，这个请求会强制进行再验证，或者无条件从服务器获取文档。

16.文档过期系统并**不是一个完美的系统**，如果发布者不小心分配了一个很久之后的过期日期，那么在文档过期之前，它要对文档所做的所有修改都不会出现在任何缓存中。因此，很多发布者甚至都不使用过期日期。

17.发布广告者的**两难处境**，虽然缓存有利于更快的显示，但是很多内容提供商的收益都是通过广告实现的，每向用户显示一次广告内容，内容提供商就会获得相应的收益。这就是缓存的问题，如果缓存做的很好，原始服务器根本收不到任何HTTP访问，因为这些访问都被因特网缓存吸收了。所以一般内容提供商都会在内容上**加上no-cache首部**，让用户每次访问时都与服务器进行再验证。
