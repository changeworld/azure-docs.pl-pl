---
title: Kontrolki zabezpieczeń dla sieci szkieletowej usług Azure
description: Dowiedz się więcej o kontrolach zabezpieczeń dla sieci szkieletowej usług Azure. Zawiera listę kontrolną wbudowanych zabezpieczeń.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: a8bb49e20ec5812a4882966c6918cf2bd59f36a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645433"
---
# <a name="security-controls-for-azure-service-fabric"></a>Kontrolki zabezpieczeń dla sieci szkieletowej usług Azure

Ten artykuł dokumentuje kontrolki zabezpieczeń wbudowane w usługę Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Sieć)

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak |  |
| Obsługa wtrysku sieci wirtualnej| Tak |  |
| Obsługa izolacji sieci i zaporowania| Tak | Korzystanie z grup zabezpieczeń sieciowych (NSG). |
| Wymuszone wsparcie tunelowania| Tak | Sieć platformy Azure zapewnia wymuszone tunelowanie. |

## <a name="monitoring--logging"></a>Monitorowanie & rejestrowania

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa monitorowania platformy Azure (analiza dzienników, wgląd w aplikacje itp.)| Tak | Korzystanie z pomocy technicznej monitorowania platformy Azure i pomocy technicznej innych firm. |
| Rejestrowanie i audyt płaszczyzny kontroli i zarządzania| Tak | Wszystkie operacje płaszczyzny sterowania przebiegają przez procesy inspekcji i zatwierdzania. |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie dotyczy | Klient jest właścicielem klastra.  |

## <a name="identity"></a>Tożsamość

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Uwierzytelnianie| Tak | Uwierzytelnianie odbywa się za pośrednictwem usługi Azure Active Directory. |
| Autoryzacja| Tak | Zarządzanie tożsamością i dostępem (IAM) dla połączeń za pośrednictwem protokołu SFRP. Wywołania bezpośrednio do punktu końcowego klastra obsługuje dwie role: użytkownik i administrator. Klient może mapować interfejsy API do każdej roli. |

## <a name="data-protection"></a>Ochrona danych

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Klient jest właścicielem klastra i zestawu skalowania maszyny wirtualnej, na którym jest zbudowany klaster. Szyfrowanie dysków platformy Azure można włączyć w zestawie skalowania maszyny wirtualnej. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak | Klient jest właścicielem klastra i zestawu skalowania maszyny wirtualnej, na którym jest zbudowany klaster. Szyfrowanie dysków platformy Azure można włączyć w zestawie skalowania maszyny wirtualnej. |
| Szyfrowanie na poziomie kolumny (usługi Azure Data Services)| Nie dotyczy |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie usługi ExpressRoute, szyfrowanie w sieci wirtualnej i szyfrowanie sieci wirtualnej wirtualnej)| Tak |  |
| Szyfrowane wywołania interfejsu API| Tak | Wywołania interfejsu API sieci szkieletowej usług są nawiązywają za pośrednictwem usługi Azure Resource Manager. Wymagany jest prawidłowy token internetowy JSON (JWT). |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola bezpieczeństwa | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (przechowywanie wersji konfiguracji itp.)| Tak | |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych zabezpieczeniach w usługach platformy Azure.](../security/fundamentals/security-controls.md)