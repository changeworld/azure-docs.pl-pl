---
title: Atrybuty zabezpieczeń dla usługi Azure VPN Gateway
description: Lista kontrolna atrybutów zabezpieczeń służących do oceny VPN Gateway platformy Azure
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: c355f70975f441609304a4c9ee2ead75f0e0ce25
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444599"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Atrybuty zabezpieczeń dla usługi Azure VPN Gateway

W tym artykule opisano atrybuty zabezpieczeń wbudowane w usługę Azure VPN Gateway.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | ND | Dane klienta dotyczące tranzytowej bramy sieci VPN nie przechowują danych klienta |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Usługa VPN Gateway szyfruje pakiety klienta między bramami sieci VPN platformy Azure i lokalnymi urządzeniami sieci VPN (S2S) lub klientami sieci VPN (P2S). Bramy VPN obsługują również szyfrowanie między sieciami wirtualnymi. |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Nie | Klucze wstępne określone przez klienta są szyfrowane w stanie spoczynku; ale nie jest to jeszcze zintegrowane z CMK. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | Za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml) i https  |

## <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| ND | |
| Obsługa iniekcji sieci wirtualnej| ND | . |
| Izolacja sieci i obsługa zapór| Tak | Bramy sieci VPN to dedykowane wystąpienia maszyn wirtualnych dla poszczególnych klientów Virtual Network  |
| Obsługa tunelowania wymuszonego| Tak |  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Zobacz [dzienniki/](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & alerty diagnostyki Azure monitor[Azure monitor metryki/alerty](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) zarządzania usługą i konfigurowania bramy sieci VPN platformy Azure. |
| Authorization| Yes | Obsługa autoryzacji za pośrednictwem [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes | Azure Resource Manager dziennik aktywności. |
| Rejestrowanie i inspekcja płaszczyzny danych | Tak | [Azure monitor dzienników diagnostycznych](../azure-resource-manager/resource-group-audit.md) dotyczących rejestrowania i inspekcji łączności sieci VPN. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes | W przypadku operacji zarządzania stanem konfiguracji bramy sieci VPN platformy Azure można wyeksportować jako szablon Azure Resource Manager i wersję z biegiem czasu. | 