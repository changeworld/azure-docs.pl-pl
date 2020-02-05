---
title: Azure VMware Solutions (AVS) - VPN gateways
description: Learn about AVS Site-to-Site VPN and Point-to-Site VPN concepts
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 73171e2c46bdf6c934db5777efe36ba51153a686
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024861"
---
# <a name="vpn-gateways-overview"></a>VPN gateways overview

A VPN gateway is used to send encrypted traffic between an AVS region network at an on-premises location, or a computer over the public internet. Each region can have one VPN gateway, which can support multiple connections. W przypadku utworzenia wielu połączeń do tej samej bramy sieci VPN wszystkie tunele VPN współdzielą dostępną przepustowość bramy.

AVS provides two kinds of VPN gateways:

* Site-to-Site VPN gateway
* Point-to-Site VPN gateway

## <a name="site-to-site-vpn-gateway"></a>Site-to-Site VPN gateway

A Site-to-Site VPN gateway is used to send encrypted traffic between an AVS region network and an on-premises datacenter. Use this connection to define the subnets/CIDR range, for network traffic between your on-premises network and the AVS region network.

The VPN gateway allows you to consume services from on-premises on your AVS Private Cloud, and services on your AVS Private Cloud from the on-premises network. AVS provides a policy-based VPN server for establishing the connection from your on-premises network.

Use cases for Site-to-Site VPN:

* Accessibility of your AVS Private Cloud vCenter from any workstation in your on-premises network.
* Use of your on-premises Active Directory as a vCenter identity source.
* Convenient transfer of VM templates, ISOs, and other files from your on-premises resources to your AVS Private Cloud vCenter.
* Accessibility of workloads running on your AVS Private Cloud from your on-premises network.

![Site-to-Site VPN connection topology](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Cryptographic parameters

A Site-to-Site VPN connection uses the following default cryptographic parameters to establish a secure connection. When you create a connection from your on-premises VPN device, use any of the following parameters that are supported by your on-premises VPN gateway.

#### <a name="phase-1-proposals"></a>Phase 1 proposals

| Parametr | Proposal 1 | Proposal 2 | Proposal 3 |
|-----------|------------|------------|------------|
| Wersja IKE | IKEv1 | IKEv1 | IKEv1 |
| Szyfrowanie | AES 128 | AES 256 | AES 256 |
| Hash Algorithm| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman Group (DH Group) | 2 | 2 | 2 |
| Life Time | 28 800 sekund | 28 800 sekund | 28 800 sekund |
| Data Size | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Phase 2 proposals

| Parametr | Proposal 1 | Proposal 2 | Proposal 3 |
|-----------|------------|------------|------------|
| Szyfrowanie | AES 128 | AES 256 | AES 256 |
| Hash Algorithm| SHA 256 | SHA 256 | SHA 1 |
| Perfect Forward Secrecy Group (PFS Group) | Brak | Brak | Brak |
| Life Time | 1,800 seconds | 1,800 seconds | 1,800 seconds |
| Data Size | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> Set TCP MSS Clamping at 1200 on your VPN device. Or if your VPN devices do not support MSS clamping, you can alternatively set the MTU on the tunnel interface to 1240 bytes instead.

## <a name="point-to-site-vpn-gateway"></a>Point-to-Site VPN gateway

A Point-to-Site VPN is used to send encrypted traffic between an AVS region network and a client computer. Point-to-Site VPN is the easiest way to access your AVS Private Cloud network, including your AVS Private Cloud vCenter and workload VMs. Use Point-to-Site VPN connectivity if you're connecting to the AVS Private Cloud remotely.

## <a name="next-steps"></a>Następne kroki

* [Set up VPN gateway](vpn-gateway.md)
