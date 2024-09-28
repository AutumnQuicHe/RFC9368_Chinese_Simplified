---
title: "3. 版本信息"
anchor: "3_Version_Information"
weight: 300
rank: "h1"
---


握手期间，终端将交换版本信息（Version Information），包括一个选定版本及一系列可选版本。
任何支持该机制的QUIC版本{{< req_level MUST >}}提供一个能够在握手期间双向交换版本信息的机制，并确保该数据得到认证。

在QUIC版本1中，版本信息通过一个`version_information`（版本信息）传输参数（详见《[QUIC传输协议](/RFC9000_Chinese_Simplified/)》[第7.4章](/RFC9000_Chinese_Simplified/#7.4_Transport_Parameters)）。
版本信息内容如下（使用《[QUIC传输协议](/RFC9000_Chinese_Simplified/)》[第1.3章](/RFC9000_Chinese_Simplified/#1.3_Notational_Conventions)所述表示法表示）：

```
版本信息 {
  选定版本 (32),
  可选版本 (32) ...,
}
```
图2：版本信息格式

各个字段内容描述如下：

选定版本（Chosen Version）：
> 发送端为连接选择的版本。
> 在大多数情况下，该字段的值等于承载该段数据的长包头中版本字段的值。
> 但是，未来的版本或扩展可以选择在长包头的版本字段设置不同的值。
>
> 可选版本字段的内容基于其发送端是客户端还是服务端。

客户端发送的可选版本（Client-Sent Available Versions）：
> 当由客户端发送的时候，可选版本字段列出所有该首飞兼容的版本，并按降序排列。
> 注意，该列表中{{< req_level MUST >}}包含选定版本字段中的版本，使得客户端得以表明偏好该选定版本；
> 且该偏好仅供参考，服务端{{< req_level MAY >}}选择自身偏好的版本取代。

服务端发送的可选版本（Server-Sent Available Versions）：
> 当由服务端发送的时候，可选版本字段列出所有当前服务端完整部署的版本（Fully Deployed Versions，详见[第5章](#5_Server_Deployments_of_QUIC)）。
> 该字段列出的版本在排序上不具有任何涵义。
> 注意，选定版本字段中的版本不要求必须包含在该列表中，因为服务端操作者可能正在移除对该版本的支持。
> 基于同样的原因，可选版本字段{{< req_level MAY >}}为空。

客户端和服务端都{{< req_level MAY >}}在其可选版本列表中包含`0x?a?a?a?a`格式的版本。
这些版本用于测试版本协商（详见《[QUIC传输协议](/RFC9000_Chinese_Simplified/)》[第15章](/RFC9000_Chinese_Simplified/#15_Versions)），且永远不会被用于连接。
