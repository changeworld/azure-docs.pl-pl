---
title: Atrybuty zabezpieczeń usługi Azure Service Bus Relay
description: Lista kontrolna atrybutów zabezpieczeń do oceny Azure Service Bus Relay
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 2047f64914d4a286e6de38b7b2c8524d98eba562
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443878"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Atrybuty zabezpieczeń usługi Azure Service Bus Relay

W tym artykule opisano atrybuty zabezpieczeń wbudowane w Azure Service Bus Relay.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania)|  ND | Przekaźnik jest gniazdem internetowym i nie utrzymuje danych. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Usługa wymaga protokołu TLS. |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Nie | Używa tylko certyfikatów Microsoft TLS.  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND | |
| Wywołania interfejsu API są szyfrowane| Tak | HTTPS. |

## <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Nie |  |
| Izolacja sieci i obsługa zapór| Nie |  |
| Obsługa tunelowania wymuszonego| ND | Przekaźnik jest tunelem TLS  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Za pośrednictwem SAS. |
| Authorization|  Yes | Za pośrednictwem SAS. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes | Za [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Rejestrowanie i inspekcja płaszczyzny danych| Yes | Powodzenie/Niepowodzenie połączenia i błędy oraz zarejestrowane.  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Tak | Za [Azure Resource Manager](../azure-resource-manager/index.yml).|
