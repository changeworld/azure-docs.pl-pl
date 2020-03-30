---
title: Mechanizmy kontroli zabezpieczeń bramy sieci VPN platformy Azure
description: Lista kontrolna zabezpieczeń do oceny bramy sieci VPN platformy Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: cdf616b29a93e786ef26af83b5d3b3541f94d67c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972279"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Mechanizmy kontroli zabezpieczeń bramy sieci VPN platformy Azure

Ten artykuł dokumentuje mechanizmy kontroli zabezpieczeń wbudowane w usługę Azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Nie dotyczy | |
| Obsługa wtrysku sieci wirtualnej| Nie dotyczy | |
| Obsługa izolacji sieci i zapory sieciowej| Tak | Bramy sieci VPN są dedykowanymi wystąpieniami maszyn wirtualnych dla każdej sieci wirtualnej klienta  |
| Wymuszone wsparcie tunelowania| Tak |  |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | Zobacz [Dzienniki diagnostyczne usługi Azure Monitor/alert](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Metryki/alerty usługi Azure Monitor](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).  |
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania| Tak | Dziennik aktywności usługi Azure Resource Manager. |
| Rejestrowanie i inspekcja płaszczyzny danych | Tak | [Dzienniki diagnostyczne usługi Azure Monitor](../azure-resource-manager/management/view-activity-logs.md) do rejestrowania i inspekcji łączności sieci VPN. |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| Tak | [Usługa Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) do zarządzania usługą i konfigurowania bramy sieci VPN platformy Azure. |
| Autoryzacja| Tak | Autoryzacja wsparcia za pośrednictwem [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft | Nie dotyczy | Brama sieci VPN przesyła dane klienta, NIE przechowuje danych klientów |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Tak | Brama sieci VPN szyfruje pakiety klientów między bramami sieci VPN platformy Azure a lokalnymi urządzeniami sieci VPN (S2S) lub klientami sieci VPN (P2S). Bramy sieci VPN obsługują również szyfrowanie sieci wirtualnej do sieci wirtualnej. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie | Klucze wstępnie udostępnione określone przez klienta są szyfrowane w spoczynku; ale nie jest jeszcze zintegrowany z CMK. |
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy | |
| Szyfrowane wywołania interfejsu API| Tak | Za pośrednictwem [usługi Azure Resource Manager](../azure-resource-manager/index.yml) i HTTPS  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Tak | W przypadku operacji zarządzania stan konfiguracji bramy sieci VPN platformy Azure można wyeksportować jako szablon usługi Azure Resource Manager i wersjonować w czasie. |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../security/fundamentals/security-controls.md)
