---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 78dfd57fba6365f9c8937b30b5cf96b840749c68
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2019
ms.locfileid: "54211887"
---
| **Dostawca** | **Rodzina urządzeń** | **Wersja oprogramowania układowego** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (wersja zapoznawcza)|
|Cisco | ASA | RouteBased ASA (*) (IKEv2 No BGP) asa poniżej 9,8 |
|Cisco | ASA | RouteBased ASA (protokół IKEv2 — nie protokołu BGP) asa 9,8 + |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased protokołu BGP |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| RouteBased v1.10x EdgeOS protokołu BGP|

> [!NOTE]
> ( * ) Wymagane: NarrowAzureTrafficSelectors (Włącz opcję UsePolicyBasedTrafficSelectors) i CustomAzurePolicies (IKE/IPsec)
>
