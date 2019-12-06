---
title: Kontrolki zabezpieczeń dla Azure Cosmos DB
description: Zapoznaj się z listą kontrolną kontroli zabezpieczeń, taką jak sieć, monitorowanie, tożsamość i ochrona danych, aby oszacować Azure Cosmos DB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.openlocfilehash: 5ab4281f1ad591befda5a439906604331a1ab323
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872149"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Kontrolki zabezpieczeń dla Azure Cosmos DB

W tym artykule opisano kontrolki zabezpieczeń wbudowane w Azure Cosmos DB.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Sieć

| Kontrola zabezpieczeń | Tak/nie | Uwagi |
|---|---|--|
| Obsługa punktów końcowych usługi| Tak |  |
| Obsługa iniekcji sieci wirtualnej| Tak | Za pomocą punktu końcowego usługi sieci wirtualnej można skonfigurować konto Azure Cosmos DB, aby zezwalać na dostęp tylko z określonej podsieci sieci wirtualnej (VNet). Możesz również połączyć dostęp do sieci wirtualnej przy użyciu reguł zapory. Aby dowiedzieć się więcej, zobacz [dostęp Azure Cosmos DB z sieci wirtualnych](VNet-service-endpoint.md). |
| Izolacja sieci i obsługa zapory| Tak | Dzięki obsłudze zapory można skonfigurować konto usługi Azure Cosmos, aby zezwalać na dostęp tylko z zatwierdzonego zestawu adresów IP, zakresu adresów IP i/lub usług w chmurze. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapory IP w Azure Cosmos DB](how-to-configure-firewall.md).|
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
| Uwierzytelnianie| Tak | Tak na poziomie konta bazy danych; na poziomie płaszczyzny danych Cosmos DB używa tokenów zasobów i dostępu do kluczy. |
| Autoryzacja| Tak | Obsługiwane na koncie usługi Azure Cosmos z kluczami głównymi (podstawowymi i pomocniczymi) i tokenami zasobów. Możesz uzyskać dostęp do odczytu/zapisu lub tylko do odczytu do danych z kluczami głównymi. Tokeny zasobów umożliwiają ograniczony dostęp do zasobów, takich jak dokumenty i kontenery. |

## <a name="data-protection"></a>Ochrona danych

| Kontrola zabezpieczeń | Tak/nie | Uwagi |
|---|---|--|
| Szyfrowanie po stronie serwera w czasie spoczynku: klucze zarządzane przez firmę Microsoft | Tak | Wszystkie bazy danych i kopie zapasowe usługi Azure Cosmos są domyślnie szyfrowane. Zobacz [szyfrowanie danych w Azure Cosmos DB](database-encryption-at-rest.md). Szyfrowanie po stronie serwera z kluczami zarządzanymi przez klienta nie jest obsługiwane. |
| Szyfrowanie po stronie serwera w spoczynku: klucze zarządzane przez klienta (BYOK) | Nie |  |
| Szyfrowanie na poziomie kolumny (Data Services platformy Azure)| Tak | Tylko w interfejsie API tabel Premium. Nie wszystkie interfejsy API obsługują tę funkcję. Zapoznaj [się z artykułem wprowadzenie do Azure Cosmos DB: interfejs API tabel](table-introduction.md). |
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