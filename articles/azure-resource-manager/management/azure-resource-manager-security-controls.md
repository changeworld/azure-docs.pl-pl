---
title: Mechanizmy zabezpieczeń
description: Lista kontrolna wbudowanych zabezpieczeń do oceny usługi Azure Resource Manager.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: d0a0625153e428a0d261e52d40b31ef5142eddfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485626"
---
# <a name="security-controls-for-azure-resource-manager"></a>Kontrolki zabezpieczeń usługi Azure Resource Manager

Ten artykuł dokumentuje kontrolki zabezpieczeń wbudowane w usługę Azure Resource Manager.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft | Tak |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Tak | protokół HTTPS/TLS. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie dotyczy | Usługa Azure Resource Manager nie przechowuje zawartości klienta, a tylko dane kontrolne. |
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Tak | |
| Szyfrowane wywołania interfejsu API| Tak | |

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Nie | |
| Obsługa wtrysku sieci wirtualnej| Tak | |
| Obsługa izolacji sieci i zaporowania| Nie |  |
| Wymuszone wsparcie tunelowania| Nie |  |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Nie | |
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania| Tak | Dzienniki aktywności uwidaczniają wszystkie operacje zapisu (PUT, POST, DELETE) wykonywane na zasobach; zobacz [Wyświetlanie dzienników aktywności w celu inspekcji akcji dotyczących zasobów](view-activity-logs.md). |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie dotyczy | |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| Tak | [Usługa Azure Active Directory](/azure/active-directory) na podstawie.|
| Autoryzacja| Tak | |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Tak |  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../../security/fundamentals/security-controls.md)