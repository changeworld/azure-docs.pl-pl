---
title: 'Azure ExpressRoute: kontrolki zabezpieczeń'
description: Lista kontrolna zabezpieczeń na potrzeby oceny usługi Azure ExpressRoute
services: expressroute
ms.service: expressroute
author: msmbaldwin
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: a288b44c07bc2df8529f07264dcee648f3af379a
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74079926"
---
# <a name="security-controls-for-azure-expressroute"></a>Kontrolki zabezpieczeń dla usługi Azure ExpressRoute

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w usługę Azure ExpressRoute.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Nie dotyczy |  |
| Obsługa iniekcji sieci wirtualnej| Nie dotyczy | |
| Izolacja sieci i obsługa zapór| Yes | Każdy klient jest zawarty we własnej domenie routingu i tunelowany do własnej sieci wirtualnej |
| Obsługa tunelowania wymuszonego| Nie dotyczy | Za pośrednictwem Border Gateway Protocol (BGP). |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Yes | Zobacz [monitorowanie ExpressRoute, metryki i alerty](expressroute-monitoring-metrics-alerts.md).|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes |  |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie |   |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Konto usługi dla bramy dla firmy Microsoft (GWM) (kontroler); Dostęp just in Time (JIT) do deweloperów i operacji. |
| Autoryzacja|  Yes |Konto usługi dla bramy dla firmy Microsoft (GWM) (kontroler); Dostęp just in Time (JIT) do deweloperów i operacji. |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft |  Nie dotyczy | ExpressRoute nie przechowuje danych klienta. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie dotyczy |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Nie dotyczy | |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Nie | |
| Wywołania interfejsu API są szyfrowane| Yes | Za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml) i https. |


## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes | Za pośrednictwem dostawcy zasobów sieciowych (NRP). |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).