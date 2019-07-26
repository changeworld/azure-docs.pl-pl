---
title: Atrybuty zabezpieczeń dla usługi Azure ExpressRoute
description: Lista kontrolna atrybutów zabezpieczeń do oceny usługi Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: c9a46497c18b99ad7774036fd92e63d024b47045
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442201"
---
# <a name="security-attributes-for-azure-expressroute"></a>Atrybuty zabezpieczeń dla usługi Azure ExpressRoute

W tym artykule opisano atrybuty zabezpieczeń wbudowane w usługę Azure ExpressRoute.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania)|  ND | ExpressRoute nie przechowuje danych klienta. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Nie | |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| ND |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | Za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml) i https. |

## <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| ND |  |
| Obsługa iniekcji sieci wirtualnej| ND | |
| Izolacja sieci i obsługa zapór| Yes | Każdy klient jest zawarty we własnej domenie routingu i tunelowany do własnej sieci wirtualnej |
| Obsługa tunelowania wymuszonego| ND | Za pośrednictwem Border Gateway Protocol (BGP). |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Zobacz [monitorowanie ExpressRoute, metryki i alerty](expressroute-monitoring-metrics-alerts.md).|

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Konto usługi dla bramy dla firmy Microsoft (GWM) (kontroler); Dostęp just in Time (JIT) do deweloperów i operacji. |
| Authorization|  Yes |Konto usługi dla bramy dla firmy Microsoft (GWM) (kontroler); Dostęp just in Time (JIT) do deweloperów i operacji. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi| 
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes |  |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie |   |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Za pośrednictwem dostawcy zasobów sieciowych (NRP). |
