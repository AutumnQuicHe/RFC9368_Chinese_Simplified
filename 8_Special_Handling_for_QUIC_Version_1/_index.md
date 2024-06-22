---
title: "8. QUIC第一版的特殊处理"
anchor: "8_Special_Handling_for_QUIC_Version_1"
weight: 800
rank: "h1"
---

Because QUIC version 1 was the only QUIC version that was published on the IETF Standards Track before this document, it is handled specially as follows: if a client is starting a QUIC version 1 connection in response to a received Version Negotiation packet and the version_information transport parameter is missing from the server's transport parameters, then the client SHALL proceed as if the server's transport parameters contained a version_information transport parameter with a Chosen Version set to 0x00000001 and an Available Version list containing exactly one version set to 0x00000001. This allows version negotiation to work with servers that only support QUIC version 1. Note that implementations that wish to use version negotiation to negotiate versions other than QUIC version 1 MUST implement the version negotiation mechanism defined in this document.
