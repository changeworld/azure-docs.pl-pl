---
title: Wspólne atrybuty zabezpieczeń dla usługi Azure ExpressRoute
description: Lista kontrolna typowych atrybutów zabezpieczeń do oceny usługi Azure ExpressRoute
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: d6156715fb87831d465197fd8eec59d245221e48
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083277"
---
# <a name="common-security-attributes-for-azure-expressroute"></a>Wspólne atrybuty zabezpieczeń dla usługi Azure ExpressRoute

Zabezpieczenia są zintegrowane w każdy aspekt usługi platformy Azure. W tym artykule opisano typowe atrybuty zabezpieczeń wbudowanych w usługę Azure ExpressRoute.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>|  ND | Usługa ExpressRoute nie przechowuje danych klienta. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Nie | |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| ND |  |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND | |
| Wywołania interfejsu API szyfrowane| Tak | Za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml) i HTTPS. |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| ND |  |
| Obsługa iniekcji sieci wirtualnej| ND | |
| Izolacja sieci i zapory pomocy technicznej| Yes | Każdy klient jest zawarty w własnej domeny routingu i tunelowane do własnej sieci wirtualnej |
| Obsługa tunelowania wymuszonego| ND | Za pośrednictwem protokołu Border Gateway Protocol (BGP). |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | Zobacz [usługi ExpressRoute, monitorowanie, alerty i metryki](expressroute-monitoring-metrics-alerts.md).|

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Konto usługi bramy dla firmy Microsoft (GWM) (kontroler); Dostęp just in Time (JIT) do dla deweloperów i OP. |
| Autoryzacja|  Tak |Konto usługi bramy dla firmy Microsoft (GWM) (kontroler); Dostęp just in Time (JIT) do dla deweloperów i OP. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi| 
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Yes |  |
| Rejestrowanie płaszczyzny danych i inspekcji| Nie |   |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak | Za pomocą dostawcy zasobów sieciowych (NRP). |
