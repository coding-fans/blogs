# QUIC 协议

*QUIC* ，即 **快速UDP网络连接** ( *Quick UDP Internet Connections* )， 是由 *Google* 提出的实验性网络传输协议 ，位于 *OSI* 模型传输层。 *QUIC* 旨在解决 *TCP* 协议的缺陷，并最终替代 *TCP* 协议， 以减少数据传输，降低连接建立延迟时间，加快网页传输速度。

> 欢迎加入我们的技术交流群：**Linux网络编程** ([183196643](https://shang.qq.com/wpa/qunwpa?idkey=f1df6eab15cbb7480cca3ab43b5d1d8faab3a2d3ef3e52179eeea69a6b6a4b2d))

*QUIC* 主要特点有：

- 多流设计；
- 低等待延迟；
- 加密性能更优；
- 前向纠错；
- 应用程序实现；
- 连接保持；

## 多流设计

采用 **多路复用** 思想，一个连接可以同时承载多个 **流** ( *stream* )，同时发起多个请求。 请求间完全 **独立** ，某个请求阻塞甚至报文出错均不影响其他请求。

对比 *HTTP* 长连接，由于 *TCP* 是只实现一个字节流，如果请求阻塞，新请求无法发起。

## 低等待延迟

先考察典型的 *TLS* 连接建立过程：

![](https://linux-network-programming.readthedocs.io/zh_CN/latest/_images/b6c3265b63fd50ce949c3df89a6326d2.png)

1. 首先，执行三次握手，建立 *TCP* 连接(蓝色部分)；
1. 然后，执行 *TLS* 握手，建立 *TLS* 连接(黄色部分)；
1. 此后开始传输业务数据；

客户端和服务器之间要进行好几轮协议交互，才能建立 *TLS* 连接，延迟相当严重。 平时访问 *https* 网站明显比 *http* 网站慢，三次握手和 *TLS* 握手难辞其咎。

> **注解:**
>
> 注意到，三次握手中的 *ACK* 包与 *ClientHello* 合并在一起发送。 这是 *TCP* 实现中使用的 **延迟确认** 技术，旨在减少协议开销，改善网络性能。
>
> 那么，能不能将 *TCP* 三次握手和 *TLS* 合并起来呢？ 如果客户端在发送 *SYN* 包的同时将 *ClientHello* 一起发给服务端； 服务端响应 *SYN/ACK* 包时也带上相关 *TLS* 握手包，那么将可以节省一起往返时间！
>
> 这便是 *QUIC* 降低延迟的思路，但在 *SYN* 集成其他协议协议控制已然不可能。 因此， *QUIC* 采用基于 *UDP* 协议之上，在应用程序内实现传输控制协议的方案。

![](https://linux-network-programming.readthedocs.io/zh_CN/latest/_images/a1d7e443d6b493c707e5261876d31f2d.png)

## 加密性能更优

新的安全机制比 *TLS* 性能更好，而且具有各种攻击防御策略。

# 前向纠错

*TCP* 采用 **重传** 机制，而 *QUIC* 采用 **纠错** 机制。

*TCP* 发生丢包时，需要一个等待延时判断发生了丢包，然后再启动重传机制，这个过程会造成一定的阻塞，影响传输时间。

而 *QUIC* 则采用一种更主动的方案，有点类似 *RAID5* ，每 *n* 个包额外发一个 **校验和包** 。 如果这 *n* 个包中丢了一个包，可以通过其他包和校验和恢复出来，完全不需要重传。

## 应用程序内实现

*QUIC* 直接基于客户端(应用进程)实现，而非基于内核，可以快速迭代更新，不需要操作系统层面的改造，部署灵活。 这也是不使用基于迭代升级 *TCP* 方案的原因 —— *TCP* 在操作系统内核中实现，很难进行大规模调整以及推广。

## 连接保持

*QUIC* 在客户端保存连接标识，当客户端 *IP* 或者端口发生变化时，可以快速恢复连接 —— 客户端以标识请求服务端，服务端验证标识后感知客户端新地址端口并重新关联，继续通讯。 这对于改善移动端应用连接体验意义重大(从 *WiFi* 切换到流量)。

## 附录

更多 *网络编程* 技术文章请访问：[Linux网络编程](https://network.fasionchan.com/)，转至 [原文](https://network.fasionchan.com/zh_CN/latest/protocols/quic.html) 可获得最佳阅读体验。

订阅更新，获取更多学习资料，请关注我们的 [微信公众号](https://nodejs.fasionchan.com/zh_CN/latest/about/contact.html#wechat-mp) ：

![小菜学编程](https://cdn.fasionchan.com/coding-fan-wechat-soso-qrcode.png?x-oss-process=image/resize,w_640)