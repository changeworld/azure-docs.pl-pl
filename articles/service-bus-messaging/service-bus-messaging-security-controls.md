---
title: Kontrolki zabezpieczeń dla usługi Azure Service Bus Messaging
description: Lista kontrolna zabezpieczeń do oceny usługi Azure Service Bus Messaging
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75903248"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Kontrolki zabezpieczeń dla usługi Azure Service Bus Messaging

Ten artykuł dokumentuje kontrolki zabezpieczeń wbudowane w usługę Azure Service Bus Messaging.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Obsługa punktu końcowego usługi| Tak (tylko warstwa Premium) | Punkty końcowe usługi sieci wirtualnej są obsługiwane tylko dla [warstwy Usługi Usługi Usługi Premium.](service-bus-premium-messaging.md) |  |
| Obsługa wtrysku sieci wirtualnej| Nie | |  |
| Obsługa izolacji sieci i zaporowania| Tak (tylko warstwa Premium) |  |  |
| Wymuszone wsparcie tunelowania| Nie |  |  |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | Obsługiwane za pośrednictwem [usługi Azure Monitor i alerty](service-bus-metrics-azure-monitor.md). |  |
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania| Tak | Dzienniki operacji są dostępne.  | [Dzienniki diagnostyczne magistrali usług](service-bus-diagnostic-logs.md) |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie |  |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Uwierzytelnianie| Tak | Zarządzane za pośrednictwem [tożsamości usługi zarządzanej usługi Active Directory](service-bus-managed-service-identity.md)platformy Azure .| [Uwierzytelnianie i autoryzacja usługi Service Bus](service-bus-authentication-and-authorization.md). |
| Autoryzacja| Tak | Obsługuje autoryzację za pośrednictwem tokenu [RBAC](authenticate-application.md) i SAS. | [Uwierzytelnianie i autoryzacja usługi Service Bus](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft |  Tak dla szyfrowania po stronie serwera w spoczynku domyślnie. |  |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak. | Klucz zarządzany przez klienta w usłudze Azure KeyVault może służyć do szyfrowania danych w obszarze nazw usługi Service Bus w spoczynku. | [Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania danych usługi Azure Service Bus w stanie spoczynku przy użyciu witryny Azure portal](configure-customer-managed-key.md)  |
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy | |   |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Tak | Obsługuje standardowy mechanizm HTTPS/TLS. |   |
| Szyfrowane wywołania interfejsu API| Tak | Wywołania interfejsu API są nawiązywają za pośrednictwem [usługi Azure Resource Manager](../azure-resource-manager/index.yml) i HTTPS. |   |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Tak | Obsługuje przechowywanie wersji dostawcy zasobów za pośrednictwem [interfejsu API usługi Azure Resource Manager](/rest/api/resources/).|   |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../security/fundamentals/security-controls.md)
