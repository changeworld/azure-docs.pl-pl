---
title: Monitorowanie metryk i dzienników w usłudze Azure Service wejściu | Dokumentacja firmy Microsoft
description: W tym artykule opisano różne metryki i Dzienniki dostępu, które obsługuje usługę drzwiami frontowymi Azure
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
ms.openlocfilehash: 16770ea0a320b3d9f081cc21a102ab050a6467f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736805"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Monitorowanie metryk i dzienników w usłudze Azure Service drzwi

Za pomocą usługi Azure Service drzwiami frontowymi, można monitorować zasoby w następujący sposób:

- **Metryki**. Usługa Application Gateway obecnie ma siedem metryki, aby wyświetlić liczniki wydajności.
- **Dzienniki**. Aktywności i dziennikach diagnostycznych Zezwalaj na wydajność, dostępu i inne dane, które mają być zapisane lub używane z zasobu do celów monitorowania.

### <a name="metrics"></a>Metryki

Metryki są funkcją dla niektórych zasobów platformy Azure, które umożliwiają wyświetlanie liczników wydajności w portalu. Dostępne metryki drzwiami frontowymi są następujące:

| Metryka | Nazwa wyświetlana metryki | Jednostka | Wymiary | Opis |
| --- | --- | --- | --- | --- |
| RequestCount | Liczba żądań | Licznik | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba żądań klientów obsługiwanych przez wejściu.  |
| RequestSize | Rozmiar żądania | Bajty | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba bajtów wysłanych jako żądania od klientów na wejściu. |
| ResponseSize | Rozmiar odpowiedzi | Bajty | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Liczba bajtów wysłanych jako odpowiedzi z wejściu do klientów. |
| TotalLatency | Całkowity czas oczekiwania | Milisekundy | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Czas jest obliczany od żądania klienta odebranych przez drzwiami frontowymi, dopóki klient potwierdzenia ostatni bajt odpowiedzi z drzwiami frontowymi. |
| BackendRequestCount | Liczba żądań wewnętrznej bazy danych | Licznik | HttpStatus</br>HttpStatusGroup</br>Zaplecze | Liczba żądań wysyłanych z wejściu do zaplecza. |
| BackendRequestLatency | Opóźnienia żądania wewnętrznej bazy danych | Milisekundy | Zaplecze | Czas obliczonym na podstawie Jeśli żądanie zostało wysłane przy wejściu do wewnętrznej bazy danych, do chwili drzwiami frontowymi odebrania ostatniego bajtu odpowiedzi z wewnętrznej bazy danych. |
| BackendHealthPercentage | Wartość procentowa kondycję wewnętrznej bazy danych | Procent | Zaplecze</br>BackendPool | Procent pomyślnych kondycji sondy z wejściu do zaplecza. |
| WebApplicationFirewallRequestCount | Liczba żądań zapory aplikacji sieci Web | Licznik | PolicyName</br>RuleName</br>Akcja | Liczba żądań klienta przetwarzanych przez drzwiami frontowymi zabezpieczeń warstwy aplikacji. |

## <a name="activity-log"></a>Dzienniki aktywności

Dzienniki aktywności udostępniają informacje o operacjach na wejściu usługi. Określają również co, kto i dla każdego zapisu (put, post lub delete) wykonywanych na wejściu usługi.

>[!NOTE]
>Dzienniki aktywności nie obejmują operacji odczytu (get). Te aktualizacje nie obejmują również operacje wykonywane przy użyciu witryny Azure portal lub oryginalny interfejs API zarządzania.

Dzienniki aktywności dostępu w usłudze drzwiami frontowymi lub wszystkich dzienników z zasobów platformy Azure w usłudze Azure Monitor. Aby wyświetlić dzienniki aktywności:

1. Wybierz wystąpienie usługi wejściu.
2. Wybierz **dziennika aktywności**.

    ![Dziennik aktywności](./media/front-door-diagnostics/activity-log.png)

3. Wybierz zakres filtrowania, a następnie wybierz pozycję **Zastosuj**.

## <a name="diagnostic-logging"></a>Dzienniki diagnostyczne
Dzienniki diagnostyczne zawierają bogate informacje o operacjach i błędach, które są ważne na potrzeby inspekcji i rozwiązywania problemów. Dzienniki diagnostyczne różnią się od dzienników aktywności.

Dzienniki aktywności udostępniają szczegółowe dane operacji na zasoby platformy Azure. Dzienniki diagnostyczne udostępniają szczegółowe dane operacji wykonanych przez zasób. Aby uzyskać więcej informacji, zobacz [dzienników diagnostycznych usługi Azure Monitor](../azure-monitor/platform/diagnostic-logs-overview.md).

![Dzienniki diagnostyczne](./media/front-door-diagnostics/diagnostic-log.png)

Aby skonfigurować dzienniki diagnostyczne usługi drzwi wejściowe:

1. Wybierz usługę Azure drzwiami frontowymi.

2. Wybierz **ustawień diagnostycznych**.

3. Wybierz **Włącz diagnostykę**. Archiwizuj dzienniki diagnostyczne wraz z metrykami na konto magazynu, przekazywać je strumieniowo do Centrum zdarzeń lub wysyłać dzienniki usługi Azure Monitor.

Usługa drzwiami frontowymi udostępnia obecnie dzienniki diagnostyczne (wsadowe co godzinę). Dzienniki diagnostyczne zawierają poszczególnych żądań interfejsu API zgodny z następującym schematem AP. Każdy wpis:

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

- [Utwórz profil drzwi](quickstart-create-front-door.md)
- [Jak działa drzwi](front-door-routing-architecture.md)
