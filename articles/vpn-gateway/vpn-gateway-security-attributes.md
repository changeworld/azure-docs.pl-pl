---
title: Atrybuty zabezpieczeń dla usługi Azure VPN Gateway
description: Lista kontrolna atrybutów zabezpieczeń do oceny usługi Azure VPN Gateway
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e58e7b3f77d9bb673e300aa60ad07ca9dba5153
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083134"
---
# <a name="security-attributes-for-azure-vpn-gateway"></a>Atrybuty zabezpieczeń dla usługi Azure VPN Gateway

W tym artykule opisano typowe atrybuty zabezpieczeń wbudowane w usłudze Azure VPN Gateway.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (na przykład szyfrowanie po stronie serwera, szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta i inne funkcje szyfrowania) | ND | Dane klienta tranzyt bramy sieci VPN, nie przechowują danych klienta |
| Szyfrowanie podczas transferu (np. szyfrowanie usługi ExpressRoute w sieci wirtualnej, szyfrowania i szyfrowania sieć wirtualna-sieć wirtualna)| Tak | Brama sieci VPN szyfrowanie pakietów klienta między bramami sieci VPN Azure i klientów na lokalnymi urządzeniami sieci VPN (S2S) lub klientów sieci VPN (P2S). Bramy sieci VPN obsługują także sieć wirtualna-sieć wirtualna szyfrowania. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Nie | Określony przez klienta klucze wstępne są szyfrowane, gdy; ale nie jeszcze zintegrowane z CMK. |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND | |
| Wywołania interfejsu API szyfrowane| Tak | Za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml) i HTTPS  |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| ND | |
| Obsługa iniekcji sieci wirtualnej| ND | . |
| Izolacja sieci i Firewalling pomocy technicznej| Tak | Bramy sieci VPN są dedykowane wystąpienia maszyn wirtualnych, dla każdego klienta sieci wirtualnej  |
| Obsługa tunelowania wymuszonego| Tak |  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Zobacz [dzienników diagnostycznych monitora platformy Azure/alerty](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [alert metryk usługi Azure Monitor](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | [Usługa Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) do zarządzania usługi i skonfigurowanie bramy sieci VPN platformy Azure. |
| Autoryzacja| Yes | Obsługuje autoryzację za pośrednictwem [RBAC](../role-based-access-control/overview.md). |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak | Dziennik aktywności platformy Azure Resource Manager. |
| Rejestrowanie płaszczyzny danych i inspekcji | Tak | [Dzienniki diagnostyczne platformy Azure Monitor](../azure-resource-manager/resource-group-audit.md) dla połączenia sieci VPN, rejestrowanie i przeprowadzanie inspekcji. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Yes | Dla operacji zarządzania stan konfiguracji bramy sieci VPN platformy Azure można eksportować jako szablon usługi Azure Resource Manager i wersjonowanymi wraz z upływem czasu. | 