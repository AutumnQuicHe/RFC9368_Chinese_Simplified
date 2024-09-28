---
title: "8. QUIC版本1的特殊处理"
anchor: "8_Special_Handling_for_QUIC_Version_1"
weight: 800
rank: "h1"
---

因为QUIC版本1是本文发布前唯一以IETF标准发布的QUIC协议，所以它被特殊处理如下：
如果客户端正在发起一条QUIC版本1的连接响应接收到的版本协商包，且服务端的传输参数中缺失`version_information`参数，那么客户端{{< req_level SHALL >}}假定服务端传输参数包含`version_information`字段且其中包含值为`0x00000001`的选定版本以及包含版本集中恰好由`0x00000001`组成的可选版本列表。
从而得以与只支持QUIC版本1的服务端进行版本协商。
注意，希望通过版本协商来协商除QUIC版本1之外的版本的QUIC实现{{< req_level MUST >}}实现本文定义的版本协商机制。
