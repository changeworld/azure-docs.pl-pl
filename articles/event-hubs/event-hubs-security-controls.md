---
title: Kontrolki zabezpieczeń dla usługi Azure Event Hubs
description: Lista kontrolna zabezpieczeń na potrzeby oceny Event Hubs platformy Azure
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 277a745d26961ed509258d5423fc3c0da9b79a24
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219408"
---
# <a name="security-controls-for-azure-event-hubs"></a>Kontrolki zabezpieczeń dla usługi Azure Event Hubs

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w usługę Azure Event Hubs.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Obsługa punktów końcowych usługi| Tak |  |  |
| Obsługa iniekcji sieci wirtualnej| Nie | |  |
| Izolacja sieci i obsługa zapór| Tak |  |  |
| Obsługa tunelowania wymuszonego| Nie |  |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | |  |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak |  |  |
| Rejestrowanie i inspekcja płaszczyzny danych| Tak |   |  |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Authentication| Tak | | Autoryzuj [dostęp do usługi Azure Event Hubs](authorize-access-event-hubs.md), [Autoryzuj dostęp do zasobów Event Hubs za pomocą Azure Active Directory](authorize-access-azure-active-directory.md), [autoryzując dostęp do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego](authorize-access-shared-access-signature.md) |
| Authorization|  Tak | | [Uwierzytelnianie zarządzanej tożsamości za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authenticate-managed-identity.md), [uwierzytelniania aplikacji za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authenticate-application.md), [uwierzytelniania dostępu do zasobów Event Hubs przy użyciu sygnatury dostępu współdzielonego (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi | Dokumentacja |
|---|---|--|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: Klucze zarządzane przez firmę Microsoft |  Tak | |  |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie |  |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | |  |
| Wywołania interfejsu API są szyfrowane| Tak |  |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi| Dokumentacja |
|---|---|--|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | |  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).
