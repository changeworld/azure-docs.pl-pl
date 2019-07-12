---
title: Atrybuty zabezpieczeń dla usługi Azure Load Balancer
description: Lista kontrolna atrybutów zabezpieczeń do oceny usługi równoważenia obciążenia
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 1892b918ed35221bc73d5070d5a73ecc359aa4c2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800083"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Atrybuty zabezpieczeń dla usługi Azure Load Balancer

W tym artykule opisano typowe atrybuty zabezpieczeń wbudowane w usłudze Azure Load Balancer.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (na przykład szyfrowanie po stronie serwera, szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta i inne funkcje szyfrowania) | ND | |
| Szyfrowanie podczas transferu (np. szyfrowanie usługi ExpressRoute w sieci wirtualnej, szyfrowania i szyfrowania sieć wirtualna-sieć wirtualna)| ND | |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| ND | |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND | |
| Wywołania interfejsu API szyfrowane| Tak | Za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| ND | |
| Obsługa iniekcji sieci wirtualnej| ND | . |
| Izolacja sieci i Firewalling pomocy technicznej| ND |  |
| Obsługa tunelowania wymuszonego| ND | |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Zobacz [usługi Azure Monitor w dziennikach publiczny podstawowy moduł równoważenia obciążenia](load-balancer-monitor-log.md). |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| ND |  |
| Authorization| ND |  |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak | Zobacz [usługi Azure Monitor w dziennikach publiczny podstawowy moduł równoważenia obciążenia](load-balancer-monitor-log.md). |
| Rejestrowanie płaszczyzny danych i inspekcji | ND |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| ND |  | 
