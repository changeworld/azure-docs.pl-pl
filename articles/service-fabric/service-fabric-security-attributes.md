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
ms.openlocfilehash: f6db40a35c289fa0870ac4c9e5e55b773c84f4f4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679231"
---
# <a name="common-security-attributes-for-azure-service-fabric"></a>Wspólne atrybuty zabezpieczeń dla usługi Azure Service Fabric

Zabezpieczenia są zintegrowane w każdy aspekt usługi platformy Azure. W tym artykule opisano typowe atrybuty zabezpieczeń wbudowane w usłudze Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie danych magazynowanych:<ul><li>Szyfrowanie po stronie serwera</li><li>Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta</li><li>Inne funkcje szyfrowania (na przykład po stronie klienta, są zawsze szyfrowane, itd.)</ul>| Yes | Klient jest właścicielem klastra i ustawić dla klastra, który bazuje skalowania maszyn wirtualnych. Usługa Azure disk encryption można włączyć dla zestawu skalowania maszyn wirtualnych. |
| Szyfrowanie podczas przesyłania:<ul><li>Express route szyfrowania</li><li>W przypadku szyfrowania sieci wirtualnej</li><li>Sieć wirtualna-sieć wirtualna szyfrowania</ul>| Yes |  |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Yes | Klient jest właścicielem klastra i ustawić dla klastra, który bazuje skalowania maszyn wirtualnych. Usługa Azure disk encryption można włączyć dla zestawu skalowania maszyn wirtualnych. |
| Szyfrowanie na poziomie kolumny (usługi danych platformy Azure)| ND |  |
| Wywołania interfejsu API szyfrowane| Yes | Wywołania interfejsu API usługi Service Fabric są wykonywane za pośrednictwem usługi Azure Resource Manager. Nieprawidłowy token sieci web JSON (JWT) jest wymagana. |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Punkt końcowy usługi pomocy technicznej| Yes |  |
| Obsługa iniekcji sieci wirtualnej| Yes |  |
| Izolacja sieci / Zapora pomocy technicznej| Yes | Przy użyciu sieciowych grup zabezpieczeń (NSG). |
| Obsługa wymuszonego tunelowania | Yes | Sieć Azure udostępnia wymuszonym tunelowaniem. |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Korzystanie z platformy Azure, monitorowanie, obsługę i innych firm. |

## <a name="iam-support"></a>Obsługa zarządzania tożsamościami i Dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzanie dostępem — uwierzytelnianie| Yes | Uwierzytelnianie jest za pomocą usługi Azure Active Directory. |
| Zarządzanie dostępem - autoryzacji| Yes | Zarządzanie tożsamościami i dostępem (IAM) dla połączeń za pośrednictwem SFRP. Wywołań bezpośrednio do punktu końcowego klastra obsługuje dwie role: Użytkownika i administratora. Klienta można mapować interfejsów API do każdej roli. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Zarządzania kontrolą/Plan rejestrowania i inspekcji| Yes | Wszystkie operacje warstwy kontroli przez procesy do inspekcji i zatwierdzeń. |
| Rejestrowanie i inspekcja na płaszczyźnie danych| ND | Klient jest właścicielem klastra.  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Yes | Konfiguracja usługi jest wersjonowane i wdrażane za pomocą wdrażania na platformie Azure. Kod (aplikacji i środowiska uruchomieniowego) jest wersjonowany, za pomocą usługi Azure kompilacji.