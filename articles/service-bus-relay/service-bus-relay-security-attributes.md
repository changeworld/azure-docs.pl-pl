---
title: Wspólne atrybuty zabezpieczeń dla usługi Azure Service Bus Relay
description: Lista kontrolna typowych atrybutów zabezpieczeń do oceny usługi Azure Service Bus Relay
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d8ce3c995e8e0f20ed6d694f481cc5fc9fde4fa7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66000150"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Atrybuty zabezpieczeń dla usługi Azure Service Bus Relay

W tym artykule opisano atrybuty zabezpieczeń wbudowane w usłudze Azure Service Bus Relay.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>|  ND | Przekaźnik jest gniazda sieci web i nie utrwala dane. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Tak | Usługa wymaga protokołu TLS. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Nie | Używa tylko certyfikaty TLS firmy Microsoft.  |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND | |
| Wywołania interfejsu API szyfrowane| Yes | HTTPS. |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Nie |  |
| Izolacja sieci i zapory pomocy technicznej| Nie |  |
| Obsługa tunelowania wymuszonego| ND | Przekaźnik to tunelu TLS  |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Via SAS. |
| Autoryzacja|  Tak | Via SAS. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Tak | Za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml). |
| Rejestrowanie płaszczyzny danych i inspekcji| Tak | Pomyślnego połączenia / awarii i błędów i rejestrowane.  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Tak | Za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml).|
