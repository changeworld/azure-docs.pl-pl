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
ms.openlocfilehash: 11977a5aa36b023e468ce6a54862b5138995c417
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/25/2019
ms.locfileid: "64513512"
---
# <a name="common-security-attributes-for-azure-service-bus-messaging"></a>Wspólne atrybuty zabezpieczeń dla komunikatów usługi Azure Service Bus

Zabezpieczenia są zintegrowane w każdy aspekt usługi platformy Azure. W tym artykule opisano typowe atrybuty zabezpieczeń wbudowanych w komunikatów usługi Azure Service Bus.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>|  Tak po stronie serwera szyfrowania podczas spoczynku domyślnie. | Klucze zarządzaną przez klienta i scenariusza BYOK nie są jeszcze obsługiwane. Szyfrowanie po stronie klienta jest odpowiedzialny za klienta |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Yes | Obsługuje standardowego mechanizmu HTTPS/TLS. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Nie |   |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND | |
| Wywołania interfejsu API szyfrowane| Yes | Wywołania interfejsu API odbywa się za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml) i HTTPS. |

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
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Obsługiwane za pośrednictwem [usługa Azure Monitor i alerty](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Zarządzane za pośrednictwem [usługi Azure Active Directory tożsamości usługi zarządzanej](service-bus-managed-service-identity.md); zobacz [usługi Service Bus, uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md).|
| Autoryzacja| Yes | Obsługuje autoryzację za pośrednictwem [RBAC](service-bus-role-based-access-control.md) (wersja zapoznawcza) i tokenu sygnatury dostępu Współdzielonego, zobacz [usługi Service Bus, uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Yes | Dzienniki czynności są dostępne; zobacz [dzienników diagnostycznych usługi Service Bus](service-bus-diagnostic-logs.md).  |
| Rejestrowanie płaszczyzny danych i inspekcji| Nie |  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Yes | Obsługuje przechowywanie wersji dostawcy zasobu za pośrednictwem [interfejsu API usługi Azure Resource Manager](/rest/api/resources/).|
