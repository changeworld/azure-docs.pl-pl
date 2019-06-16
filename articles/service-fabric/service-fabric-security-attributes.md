---
title: Wspólne atrybuty zabezpieczeń dla usługi Azure Service Fabric
description: Lista kontrolna typowych atrybutów zabezpieczeń do oceny usługi Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 7c1718298c3f7c3fea28fa0b18569085f071696f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66003058"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Atrybuty zabezpieczeń dla usługi Azure Service Fabric

W tym artykule opisano atrybuty zabezpieczeń wbudowane w usłudze Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Yes | Klient jest właścicielem klastra i ustawić dla klastra, który bazuje skalowania maszyn wirtualnych. Usługa Azure disk encryption można włączyć dla zestawu skalowania maszyn wirtualnych. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Tak |  |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Tak | Klient jest właścicielem klastra i ustawić dla klastra, który bazuje skalowania maszyn wirtualnych. Usługa Azure disk encryption można włączyć dla zestawu skalowania maszyn wirtualnych. |
| Szyfrowanie na poziomie kolumny (Azure Data Services)| ND |  |
| Wywołania interfejsu API szyfrowane| Tak | Wywołania interfejsu API usługi Service Fabric są wykonywane za pośrednictwem usługi Azure Resource Manager. Nieprawidłowy token sieci web JSON (JWT) jest wymagana. |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktu końcowego usługi| Yes |  |
| Obsługa iniekcji sieci wirtualnej| Tak |  |
| Izolacja sieci i zapory pomocy technicznej| Tak | Przy użyciu sieciowych grup zabezpieczeń (NSG). |
| Obsługa tunelowania wymuszonego| Tak | Sieć Azure udostępnia wymuszonym tunelowaniem. |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Tak | Korzystanie z platformy Azure, monitorowanie, obsługę i innych firm. |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Uwierzytelnianie jest za pomocą usługi Azure Active Directory. |
| Autoryzacja| Tak | Zarządzanie tożsamościami i dostępem (IAM) dla połączeń za pośrednictwem SFRP. Wywołań bezpośrednio do punktu końcowego klastra obsługuje dwie role: Użytkownika i administratora. Klienta można mapować interfejsów API do każdej roli. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie płaszczyzny zarządzania i kontroli i inspekcji| Yes | Wszystkie operacje warstwy kontroli przez procesy do inspekcji i zatwierdzeń. |
| Rejestrowanie płaszczyzny danych i inspekcji| ND | Klient jest właścicielem klastra.  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Yes | |