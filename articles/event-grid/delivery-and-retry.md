---
title: Azure dostarczania zdarzeń siatki i spróbuj ponownie
description: Opisuje sposób siatki zdarzeń Azure zapewnia zdarzeń i sposób obsługi niedostarczone wiadomości.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 8eb6717369b48289bd31dcd1972ce275bc550c77
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="event-grid-message-delivery-and-retry"></a>Dostarczanie komunikatów siatki zdarzeń i spróbuj ponownie 

W tym artykule opisano, jak siatki zdarzeń Azure obsługuje zdarzenia podczas dostarczania nie zostaje potwierdzony.

Zdarzenie siatki udostępnia trwałe dostarczania. Zapewnia każdy komunikat co najmniej raz dla każdej subskrypcji. Zdarzenia są wysyłane bezpośrednio do zarejestrowanych elementu webhook każdej subskrypcji. Jeśli elementu webhook nie otrzymanie zdarzenia w ciągu 60 sekund przy pierwszej próbie dostarczenia, siatki zdarzeń ponowi próbę dostarczania zdarzenia. 

Obecnie siatki zdarzeń wysyła każdego zdarzenia indywidualnie do subskrybentów. Subskrybent odbiera tablicy z pojedynczego zdarzenia.

## <a name="message-delivery-status"></a>Stan dostarczania wiadomości

Siatka zdarzeń używa kody odpowiedzi HTTP do otrzymanie zdarzenia. 

### <a name="success-codes"></a>Kody operacji zakończonych powodzeniem

Następujące kody odpowiedzi HTTP wskazują, że zdarzenia został pomyślnie dostarczony do Twojej elementu webhook. Zdarzenie siatki uwzględnia pełną dostarczania.

- 200 OK
- 202 zaakceptowane

### <a name="failure-codes"></a>Kod błędów

Następujące kody odpowiedzi HTTP wskazują, że próba dostarczania zdarzeń nie powiodła się. 

- 400 Niewłaściwe żądanie
- 401 nieautoryzowane
- 404 — Nie odnaleziono
- 408 Limit czasu żądania
- Identyfikator URI 414 zbyt długa
- 500 — wewnętrzny błąd serwera
- 503 — usługa niedostępna
- 504 — limit czasu bramy

Jeśli zdarzenia siatki odebrał błąd, który wskazuje, że punkt końcowy jest niedostępny, próbuje ponownie wysłać zdarzenia. 

## <a name="retry-intervals-and-duration"></a>Interwał ponawiania i czas trwania

Siatka zdarzeń używa zasady ponawiania wykładniczego wycofywania w celu dostarczania zdarzeń. Jeśli Twoje elementu webhook nie odpowiada, zwraca kod błędu siatki zdarzeń ponowi próbę dostarczania zgodnie z harmonogramem następujące:

1. 10 sekund
2. 30 sekund
3. 1 min
4. 5 minut
5. 10 minut
6. 30 minut
7. 1 godzina

Siatka zdarzeń dodaje małe losowe do wszystkich interwałów ponów próbę. Po upływie godziny dostarczania zdarzeń próba zostanie ponowiona godzinę.

Domyślnie zdarzenia siatki wygasa wszystkie zdarzenia, które nie zostały dostarczone w ciągu 24 godzin.

## <a name="next-steps"></a>Kolejne kroki

* Aby wyświetlić stan dostawy zdarzeń, zobacz [dostarczanie komunikatów Monitora zdarzeń siatki](monitor-event-delivery.md).
* Aby obejrzeć wprowadzenie do siatki zdarzeń, zobacz [o siatki zdarzeń](overview.md).
* Aby szybko rozpocząć korzystanie z siatki zdarzeń, zobacz [tworzenie i tras niestandardowych zdarzeń siatki zdarzeń Azure](custom-event-quickstart.md).