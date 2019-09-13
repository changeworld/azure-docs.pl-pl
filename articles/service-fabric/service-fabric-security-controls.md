---
title: Kontrolki zabezpieczeń dla usługi Azure Service Fabric
description: Lista kontrolna zabezpieczeń na potrzeby oceny Service Fabric platformy Azure
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: d62c7848588c494c8190f0d429ce2d6641928b52
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886743"
---
# <a name="security-controls-for-azure-service-fabric"></a>Kontrolki zabezpieczeń dla usługi Azure Service Fabric

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w usługę Azure Service Fabric. 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak |  |
| Obsługa iniekcji sieci wirtualnej| Tak |  |
| Izolacja sieci i obsługa zapór| Tak | Korzystanie z sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). |
| Obsługa tunelowania wymuszonego| Tak | Sieć Azure udostępnia tunelowanie wymuszone. |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Korzystanie z pomocy technicznej usługi Azure Monitoring i pomocy technicznej innej firmy. |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Wszystkie operacje płaszczyzny kontroli działają w ramach procesów na potrzeby inspekcji i zatwierdzeń. |
| Rejestrowanie i inspekcja płaszczyzny danych| ND | Klient jest właścicielem klastra.  |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Uwierzytelnianie odbywa się za Azure Active Directory. |
| Authorization| Tak | Zarządzanie tożsamościami i dostępem (IAM) dla wywołań za pośrednictwem usługi SFRP. Wywołania bezpośrednio do punktu końcowego klastra obsługują dwie role: Użytkownik i administrator. Klient może mapować interfejsy API na jedną rolę. |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: Klucze zarządzane przez firmę Microsoft | Tak | Klient jest właścicielem klastra i zestawu skalowania maszyn wirtualnych, na którym został skompilowany klaster. Usługę Azure Disk Encryption można włączyć na zestawie skalowania maszyn wirtualnych. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Tak | Klient jest właścicielem klastra i zestawu skalowania maszyn wirtualnych, na którym został skompilowany klaster. Usługę Azure Disk Encryption można włączyć na zestawie skalowania maszyn wirtualnych. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND |  |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak |  |
| Wywołania interfejsu API są szyfrowane| Tak | Wywołania interfejsu API Service Fabric są nawiązywane przez Azure Resource Manager. Wymagany jest prawidłowy token sieci Web JSON (JWT). |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).