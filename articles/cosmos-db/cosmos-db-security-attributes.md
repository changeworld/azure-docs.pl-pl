---
title: Atrybuty zabezpieczeń dla Azure Cosmos DB
description: Lista kontrolna atrybutów zabezpieczeń do oceny Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 50711f8675e1b8aca6b9f90925ea921d22020ddd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442550"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Atrybuty zabezpieczeń dla Azure Cosmos DB

W tym artykule opisano atrybuty zabezpieczeń wbudowane w Azure Cosmos DB.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Zapobiegawczej

| Atrybut zabezpieczeń | Tak/nie | Uwagi |
|---|---|--|
| Szyfrowanie w spoczynku (takie jak szyfrowanie po stronie serwera, szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta i inne funkcje szyfrowania) | Tak | Wszystkie Cosmos DB bazy danych i kopie zapasowe są domyślnie szyfrowane; Zobacz [szyfrowanie danych w Azure Cosmos DB](database-encryption-at-rest.md). Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta nie jest obsługiwane. |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Wszystkie dane Azure Cosmos DB są szyfrowane podczas przesyłania. |
| Obsługa kluczy szyfrowania (CMK, BYOK itp.)| Nie |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Tak | Tylko w interfejsie API tabel Premium. Nie wszystkie interfejsy API obsługują tę funkcję. Zobacz [wprowadzenie do Azure Cosmos DB: Interfejs API tabel](table-introduction.md). |
| Wywołania interfejsu API są szyfrowane| Tak | Wszystkie połączenia do Azure Cosmos DB obsługują protokół HTTPS. Azure Cosmos DB obsługuje również połączenia TLS 1,2, ale nie jest to jeszcze wymuszane. Jeśli klienci wyłączają niższy poziom protokołu TLS na swoich końcach, mogą się upewnić, że nawiąże połączenie z Cosmos DB.  |

## <a name="network-segmentation"></a>Segmentacja sieci

| Atrybut zabezpieczeń | Tak/nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak |  |
| Obsługa iniekcji sieci wirtualnej| Tak | Za pomocą punktu końcowego usługi sieci wirtualnej można skonfigurować konto Azure Cosmos DB, aby zezwalać na dostęp tylko z określonej podsieci sieci wirtualnej (VNet). Możesz również połączyć dostęp do sieci wirtualnej przy użyciu reguł zapory.  Zobacz [dostęp Azure Cosmos DB z sieci wirtualnych](VNet-service-endpoint.md). |
| Izolacja sieci i obsługa zapór| Yes | Dzięki obsłudze zapory można skonfigurować konto usługi Azure Cosmos, aby zezwalać na dostęp tylko z zatwierdzonego zestawu adresów IP, zakresu adresów IP i/lub usług w chmurze. Zobacz [Konfigurowanie zapory IP w Azure Cosmos DB](how-to-configure-firewall.md).|
| Obsługa tunelowania wymuszonego| Yes | Można skonfigurować po stronie klienta w sieci wirtualnej, w której znajdują się maszyny wirtualne.   |

## <a name="detection"></a>Wykrywanie

| Atrybut zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Yes | Wszystkie żądania wysyłane do Azure Cosmos DB są rejestrowane. [Monitorowanie platformy](../azure-monitor/overview.md)Azure, metryki platformy Azure, rejestrowanie inspekcji platformy Azure jest obsługiwane.  Można rejestrować informacje odpowiadające na żądania płaszczyzny danych, statystyki środowiska uruchomieniowego zapytań, tekst zapytania, żądania MongoDB. Możesz również skonfigurować alerty. |

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

| Atrybut zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Authentication| Tak | Tak na poziomie konta bazy danych; na poziomie płaszczyzny danych Cosmos DB używa tokenów zasobów i dostępu do kluczy. |
| Authorization| Tak | Obsługiwane na koncie usługi Azure Cosmos z kluczami głównymi (podstawowymi i pomocniczymi) i tokenami zasobów. Możesz uzyskać dostęp do odczytu/zapisu lub tylko do odczytu do danych z kluczami głównymi. Tokeny zasobów umożliwiają ograniczony dostęp do zasobów, takich jak dokumenty i kontenery. |


## <a name="audit-trail"></a>Dziennik inspekcji

| Atrybut zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Yes | Dziennik aktywności platformy Azure dla operacji na poziomie konta, takich jak zapory, sieci wirtualnych, dostęp do kluczy i IAM. |
| Rejestrowanie i inspekcja płaszczyzny danych | Yes | Rejestrowanie diagnostyczne monitorowania dla operacji na poziomie kontenera, takich jak tworzenie kontenerów, obsługa przepływności, zasady indeksowania i operacje CRUD na dokumentach. |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Atrybut zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Nie  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Dodatkowe atrybuty zabezpieczeń dla Cosmos DB

| Atrybut zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Współużytkowanie zasobów między źródłami (CORS) | Tak | Zobacz [Konfigurowanie udostępniania zasobów między źródłami (CORS)](how-to-configure-cross-origin-resource-sharing.md). |
