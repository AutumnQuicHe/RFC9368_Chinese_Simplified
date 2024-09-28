---
title: "6. 关于应用层协议的考虑"
anchor: "6_Application_Layer_Protocol_Considerations"
weight: 600
rank: "h1"
---

客户端创建QUIC连接是为了使用应用层协议。
因此，在考虑哪些版本兼容时，客户端只会考虑那些支持其预定使用的其中一个应用层协议的版本。
如果客户端首飞推荐了多个应用层协议协商（ALPN<sup>《[ALPN](#ALPN)》</sup>）令牌及多个兼容版本，那么可能存在某些应用层协议无法在所提供的兼容版本上运行。
服务端负责选择一个且只选择一个可以运行选定的兼容QUIC版本的ALPN令牌。

给定ALPN令牌{{< req_level MUST_NOT >}}能用于其最初定义的QUIC版本之外其他新的QUIC版本，除非满足下述所有要求：

- 新QUIC版本支持该应用层协议所需的传输特性；
- 新QUIC版本支持ALPN；
- 新QUIC版本兼容ALPN令牌最初定义的QUIC版本。

当使用不兼容版本协商时，为响应收到的版本协商数据包而创建的第二个连接{{< req_level MUST >}}重启其应用层协议协商进程，且忽略初始版本。
