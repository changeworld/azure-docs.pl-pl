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
ms.openlocfilehash: 643ae03a9350868b172d99b2af7ce23e34fedf47
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998002"
---
# <a name="monitoring-metrics-for-front-door"></a>Monitorowanie metryk drzwi

Za pomocą usługi Azure Service drzwiami frontowymi, można monitorować swoje kluczowe metryki, aby zweryfikować konfigurację drzwiami frontowymi, wraz z użycia, kondycji i wydajności lub usługi drzwiami frontowymi.

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
| BackendHealthPercentage | Wartość procentowa kondycję wewnętrznej bazy danych | Procent | Zaplecze</br>Ustawień | Procent pomyślnych kondycji sondy z wejściu do zaplecza. |
| WebApplicationFirewallRequestCount | Liczba żądań zapory aplikacji sieci Web | Licznik | PolicyName</br>RuleName</br>Akcja | Liczba żądań klienta przetwarzanych przez drzwiami frontowymi zabezpieczeń warstwy aplikacji. |


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [tworzenie drzwiami frontowymi](quickstart-create-front-door.md).
- Dowiedz się, [działania drzwiami frontowymi](front-door-routing-architecture.md).
