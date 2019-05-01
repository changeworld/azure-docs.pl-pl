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
ms.openlocfilehash: f8827ac290393c9f394c3b13149555a1a2aa6df9
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927495"
---
# <a name="common-security-attributes-for-azure-service-bus-relay"></a>Wspólne atrybuty zabezpieczeń dla usługi Azure Service Bus Relay

Zabezpieczenia są zintegrowane w każdy aspekt usługi platformy Azure. W tym artykule opisano typowe atrybuty zabezpieczeń wbudowane w usłudze Azure Service Bus Relay.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>|  ND | Przekaźnik jest gniazda sieci web i nie utrwala dane. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Yes | Usługa wymaga protokołu TLS. |
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
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Yes | Via SAS. |
| Autoryzacja|  Yes | Via SAS. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Yes | Za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml). |
| Rejestrowanie płaszczyzny danych i inspekcji| Yes | Pomyślnego połączenia / awarii i błędów i rejestrowane.  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Yes | Za pomocą [usługi Azure Resource Manager](../azure-resource-manager/index.yml).|
