# 反向代理学习笔记(一) Nginx与反向代理绪论

> 本来是只想学习Nginx的，但是想来只学Nginx学过来有些狭窄，因为现在反向代理服务器

[TOC]

## 什么是代理？

作为一个沪漂程序员，听到代理这个词，我下意识的想到了中介，现在在上海已经很少能找到房东看房子了，基本上都是从中介那里看房，从这个角度来说中介代理了房东的部分职责，带你看房。在汉语词典中也是这么阐释的，待他人处理事务。那我们给代理前面加上正向这个词呢，这也就成为了计算机领域内的专有名词，正向代理。

### 什么是正向代理？

那什么是正向代理呢，我想起大学的上机课，为了让我们好好学习，上机课的电脑是无法看到一些娱乐网站的, 比如知乎、腾讯游戏。因为有同学确实在课上打游戏，丝毫不听课。学校好像意识到了问题的不对，加上了限制，再一次上课的我们，就发现一些娱乐网站上不了了。是通过网络白名单来进行的限制，那有什么方法能够绕开这个限制呢，答案就是代理，上网的时候，流量先到达我们的代理服务器，获取到内容之后，再给我们。像下面这样:

![image-20230320195811499](C:\Users\chenxingke\AppData\Roaming\Typora\typora-user-images\image-20230320195811499.png)

这是代理服务器的第一个用处，绕开浏览限制，一些学校和其他组织使用防火墙来使用户访问受限制的互联网。 代理服务器可以用于绕开这些限制，因为它们使用户可以连接到代理服务器，而不是直接连接到它们正在访问的沾点。与之相对，我们也可以使用代理服务器来组织特定用户群访问某些站点，比如学校网络可能配置为通过启动内容筛选规则的代理连接到Web，不在白名单的，拒绝转发响应。某些场景下，互联网用户也希望保护自己的在线身份，在一些情况下，互联网用户希望增强其在线匿名性，比如不想显示自己的真实ip，如果我们用代理服务器来访问互联网，发表评论的时候就更难追溯到真实ip。这是正向代理，我们可以认为正向代理，代理的是人。

### 什么是反向代理?

那反向代理呢，反向代理代理的则是服务器，有的时候我们并不愿意将我们的服务端应用完整的暴露出去，这会增大服务端被攻击的风险，这也就是反向代理服务器，如下图所示：

![No alt text provided for this image](https://substackcdn.com/image/fetch/w_1456,c_limit,f_auto,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2F257642d6-9742-432b-9ca8-2a866dea04dd_1445x1536.jpeg)

图片来自ByteByteGo。反向代理服务器可以帮助我们保护服务端，同时也可以帮我们做负载均衡，缓存静态资源，加密和解密SSL请求。本篇我们的重头戏就是反向代理服务器。

## 反向代理服务器

想我刚实习的时候，因为会的比较少，觉得自己用的技术比较没有逼格，听到Nginx、Redis这些名词的时候，心里总是有几分敬畏，觉得很有感觉。那反向代理服务器有哪些呢？ 比较知名的是Nginx，网站用的什么服务器处理请求，有些网站可以在响应中看到，我们首先看下掘金有没有声明自己用的是什么服务器处理的请求: 

![image-20230323131911755](C:\Users\chenxingke\AppData\Roaming\Typora\typora-user-images\image-20230323131911755.png)

是Nginx，本来我以为反向代理服务器会使用一个，我扒了扒其他请求，发现不是:

![image-20230323132200003](C:\Users\chenxingke\AppData\Roaming\Typora\typora-user-images\image-20230323132200003.png)

这个Tengine是啥，我们搜索一下:

![image-20230323132413714](C:\Users\chenxingke\AppData\Roaming\Typora\typora-user-images\image-20230323132413714.png)

原来是基于Nginx的HTTP服务器，我们进他的官网看看:

![image-20230323132523824](C:\Users\chenxingke\AppData\Roaming\Typora\typora-user-images\image-20230323132523824.png)

在原来Nginx的基础上，针对大访问量网站的需求，添加了很多高级功能和特性。那可以理解为Nginx Plus嘛，那我们自然就会有一个问题，既然Tengine比Nginx更强大，为什么没有取代Nginx呢?  写到这里，突然想起一句话，物竞天择，适者生存。不是更强大的完全就能完全取代弱的，合适的，适合环境的，自然能够生存下来。 bing还贴心的搜出了下面这个问题:

>  既然 Tengine 比 Nginx 更强大，为什么没有取代 Nginx 呢？

这个问题来自于知乎，下面节选一下我认为比较不错的回答:

> **开源软件的模式从来不是谁取代谁，而是百花齐放，博采众长。若意见不合，也欢迎自立门户。**

写到这里，想起知乎之前看到的一个问题: 

> Cloudflare弃用NGINX，改用Rust编写的Pingora，你怎么看？

还真是白花齐放哈，又来了一个Pingora，对于Pingora，我们目前就姑且理解到另一个反向代理服务器吧，关于Cloudflare为什么用Pingora取代Rust，参看下面这篇文章:  https://blog.cloudflare.com/zh-cn/how-we-built-pingora-the-proxy-that-connects-cloudflare-to-the-internet-zh-cn/。

在上面的知乎问答中，我又看到了其他方向代理服务器:

- BFE 

> BFE (Beyond Front End) 是百度开源的现代化、企业级的七层负载均衡系统。 使用Go编写。

对应的文章参看:  为什么BFE可以取代Nginx：十问十答 https://zhuanlan.zhihu.com/p/533272410

- Higress

> Higress 是基于阿里内部两年多的 Envoy Gateway 实践沉淀，以开源 Istio 与 Envoy 为核心构建的下一代云原生网关。Higress 实现了安全防护网关、流量网关、微服务网关三层网关合一，可以显著降低网关的部署和运维成本。

这些只做了解，我们的重头戏还在Nginx上。

## Nginx 入门



### 安装



### 反向代理





### 负载均衡





## 总结一下







## 参考资料

- Why is Nginx called a “reverse” proxy?  https://blog.bytebytego.com/p/ep25-proxy-vs-reverse-proxy#%C2%A7why-is-nginx-called-a-reverse-proxy
- 为什么BFE可以取代Nginx？ https://zhuanlan.zhihu.com/p/528485453
