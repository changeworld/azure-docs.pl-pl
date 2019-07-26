---
title: Atrybuty zabezpieczeń dla Azure Load Balancer
description: Lista kontrolna atrybutów zabezpieczeń do oceny Load Balancer
services: load-balancer
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbaldwin
ms.openlocfilehash: 7d40618d5f4fde4a2b3fdfbde8a6de0a049ce3b6
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68440876"
---
# <a name="security-attributes-for-azure-load-balancer"></a>Atrybuty zabezpieczeń dla Azure Load Balancer

W tym artykule opisano atrybuty zabezpieczeń wbudowane w Azure Load Balancer.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | ND | |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| ND | |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| ND | |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Yes | Za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| ND | |
| Obsługa iniekcji sieci wirtualnej| ND | . |
| Izolacja sieci i obsługa zapór| ND |  |
| Obsługa tunelowania wymuszonego| ND | |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Zapoznaj się z [dziennikami Azure monitor dla publicznych Load Balancer podstawowych](load-balancer-monitor-log.md). |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| ND |  |
| Authorization| ND |  |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Zapoznaj się z [dziennikami Azure monitor dla publicznych Load Balancer podstawowych](load-balancer-monitor-log.md). |
| Rejestrowanie i inspekcja płaszczyzny danych | ND |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| ND |  | 
