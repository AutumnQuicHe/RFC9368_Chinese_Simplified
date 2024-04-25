---
title: "4. 版本降级防范"
anchor: "4_Version_Downgrade_Prevention"
weight: 400
rank: "h1"
---


版本降级是一种由恶意实体设法使QUIC终端协商出的版本不同于其在不受该攻击情况下协商得到的版本的攻击。
本文描述的机制是为了防范降级攻击而设计的。

客户端{{< req_level MUST >}}忽略任何包含初始版本的版本协商包。
试图基于从版本协商包得到的信息创建新的连接的客户端{{< req_level MUST >}}忽略所有其收到的用于回应该连接的版本协商包。

握手期间，双端{{< req_level MUST >}}解析对端发送的版本信息。
如果解析失败（例如，版本信息太短或其长度不能被4整除），终端{{< req_level MUST >}}关闭连接；如果连接使用QUIC版本1，{{< req_level MUST >}}使用类型为`TRANSPORT_PARAMETER_ERROR`（`传输参数错误`）的传输错误关闭连接。
如果终端收到的选定版本为零，或任何可选版本为零，{{< req_level MUST >}}视为解析错误。
如果服务端收到的版本信息中的可选版本不包含选定版本，也{{< req_level MUST >}}视为解析错误。

任何支持版本协商的QUIC版本{{< req_level MUST >}}定义一种以版本协商错误关闭连接的。
对于QUIC版本1，版本协商错误通过`VERSION_NEGOTIATION_ERROR`类型传输错误（详见[第10.2章]()）进行标识。

当服务端收到客户端的首飞，服务端将首先确定该连接使用的QUIC版本，从而正确解析首飞。
这可能涉及检查握手记录之外的数据，例如部分包头。
当服务端解析完客户端的版本信息时，{{< req_level MUST >}}校验客户端选定版本是否与连接当前使用版本一致。
如果两者不一致，服务端{{< req_level MUST >}}以版本协商错误为由关闭连接。

In the specific case of QUIC version 1, the server determines that version 1 is in use by observing that the Version field of the first Long Header packet it receives is set to 0x00000001. Subsequently, if the server receives the client's Version Information over QUIC version 1 (as indicated by the Version field of the Long Header packets that carried the transport parameters) and the client's Chosen Version is not set to 0x00000001, the server MUST close the connection with a version negotiation error.

Servers MAY complete the handshake even if the Version Information is missing. Clients MUST NOT complete the handshake if they are reacting to a Version Negotiation packet and the Version Information is missing, but MAY do so otherwise.

If a client receives Version Information where the server's Chosen Version was not sent by the client as part of its Available Versions, the client MUST close the connection with a version negotiation error. If a client has reacted to a Version Negotiation packet and the server's Version Information was missing, the client MUST close the connection with a version negotiation error.

If the client received and acted on a Version Negotiation packet, the client MUST validate the server's Available Versions field. The Available Versions field is validated by confirming that the client would have attempted the same version with knowledge of the versions the server supports. That is, the client would have selected the same version if it received a Version Negotiation packet that listed the versions in the server's Available Versions field, plus the Negotiated Version. If the client would have selected a different version, the client MUST close the connection with a version negotiation error. In particular, if the client reacted to a Version Negotiation packet and the server's Available Versions field is empty, the client MUST close the connection with a version negotiation error. These connection closures prevent an attacker from being able to use forged Version Negotiation packets to force a version downgrade.

As an example, let's assume a client supports hypothetical QUIC versions 10, 12, and 14 with a preference for higher versions. The client initiates a connection attempt with version 12. Let's explore two independent example scenarios:

In the first scenario, the server supports versions 10, 13, and 14, but only 13 and 14 are Fully Deployed (see Section 5). The server sends a Version Negotiation packet with versions 10, 13, and 14. This triggers an incompatible version negotiation, and the client initiates a new connection with version 14. Then, the server's Available Versions field contains 13 and 14. In that scenario, the client would have also picked 14 if it had received a Version Negotiation packet with versions 13 and 14; therefore, the handshake succeeds using Negotiated Version 14.
In the second scenario, the server supports versions 10, 13, and 14, and they are all Fully Deployed. However, the attacker forges a Version Negotiation packet with versions 10 and 13. This triggers an incompatible version negotiation, and the client initiates a new connection with version 10. Then, the server's Available Versions field contains 10, 13, and 14. In that scenario, the client would have picked 14 instead of 10 if it had received a Version Negotiation packet with versions 10, 13, and 14; therefore, the client aborts the handshake with a version negotiation error.
This validation of Available Versions is not sufficient to prevent downgrade. Downgrade prevention also depends on the client ignoring Version Negotiation packets that contain the Original Version (see Section 2.1).

After the process of version negotiation described in this document completes, the version in use for the connection is the version that the server sent in the Chosen Version field of its Version Information. That remains true even if other versions were used in the Version field of long headers at any point in the lifetime of the connection. In particular, since the client can be made aware of the Negotiated Version by the QUIC long header version during compatible version negotiation (see Section 2.3), clients MUST validate that the server's Chosen Version is equal to the Negotiated Version; if they do not match, the client MUST close the connection with a version negotiation error. This prevents an attacker's ability to influence version negotiation by forging the long header Version field.