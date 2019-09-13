---
title: Kontrolki zabezpieczeń dla Azure Cosmos DB
description: Lista kontrolna kontroli zabezpieczeń dla oceny Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 20cdfc61a4cdfe5263e48d049aab14cad2458b06
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886616"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Kontrolki zabezpieczeń dla Azure Cosmos DB

W tym artykule opisano kontrolki zabezpieczeń wbudowane w Azure Cosmos DB.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak |  |
| Obsługa iniekcji sieci wirtualnej| Tak | Za pomocą punktu końcowego usługi sieci wirtualnej można skonfigurować konto Azure Cosmos DB, aby zezwalać na dostęp tylko z określonej podsieci sieci wirtualnej (VNet). Możesz również połączyć dostęp do sieci wirtualnej przy użyciu reguł zapory.  Zobacz [dostęp Azure Cosmos DB z sieci wirtualnych](VNet-service-endpoint.md). |
| Izolacja sieci i obsługa zapór| Tak | Dzięki obsłudze zapory można skonfigurować konto usługi Azure Cosmos, aby zezwalać na dostęp tylko z zatwierdzonego zestawu adresów IP, zakresu adresów IP i/lub usług w chmurze. Zobacz [Konfigurowanie zapory IP w Azure Cosmos DB](how-to-configure-firewall.md).|
| Obsługa tunelowania wymuszonego| Tak | Można skonfigurować po stronie klienta w sieci wirtualnej, w której znajdują się maszyny wirtualne.   |

## <a name="monitoring--logging"></a>Monitorowanie rejestrowania &

| Kontrola zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Pomoc techniczna dotycząca monitorowania platformy Azure (log Analytics, App Insights itp.)| Tak | Wszystkie żądania wysyłane do Azure Cosmos DB są rejestrowane. [Monitorowanie platformy](../azure-monitor/overview.md)Azure, metryki platformy Azure, rejestrowanie inspekcji platformy Azure jest obsługiwane.  Można rejestrować informacje odpowiadające na żądania płaszczyzny danych, statystyki środowiska uruchomieniowego zapytań, tekst zapytania, żądania MongoDB. Możesz również skonfigurować alerty. |
| Rejestrowanie i inspekcja płaszczyzny kontroli i zarządzania| Tak | Dziennik aktywności platformy Azure dla operacji na poziomie konta, takich jak zapory, sieci wirtualnych, dostęp do kluczy i IAM. |
| Rejestrowanie i inspekcja płaszczyzny danych | Tak | Rejestrowanie diagnostyczne monitorowania dla operacji na poziomie kontenera, takich jak tworzenie kontenerów, obsługa przepływności, zasady indeksowania i operacje CRUD na dokumentach. |

## <a name="identity"></a>Tożsamość

| Kontrola zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Authentication| Tak | Tak na poziomie konta bazy danych; na poziomie płaszczyzny danych Cosmos DB używa tokenów zasobów i dostępu do kluczy. |
| Authorization| Tak | Obsługiwane na koncie usługi Azure Cosmos z kluczami głównymi (podstawowymi i pomocniczymi) i tokenami zasobów. Możesz uzyskać dostęp do odczytu/zapisu lub tylko do odczytu do danych z kluczami głównymi. Tokeny zasobów umożliwiają ograniczony dostęp do zasobów, takich jak dokumenty i kontenery. |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: Klucze zarządzane przez firmę Microsoft | Tak | Wszystkie bazy danych i kopie zapasowe Cosmos są domyślnie szyfrowane; Zobacz [szyfrowanie danych w Azure Cosmos DB](database-encryption-at-rest.md). Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta nie jest obsługiwane. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Tak | Tylko w interfejsie API tabel Premium. Nie wszystkie interfejsy API obsługują tę funkcję. Zobacz [wprowadzenie do Azure Cosmos DB: Interfejs API tabel](table-introduction.md). |
| Szyfrowanie podczas przesyłania (takie jak szyfrowanie ExpressRoute, szyfrowanie sieci wirtualnej i szyfrowanie sieci wirtualnej)| Tak | Wszystkie dane Azure Cosmos DB są szyfrowane podczas przesyłania. |
| Wywołania interfejsu API są szyfrowane| Tak | Wszystkie połączenia do Azure Cosmos DB obsługują protokół HTTPS. Azure Cosmos DB obsługuje również połączenia TLS 1,2, ale nie jest to jeszcze wymuszane. Jeśli klienci wyłączają niższy poziom protokołu TLS na swoich końcach, mogą się upewnić, że nawiąże połączenie z Cosmos DB.  |

## <a name="configuration-management"></a>Zarządzanie konfiguracją

| Kontrola zabezpieczeń | Tak/Nie | Uwagi|
|---|---|--|
| Obsługa zarządzania konfiguracją (wersja konfiguracji itp.)| Nie  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>Dodatkowe opcje zabezpieczeń dla Cosmos DB

| Kontrola zabezpieczeń | Tak/nie | Uwagi|
|---|---|--|
| Współużytkowanie zasobów między źródłami (CORS) | Tak | Zobacz [Konfigurowanie udostępniania zasobów między źródłami (CORS)](how-to-configure-cross-origin-resource-sharing.md). |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [wbudowanych kontrolach zabezpieczeń w ramach usług platformy Azure](../security/fundamentals/security-controls.md).