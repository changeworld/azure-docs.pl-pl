---
title: Kontrolki zabezpieczeń dla Azure Service Bus Messaging
description: Lista kontrolna kontroli zabezpieczeń służąca do oceniania Azure Service Bus komunikatów
services: service-bus-messaging
ms.service: service-bus-messaging
author: axisc
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: aschhab
ms.openlocfilehash: ac931ac7cf6c8609019b120388e1b6ca836f72b8
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886376"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Kontrolki zabezpieczeń dla Azure Service Bus Messaging

W tym artykule opisano mechanizmy kontroli zabezpieczeń wbudowane w Azure Service Bus Messaging.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak (tylko warstwa Premium) | Punkty końcowe usługi sieci wirtualnej są obsługiwane tylko w przypadku [Service Bus warstwy Premium](service-bus-premium-messaging.md) . |
| Obsługa iniekcji sieci wirtualnej| Nie | |
| Izolacja sieci i obsługa zapór| Tak (tylko warstwa Premium) |  |
| Obsługa tunelowania wymuszonego| Nie |  |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Obsługiwane za pośrednictwem [Azure monitor i alertów](service-bus-metrics-azure-monitor.md). |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Dzienniki operacji są dostępne; Zobacz [Service Bus dzienników diagnostycznych](service-bus-diagnostic-logs.md).  |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie |  |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Zarządzane przez [Azure Active Directory tożsamość usługi zarządzanej](service-bus-managed-service-identity.md); Zobacz [Service Bus uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md).|
| Authorization| Tak | Obsługuje autoryzację za pośrednictwem tokenu [RBAC](authenticate-application.md) i SAS; Zobacz [Service Bus uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: Klucze zarządzane przez firmę Microsoft |  Tak w przypadku szyfrowania po stronie serwera domyślnie. | Klucze zarządzane przez klienta i BYOK nie są jeszcze obsługiwane. Szyfrowanie po stronie klienta jest odpowiedzialnością klienta |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie |   |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Obsługuje standardowy mechanizm HTTPS/TLS. |
| Wywołania interfejsu API są szyfrowane| Tak | Wywołania interfejsu API są nawiązywane za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml) i https. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Obsługuje przechowywanie wersji dostawcy zasobów za pomocą [interfejsu API Azure Resource Manager](/rest/api/resources/).|

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).