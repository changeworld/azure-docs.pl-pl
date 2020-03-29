---
title: Mechanizmy kontroli zabezpieczeń usługi Azure Event Hubs
description: Ten artykuł zawiera listę kontrolną zabezpieczeń do oceny usługi Azure Event Hubs (sieć, tożsamość, ochrona danych itp.).
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309510"
---
# <a name="security-controls-for-azure-event-hubs"></a>Mechanizmy kontroli zabezpieczeń usługi Azure Event Hubs

Ten artykuł dokumentuje mechanizmy kontroli zabezpieczeń wbudowane w usługi Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Obsługa punktu końcowego usługi| Tak |  |  |
| Obsługa wtrysku sieci wirtualnej| Nie | |  |
| Obsługa izolacji sieci i zaporowania| Tak |  |  |
| Wymuszone wsparcie tunelowania| Nie |  |  |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | |  |
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania| Tak |  |  |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak |   |  |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Uwierzytelnianie| Tak | | [Autoryzuj dostęp do usługi Azure Event Hubs](authorize-access-event-hubs.md), [Autoryzuj dostęp do zasobów Centrum zdarzeń przy użyciu usługi Azure Active Directory](authorize-access-azure-active-directory.md), [Autoryzowanie dostępu do zasobów Centrum zdarzeń przy użyciu podpisów dostępu współdzielonego](authorize-access-shared-access-signature.md) |
| Autoryzacja|  Tak | | [Uwierzytelnij tożsamość zarządzaną za pomocą usługi Azure Active Directory](authenticate-managed-identity.md)w celu uzyskania dostępu do zasobów usługi Event Hubs , [uwierzytelnij aplikację za pomocą usługi Azure Active Directory w celu uzyskania dostępu do zasobów usługi Event Hubs](authenticate-application.md), [uwierzytelnij dostęp do zasobów centrum zdarzeń przy użyciu sygnatur dostępu współdzielonego (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft |  Tak | |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak. Dostępne dla dedykowanych klastrów. | Klucz zarządzany przez klienta w usłudze Azure KeyVault może służyć do szyfrowania danych w Centrum zdarzeń w spoczynku. | [Konfigurowanie kluczy zarządzanych przez klienta do szyfrowania danych usługi Azure Event Hubs w stanie spoczynku przy użyciu witryny Azure portal](configure-customer-managed-key.md) |
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy | |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Tak | |  |
| Szyfrowane wywołania interfejsu API| Tak |  |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Tak | |  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../security/fundamentals/security-controls.md)
