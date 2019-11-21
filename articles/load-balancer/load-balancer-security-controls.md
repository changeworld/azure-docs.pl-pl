---
title: Security controls for Azure Load Balancer
description: A checklist of security controls for evaluating Load Balancer
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74214902"
---
# <a name="security-controls-for-azure-load-balancer"></a>Security controls for Azure Load Balancer

This article documents the security controls built into Azure Load Balancer.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Security control | Tak/Nie | Uwagi |
|---|---|--|
| Service endpoint support| ND | |
| VNet injection support| ND | |
| Network Isolation and Firewalling support| ND |  |
| Forced tunneling support| ND | |

## <a name="monitoring--logging"></a>Monitoring & logging

| Security control | Tak/Nie | Uwagi|
|---|---|--|
| Azure monitoring support (Log analytics, App insights, etc.)| Tak | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Control and management plane logging and audit| Tak | See [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md). |
| Data plane logging and audit | ND |  |

## <a name="identity"></a>Tożsamość

| Security control | Tak/Nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| ND |  |
| Autoryzacja| ND |  |

## <a name="data-protection"></a>Ochrona danych

| Security control | Tak/Nie | Uwagi |
|---|---|--|
| Server-side encryption at rest: Microsoft-managed keys | ND | |
| Encryption in transit (such as ExpressRoute encryption, in VNet encryption, and VNet-VNet encryption )| ND | |
| Server-side encryption at rest: customer-managed keys (BYOK) | ND | |
| Column level encryption (Azure Data Services)| ND | |
| API calls encrypted| Tak | Via the [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Security control | Tak/Nie | Uwagi|
|---|---|--|
| Configuration management support (versioning of configuration, etc.)| ND |  | 

## <a name="next-steps"></a>Następne kroki

- Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).