---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 00889717e0c22477b9933725bccc7de05c82bc4f
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454432"
---
| **Dostawca** | **Rodzina urządzeń** | **Wersja oprogramowania układowego** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (wersja zapoznawcza)|
|Cisco | ASA | RouteBased ASA (*) (IKEv2 No BGP) asa poniżej 9,8 |
|Cisco | ASA | RouteBased ASA (protokół IKEv2 — nie protokołu BGP) asa 9,8 + |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| RouteBased v1.10x EdgeOS protokołu BGP|

> [!NOTE]
> (*) Wymagane: NarrowAzureTrafficSelectors (Włącz opcję UsePolicyBasedTrafficSelectors) i CustomAzurePolicies (IKE/IPsec)
>
