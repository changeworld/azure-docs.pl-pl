---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e84a77629026bb8885a48b8ed928699825f07115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77111204"
---
| **Dostawca** | **Rodzina urządzeń** | **Wersja oprogramowania układowego** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (wersja zapoznawcza)|
|Cisco | ASA | ASA ( * ) RouteBased (IKEv2- No BGP) dla ASA poniżej 9,8 |
|Cisco | ASA | ASA RouteBased (IKEv2 - No BGP) dla ASA 9.8+ |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x Routbased BGP |
|Ubiquiti| EdgeRouter (wychocie krawędzi)| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter (wychocie krawędzi)| EdgeOS v1.10x Routbased BGP|

> [!NOTE]
> ( * ) Wymagane: NarrowAzureTrafficSelectors (włącz opcję UsePolicyBasedTrafficSelectors) i CustomAzurePolicies (IKE/IPsec)
>
