---
title: Usługa Azure Event Grid dostarczanie i ponawianie prób
description: W tym artykule opisano, jak usługi Azure Event Grid dostarcza zdarzenia i sposób obsługi niedostarczone wiadomości.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: tomfitz
ms.openlocfilehash: 189484291dd337535fe6988f919326b6e997b290
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39506290"
---
# <a name="event-grid-message-delivery-and-retry"></a>Dostarczanie komunikatów siatki zdarzeń, a następnie spróbuj ponownie 

W tym artykule opisano, jak usługi Azure Event Grid obsługuje zdarzenia w przypadku dostarczania nie zostało potwierdzone.

Usługi Event Grid oferuje niezawodne dostarczanie. System ten zapewnia każdy komunikat co najmniej raz dla każdej subskrypcji. Zdarzenia są wysyłane bezpośrednio do zarejestrowanych elementu webhook w każdej subskrypcji. Jeśli element webhook nie otrzymanie zdarzenia w ciągu 60 sekund od pierwszej próby dostarczenia, usługi Event Grid ponawia próbę dostarczaniu zdarzenia. 

Obecnie usługa Event Grid wysyła każde zdarzenie indywidualnie do subskrybentów. Subskrybent otrzymuje tablicy o liczbie pojedyncze zdarzenie.

## <a name="message-delivery-status"></a>Stan dostarczenia komunikatu

Usługa Event Grid używa kody odpowiedzi HTTP do otrzymanie zdarzenia. 

### <a name="success-codes"></a>Kody operacji zakończonych powodzeniem

Następujące kody odpowiedzi HTTP wskazują, że zdarzenia został pomyślnie dostarczony do usługi elementu webhook. Usługa Event Grid uwzględnia dostarczania ukończone.

- 200 OK
- 202 zaakceptowano

### <a name="failure-codes"></a>Kody błędów

Następujące kody odpowiedzi HTTP wskazują, że próby dostarczenia zdarzeń nie powiodło się. 

- 400 Niewłaściwe żądanie
- 401 Brak autoryzacji
- 404 — Nie odnaleziono
- 408 Limit czasu żądania
- Identyfikator URI 414 za długa
- 500 — wewnętrzny błąd serwera
- 503 — usługa niedostępna
- 504 — limit czasu bramy

Jeśli usługa Event Grid otrzyma błąd, który wskazuje, że punkt końcowy jest tymczasowo niedostępny, próbuje ponownie do wysłania zdarzenia. Jeśli usługi Event Grid odbiera komunikat o błędzie wskazujący dostarczanie nigdy nie powiedzie się, a jeśli tak, to a [utraconych punkt końcowy został skonfigurowany](manage-event-delivery.md), wysyła zdarzenie do endpoint utraconych wiadomości. 

## <a name="retry-intervals-and-duration"></a>Interwałami ponawiania prób i czas trwania

Usługa Event Grid używa zasady ponawiania wykładniczego wycofywania, podczas dostarczania zdarzeń. Jeśli element webhook nie odpowiada, zwraca kod błędu usługi Event Grid ponawia próbę dostarczania według następującego harmonogramu:

1. 10 sekund
2. 30 sekund
3. 1 min
4. 5 minut
5. 10 minut
6. 30 minut
7. 1 godzina

Usługa Event Grid dodaje małe losowe do wszystkich interwałów ponawiania. Po upływie godziny dostarczania zdarzeń próba jest ponawiana co godzinę.

Domyślnie usługi Event Grid wygasa wszystkie zdarzenia, które nie zostały dostarczone w ciągu 24 godzin. Możesz [Dostosuj zasady ponawiania](manage-event-delivery.md) podczas tworzenia subskrypcji zdarzeń. Podaj maksymalną liczbę prób dostarczenia (wartość domyślna to 30) i zdarzenia time to live (wartość domyślna to 1440 minut).

## <a name="dead-letter-events"></a>Zdarzenia utraconych wiadomości

Kiedy usługi Event Grid może dostarczyć zdarzenia, może wysłać zdarzenia niedostarczone do konta magazynu. Ten proces jest nazywany Obsługa utraconych komunikatów. Aby wyświetlić zdarzenia niedostarczone, możesz ściągnąć je z lokalizacji utraconych wiadomości. Aby uzyskać więcej informacji, zobacz [Dead litera, a następnie spróbuj ponownie zasady](manage-event-delivery.md).

## <a name="next-steps"></a>Kolejne kroki

* Aby wyświetlić stan wysyłki zdarzeń, zobacz [dostarczanie komunikatów Monitor Event Grid](monitor-event-delivery.md).
* Aby dostosować opcje dostarczania zdarzeń, zobacz [ustawienia dostarczania zarządzania usługi Event Grid](manage-event-delivery.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć pracę, przy użyciu usługi Event Grid, zobacz [Utwórz i wyznaczać trasy zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).