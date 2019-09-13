---
title: Kontrolki zabezpieczeń dla usługi Azure VPN Gateway
description: Lista kontrolna zabezpieczeń na potrzeby oceny VPN Gateway platformy Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 1babb892063da6d460ea2bc4c567da954731956f
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886519"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Kontrolki zabezpieczeń dla usługi Azure VPN Gateway

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w usługę Azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| ND | |
| Obsługa iniekcji sieci wirtualnej| ND | |
| Izolacja sieci i obsługa zapór| Tak | Bramy sieci VPN to dedykowane wystąpienia maszyn wirtualnych dla poszczególnych klientów Virtual Network  |
| Obsługa tunelowania wymuszonego| Tak |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Zobacz [dzienniki/](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & alerty diagnostyki Azure monitor[Azure monitor metryki/alerty](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Azure Resource Manager dziennik aktywności. |
| Rejestrowanie i inspekcja płaszczyzny danych | Tak | [Azure monitor dzienników diagnostycznych](../azure-resource-manager/resource-group-audit.md) dotyczących rejestrowania i inspekcji łączności sieci VPN. |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) zarządzania usługą i konfigurowania bramy sieci VPN platformy Azure. |
| Authorization| Tak | Obsługa autoryzacji za pośrednictwem [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: Klucze zarządzane przez firmę Microsoft | ND | Dane klienta dotyczące tranzytowej bramy sieci VPN nie przechowują danych klienta |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Usługa VPN Gateway szyfruje pakiety klienta między bramami sieci VPN platformy Azure i lokalnymi urządzeniami sieci VPN (S2S) lub klientami sieci VPN (P2S). Bramy VPN obsługują również szyfrowanie między sieciami wirtualnymi. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie | Klucze wstępne określone przez klienta są szyfrowane w stanie spoczynku; ale nie jest to jeszcze zintegrowane z CMK. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | Za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml) i https  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | W przypadku operacji zarządzania stanem konfiguracji bramy sieci VPN platformy Azure można wyeksportować jako szablon Azure Resource Manager i wersję z biegiem czasu. | 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).