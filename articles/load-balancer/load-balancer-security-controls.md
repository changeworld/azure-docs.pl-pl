---
title: Mechanizmy kontroli zabezpieczeń dla modułu równoważenia obciążenia platformy Azure
description: Lista kontrolna kontroli zabezpieczeń do oceny modułu równoważenia obciążenia
services: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: allensu
ms.openlocfilehash: 6043e574697489b6566641c352bc21a2b6d87f51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74214902"
---
# <a name="security-controls-for-azure-load-balancer"></a>Mechanizmy kontroli zabezpieczeń dla modułu równoważenia obciążenia platformy Azure

W tym artykule dokumentuje kontrolki zabezpieczeń wbudowane w moduł równoważenia obciążenia platformy Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Nie dotyczy | |
| Obsługa wtrysku sieci wirtualnej| Nie dotyczy | |
| Obsługa izolacji sieci i zapory sieciowej| Nie dotyczy |  |
| Wymuszone wsparcie tunelowania| Nie dotyczy | |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | Zobacz [Dzienniki usługi Azure Monitor dla publicznego podstawowego modułu równoważenia obciążenia](load-balancer-monitor-log.md). |
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania| Tak | Zobacz [Dzienniki usługi Azure Monitor dla publicznego podstawowego modułu równoważenia obciążenia](load-balancer-monitor-log.md). |
| Rejestrowanie i inspekcja płaszczyzny danych | Nie dotyczy |  |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| Nie dotyczy |  |
| Autoryzacja| Nie dotyczy |  |

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft | Nie dotyczy | |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Nie dotyczy | |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie dotyczy | |
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy | |
| Szyfrowane wywołania interfejsu API| Tak | Za pośrednictwem [usługi Azure Resource Manager](../azure-resource-manager/index.yml). |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Nie dotyczy |  | 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../security/fundamentals/security-controls.md)