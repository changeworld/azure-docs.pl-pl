---
title: Atrybuty zabezpieczeń dla Azure Service Bus Messaging
description: Lista kontrolna atrybutów zabezpieczeń do oceny Azure Service Bus komunikatów
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e1d6e041b47a261b549fb8b608cf09d0d6362dd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443895"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Atrybuty zabezpieczeń dla Azure Service Bus Messaging

W tym artykule opisano atrybuty zabezpieczeń wbudowane w Azure Service Bus komunikatów.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania)|  Tak w przypadku szyfrowania po stronie serwera domyślnie. | Klucze zarządzane przez klienta i BYOK nie są jeszcze obsługiwane. Szyfrowanie po stronie klienta jest odpowiedzialnością klienta |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Yes | Obsługuje standardowy mechanizm HTTPS/TLS. |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Nie |   |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | Wywołania interfejsu API są nawiązywane za pośrednictwem [Azure Resource Manager](../azure-resource-manager/index.yml) i https. |

## <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak (tylko warstwa Premium) | Punkty końcowe usługi sieci wirtualnej są obsługiwane tylko w przypadku [Service Bus warstwy Premium](service-bus-premium-messaging.md) . |
| Obsługa iniekcji sieci wirtualnej| Nie | |
| Izolacja sieci i obsługa zapór| Tak (tylko warstwa Premium) |  |
| Obsługa tunelowania wymuszonego| Nie |  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Yes | Obsługiwane za pośrednictwem [Azure monitor i alertów](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Zarządzane przez [Azure Active Directory tożsamość usługi zarządzanej](service-bus-managed-service-identity.md); Zobacz [Service Bus uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md).|
| Authorization| Yes | Obsługuje autoryzację za pośrednictwem [RBAC](service-bus-role-based-access-control.md) (wersja zapoznawcza) i token SAS; Zobacz [Service Bus uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes | Dzienniki operacji są dostępne; Zobacz [Service Bus dzienników diagnostycznych](service-bus-diagnostic-logs.md).  |
| Rejestrowanie i inspekcja płaszczyzny danych| Nie |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes | Obsługuje przechowywanie wersji dostawcy zasobów za pomocą [interfejsu API Azure Resource Manager](/rest/api/resources/).|
