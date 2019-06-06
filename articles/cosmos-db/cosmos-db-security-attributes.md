---
title: Atrybuty zabezpieczeń usługi Azure Cosmos DB
description: Lista kontrolna atrybutów zabezpieczeń do oceny usługi Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: ffeb60f5476a540e3da46a82c240b0dda9aa6be2
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480466"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Atrybuty zabezpieczeń usługi Azure Cosmos DB

W tym artykule opisano typowe atrybuty zabezpieczeń wbudowane w usłudze Azure Cosmos DB.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczych

| Atrybut zabezpieczeń | Tak/nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (na przykład szyfrowanie po stronie serwera, szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta i inne funkcje szyfrowania) | Yes | Wszystkie bazy danych Cosmos DB, jak i kopie zapasowe są domyślne szyfrowanie przekazywanego materiału; zobacz [szyfrowanie danych w usłudze Azure Cosmos DB](database-encryption-at-rest.md). Szyfrowanie po stronie serwera za pomocą kluczy zarządzanych przez klienta nie jest obsługiwana. |
| Szyfrowanie podczas transferu (np. szyfrowanie usługi ExpressRoute w sieci wirtualnej, szyfrowania i szyfrowania sieć wirtualna-sieć wirtualna)| Tak | Wszystkie usługi Azure Cosmos DB dane są szyfrowane, gdy przesyłania. |
| Obsługa klucza szyfrowania (CMK BYOK, itp.)| Nie |  |
| Szyfrowanie na poziomie kolumny (usługi danych platformy Azure)| Tak | Tylko w warstwie Premium interfejsu API tabel. Nie wszystkie interfejsy API obsługują tę funkcję. Zobacz [wprowadzenie do usługi Azure Cosmos DB: Interfejs API tabel](table-introduction.md). |
| Wywołania interfejsu API szyfrowane| Tak | Wszystkie połączenia z usługą Azure Cosmos DB obsługuje protokół HTTPS. Usługa Azure Cosmos DB obsługuje połączenia protokołu TLS 1.2, ale to nie jest jeszcze wymuszane. Jeśli klienci wyłączy niższego poziomu protokołu TLS po ich stronie zapewnienia łączenie z usługą Cosmos DB.  |

## <a name="network-segmentation"></a>Segmentacji sieci

| Atrybut zabezpieczeń | Tak/nie | Uwagi |
|---|---|--|
| Punkt końcowy usługi pomocy technicznej| Yes |  |
| Obsługa iniekcji sieci wirtualnej| Yes | Z punktu końcowego usługi sieci wirtualnej można skonfigurować konto usługi Azure Cosmos DB, aby zezwolić na dostęp tylko z określonej podsieci sieci wirtualnej (VNet). Dostęp do sieci wirtualnej można także połączyć za pomocą reguł zapory.  Zobacz [dostępu do usługi Azure Cosmos DB z sieciami wirtualnymi](vnet-service-endpoint.md). |
| Izolacja sieci i Firewalling pomocy technicznej| Tak | Dzięki obsłudze zapory można skonfigurować swoje konto usługi Azure Cosmos, aby zezwolić na dostęp tylko z zatwierdzonych zbiór adresów IP, zakresu adresów IP i/lub usług w chmurze. Zobacz [Konfigurowanie zapory adresów IP w usłudze Azure Cosmos DB](how-to-configure-firewall.md).|
| Obsługa wymuszonego tunelowania | Tak | Można skonfigurować po stronie klienta w sieci Wirtualnej, w którym znajdują się maszyny wirtualne.   |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Obsługa (usługi Log analytics, usługi App insights itp.) do monitorowania platformy Azure| Yes | Wszystkie żądania, które są wysyłane do usługi Azure Cosmos DB są rejestrowane. [Monitorowanie platformy Azure](../azure-monitor/overview.md), metryk usługi Azure, rejestrowanie inspekcji platformy Azure są obsługiwane.  Bazy danych MongoDB możesz zalogować się informacjami dotyczącymi żądania płaszczyzny danych, statystyki czasu wykonywania zapytań, tekst zapytania, żądań. Można też skonfigurować alerty. |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Authentication| Tak | Tak na poziomie konta bazy danych; na poziomie płaszczyzny danych usługi Cosmos DB używa tokenów zasobów oraz dostęp do klucza. |
| Autoryzacja| Yes | Obsługiwane na konto usługi Azure Cosmos przy użyciu kluczy głównych (podstawowych i pomocniczych) i tokenów zasobów. Można uzyskać odczytu/zapisu lub odczytu tylko dostęp do danych za pomocą kluczy głównych. Tokeny zasobów zezwolić na ograniczony czas dostępu do zasobów, takich jak dokumenty i kontenerów. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Zarządzania kontrolą/Plan rejestrowania i inspekcji| Yes | Dziennik aktywności platformy Azure dla operacji na poziomie konta takich jak zapory i sieci wirtualne, klucze dostępu i zarządzania tożsamościami i Dostępem. |
| Rejestrowanie i inspekcja na płaszczyźnie danych | Yes | Diagnostyka, monitorowanie, rejestrowanie dla operacji na poziomie kontenera, takie jak tworzenie kontenerów, aprowizowanie przepływności, indeksowanie, zasad i operacje CRUD na dokumentach. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracji (przechowywanie wersji konfiguracji itp.)| Nie  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Atrybuty dodatkowe zabezpieczenia dla usługi Cosmos DB

| Atrybut zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Krzyżowe Origin Resource Sharing (CORS) | Yes | Zobacz [skonfigurować Cross-Origin Resource Sharing (CORS)](how-to-configure-cross-origin-resource-sharing.md). |
