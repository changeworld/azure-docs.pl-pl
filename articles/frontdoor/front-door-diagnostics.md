---
title: Usługa Azure drzwiami frontowymi — metryki i rejestrowanie | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć różne metryki i Dzienniki dostępu, które obsługuje usługę drzwiami frontowymi Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 3097f4a1716718df5d67769e234562a234623cfe
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407032"
---
# <a name="monitoring-metrics-and-logs-for-front-door"></a>Monitorowanie metryk i dzienników drzwi

Za pomocą usługi Azure Service drzwiami frontowymi, można monitorować zasoby w następujący sposób:

* [Metryki](#metrics): Usługa Application Gateway obecnie ma siedem metryki, aby wyświetlić liczniki wydajności.
* [Dzienniki](#diagnostic-logging): Dzienniki umożliwiają wydajności, dostępu i inne dane, które mają być zapisane lub używane z zasobu do celów monitorowania.

## <a name="metrics"></a>Metryki

Metryki są funkcją dla niektórych zasobów platformy Azure, w którym można wyświetlać liczniki wydajności w portalu. Drzwiami frontowymi dostępne są następujące metryki:

| Metryka | Nazwa wyświetlana metryki | Jednostka | Wymiary | Opis |
| --- | --- | --- | --- | --- |
| RequestCount | Liczba żądań | Licznik | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba żądań klientów obsługiwanych przez wejściu.  |
| RequestSize | Rozmiar żądania | Bajty | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba bajtów wysłanych jako żądania od klientów na wejściu. |
| ResponseSize | Rozmiar odpowiedzi | Bajty | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba bajtów wysłanych jako odpowiedzi z wejściu do klientów. |
| TotalLatency | Całkowity czas oczekiwania | Milisekundy | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Czas obliczonym na podstawie żądanie klienta zostało odebrane przez drzwiami frontowymi, aż klient potwierdzenia ostatni bajt odpowiedzi z drzwiami frontowymi. |
| BackendRequestCount | Liczba żądań wewnętrznej bazy danych | Licznik | HttpStatus</br>HttpStatusGroup</br>Zaplecze | Liczba żądań wysyłanych z wejściu do zaplecza. |
| BackendRequestLatency | Opóźnienia żądania wewnętrznej bazy danych | Milisekundy | Zaplecze | Czas obliczonym na podstawie Jeśli żądanie zostało wysłane przy wejściu do wewnętrznej bazy danych, do chwili drzwiami frontowymi odebrania ostatniego bajtu odpowiedzi z wewnętrznej bazy danych. |
| BackendHealthPercentage | Wartość procentowa kondycję wewnętrznej bazy danych | Procent | Zaplecze</br>BackendPool | Procent pomyślnych kondycji sondy z wejściu do zaplecza. |
| WebApplicationFirewallRequestCount | Liczba żądań zapory aplikacji sieci Web | Licznik | PolicyName</br>RuleName</br>Akcja | Liczba żądań klienta przetwarzanych przez drzwiami frontowymi zabezpieczeń warstwy aplikacji. |

## <a name="activity-log"></a>Dzienniki aktywności

Dzienniki aktywności udostępniają szczegółowe dane operacji, które zostały wykonane na komputerze użytkownika drzwiami frontowymi. Korzystając z dzienników aktywności można określić "co, kto i kiedy" każdej operacji (PUT, POST, DELETE) wykonywanych na Twoje drzwiami frontowymi zapisu.

> [!NOTE]
> Dzienniki aktywności nie obejmują operacji odczytu (GET) ani operacji wykonywanych w witrynie Azure Portal albo przy użyciu oryginalnych interfejsów API zarządzania.

Możesz uzyskać dostępu do dzienników aktywności w swojej drzwiami frontowymi lub dostęp do wszystkich zasobów platformy Azure w usłudze Azure Monitor. 

Aby wyświetlić dzienniki aktywności:

1. Wybierz wystąpienie usługi wejściu.
2. Kliknij pozycję **Dziennik aktywności**.

    ![dziennik aktywności](./media/front-door-diagnostics/activity-log.png)

3. Wybierz żądany zakres filtrowania i kliknij przycisk **Zastosuj**.

## <a name="diagnostic-logging"></a>Dzienniki diagnostyczne
Dzienniki diagnostyczne zawierają bogate informacje o operacjach i błędach, które są ważne w przypadku inspekcji, a także pomagają rozwiązywać problemy. Dzienniki diagnostyczne różnią się od dzienników aktywności. Dzienniki aktywności udostępniają szczegółowe dane operacji wykonywanych w stosunku do zasobów platformy Azure. Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonanych przez zasób. Dowiedz się więcej o [dzienników diagnostycznych usługi Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md). 

Aby skonfigurować dzienniki diagnostyczne dla Twojego drzwiami frontowymi:

1. Wybierz wystąpienie usługi APIM.
2. Kliknij pozycję **Ustawienia diagnostyczne**.

    ![dzienniki diagnostyczne](./media/front-door-diagnostics/diagnostic-log.png)

3. Kliknij pozycję **Włącz diagnostykę**. Dzienniki diagnostyczne wraz z metrykami można zarchiwizować na koncie magazynu, przesyłać strumieniowo do usługi Event Hub lub wysłać do dziennika usługi Azure Monitor. 

Usługa drzwiami frontowymi udostępnia obecnie diagnostyki dzienników (wsadowe co godzinę) dotyczące poszczególnych interfejsu API żądania zgodny z następującym schematem AP. Każdy wpis:

| Właściwość  | Opis |
| ------------- | ------------- |
| ClientIp | Adres IP klienta, który zgłosił żądanie. |
| ClientPort | Port adresu IP klienta, który zgłosił żądanie. |
| HttpMethod | Metoda HTTP używana przez żądanie. |
| HttpStatusCode | Kod stanu HTTP zwrócony z serwera proxy. |
| HttpStatusDetails | Wynikowy stan dla żądania. Znaczenie tej wartości parametrów można znaleźć w tabeli referencyjnej stanu. |
| HttpVersion | Typ żądania lub połączenia. |
| RequestBytes | Rozmiar komunikatu żądania HTTP, w bajtach, w tym nagłówki żądania i treści żądania. |
| RequestUri | Identyfikator URI odebrane żądanie. |
| ResponseBytes | Liczba bajtów wysłanych przez serwer wewnętrznej bazy danych jako odpowiedzi.  |
| RoutingRuleName | Nazwa reguły routingu, który jest zgodny z żądaniem. |
| Elementu SecurityProtocol | Wersja protokołu TLS/SSL używany przez żądania lub wartość null, jeśli bez szyfrowania. |
| Właściwość timeTaken | Długość czasu wykonywania akcji w milisekundach. |
| UserAgent | Typ przeglądarki, używanego przez klienta |
| TrackingReference | Ciąg unikatowy odwołania, który identyfikuje żądanie obsługiwanej przez drzwiami frontowymi również wysyłane jako nagłówek X-Azure-Ref do klienta. Wymagane do wyszukiwania informacji w dziennikach dostępu dla określonego żądania. |

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
