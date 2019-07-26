---
title: Atrybuty zabezpieczeń dla usługi Azure Service Fabric
description: Lista kontrolna atrybutów zabezpieczeń służących do oceny Service Fabric platformy Azure
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 23c7f8bdcf67d59ccdd5cd0b00bc0e0960ba1d8f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443868"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Atrybuty zabezpieczeń dla usługi Azure Service Fabric

W tym artykule opisano atrybuty zabezpieczeń wbudowane w usługę Azure Service Fabric. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania)| Yes | Klient jest właścicielem klastra i zestawu skalowania maszyn wirtualnych, na którym został skompilowany klaster. Usługę Azure Disk Encryption można włączyć na zestawie skalowania maszyn wirtualnych. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Yes |  |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Yes | Klient jest właścicielem klastra i zestawu skalowania maszyn wirtualnych, na którym został skompilowany klaster. Usługę Azure Disk Encryption można włączyć na zestawie skalowania maszyn wirtualnych. |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| ND |  |
| Wywołania interfejsu API są szyfrowane| Yes | Wywołania interfejsu API Service Fabric są nawiązywane przez Azure Resource Manager. Wymagany jest prawidłowy token sieci Web JSON (JWT). |

## <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/Nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak |  |
| Obsługa iniekcji sieci wirtualnej| Tak |  |
| Izolacja sieci i obsługa zapór| Tak | Korzystanie z sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). |
| Obsługa tunelowania wymuszonego| Tak | Sieć Azure udostępnia tunelowanie wymuszone. |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Korzystanie z pomocy technicznej usługi Azure Monitoring i pomocy technicznej innej firmy. |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Authentication| Tak | Uwierzytelnianie odbywa się za Azure Active Directory. |
| Authorization| Tak | Zarządzanie tożsamościami i dostępem (IAM) dla wywołań za pośrednictwem usługi SFRP. Wywołania bezpośrednio do punktu końcowego klastra obsługują dwie role: Użytkownik i administrator. Klient może mapować interfejsy API na jedną rolę. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes | Wszystkie operacje płaszczyzny kontroli działają w ramach procesów na potrzeby inspekcji i zatwierdzeń. |
| Rejestrowanie i inspekcja płaszczyzny danych| ND | Klient jest właścicielem klastra.  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Yes | |