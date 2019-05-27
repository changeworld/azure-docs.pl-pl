---
title: Wspólne atrybuty zabezpieczeń dla komunikatów usługi Azure Service Bus
description: Lista kontrolna typowych atrybutów zabezpieczeń do oceny, komunikatów usługi Azure Service Bus
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d68ffe6561da6a23c288dfabd1d3eb6b34099bb3
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003114"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Atrybuty zabezpieczeń dla komunikatów usługi Azure Service Bus

W tym artykule opisano atrybuty zabezpieczeń wbudowanych w komunikatów usługi Azure Service Bus.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>|  Tak po stronie serwera szyfrowania podczas spoczynku domyślnie. | Klucze zarządzaną przez klienta i scenariusza BYOK nie są jeszcze obsługiwane. Szyfrowanie po stronie klienta jest odpowiedzialny za klienta |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Tak | Obsługuje standardowego mechanizmu HTTPS/TLS. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Nie |   |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND | |
| Wywołania interfejsu API szyfrowane| Tak | Wywołania interfejsu API odbywa się za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml) i HTTPS. |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Tak (tylko w warstwie Premium) | Punkty końcowe usługi sieci wirtualnej są obsługiwane w przypadku [warstwy usługi Service Bus w warstwie Premium](service-bus-premium-messaging.md) tylko. |
| Obsługa iniekcji sieci wirtualnej| Nie | |
| Izolacja sieci i zapory pomocy technicznej| Tak (tylko w warstwie Premium) |  |
| Obsługa tunelowania wymuszonego| Nie |  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | Obsługiwane za pośrednictwem [usługa Azure Monitor i alerty](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Zarządzane za pośrednictwem [usługi Azure Active Directory tożsamości usługi zarządzanej](service-bus-managed-service-identity.md); zobacz [usługi Service Bus, uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md).|
| Autoryzacja| Tak | Obsługuje autoryzację za pośrednictwem [RBAC](service-bus-role-based-access-control.md) (wersja zapoznawcza) i tokenu sygnatury dostępu Współdzielonego, zobacz [usługi Service Bus, uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak | Dzienniki czynności są dostępne; zobacz [dzienników diagnostycznych usługi Service Bus](service-bus-diagnostic-logs.md).  |
| Rejestrowanie płaszczyzny danych i inspekcji| Nie |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak | Obsługuje przechowywanie wersji dostawcy zasobu za pośrednictwem [interfejsu API usługi Azure Resource Manager](/rest/api/resources/).|
