---
title: 'Azure ExpressRoute: Mechanizmy kontroli zabezpieczeń'
description: Lista kontrolna zabezpieczeń do oceny usługi Azure ExpressRoute
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079926"
---
# <a name="security-controls-for-azure-expressroute"></a>Mechanizmy kontroli zabezpieczeń dla usługi Azure ExpressRoute

Ten artykuł dokumentuje mechanizmy zabezpieczeń wbudowane w usługę Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Nie dotyczy |  |
| Obsługa wtrysku sieci wirtualnej| Nie dotyczy | |
| Obsługa izolacji sieci i zaporowania| Tak | Każdy klient jest zawarty we własnej domenie routingu i tunelowany do własnej sieci wirtualnej |
| Wymuszone wsparcie tunelowania| Nie dotyczy | Za pośrednictwem protokołu Bramy Granicznej (BGP). |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | Zobacz [Monitorowanie, metryki i alerty usługi ExpressRoute](expressroute-monitoring-metrics-alerts.md).|
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania| Tak |  |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie |   |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| Tak | Konto usługi dla bramy dla firmy Microsoft (GWM) (kontroler); Dostęp just in Time (JIT) dla deweloperów i po. |
| Autoryzacja|  Tak |Konto usługi dla bramy dla firmy Microsoft (GWM) (kontroler); Dostęp just in Time (JIT) dla deweloperów i po. |

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft |  Nie dotyczy | Usługa ExpressRoute nie przechowuje danych klientów. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie dotyczy |  |
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy | |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Nie | |
| Szyfrowane wywołania interfejsu API| Tak | Za pośrednictwem [usługi Azure Resource Manager](../azure-resource-manager/index.yml) i HTTPS. |


## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Tak | Za pośrednictwem dostawcy zasobów sieciowych (KPR). |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../security/fundamentals/security-controls.md)