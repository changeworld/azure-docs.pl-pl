---
title: Usługa Azure Event Grid dostarczanie i ponawianie prób
description: W tym artykule opisano, jak usługi Azure Event Grid dostarcza zdarzenia i sposób obsługi niedostarczone wiadomości.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: spelluru
ms.openlocfilehash: 6dfa84eff8dcc104ae6f9c16262f3b1c697df6c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562002"
---
# <a name="event-grid-message-delivery-and-retry"></a>Dostarczanie komunikatów siatki zdarzeń, a następnie spróbuj ponownie

W tym artykule opisano, jak usługi Azure Event Grid obsługuje zdarzenia, gdy nie ma potwierdzenia dostarczenia.

Usługi Event Grid oferuje niezawodne dostarczanie. System ten zapewnia każdy komunikat co najmniej raz dla każdej subskrypcji. Zdarzenia są wysyłane bezpośrednio do zarejestrowanych punktu końcowego w każdej subskrypcji. Jeśli punkt końcowy nie otrzymanie zdarzenia, usługi Event Grid ponawia próbę dostarczaniu zdarzenia.

Obecnie usługa Event Grid wysyła każde zdarzenie indywidualnie do subskrybentów. Subskrybent otrzymuje tablicy o liczbie pojedyncze zdarzenie.

## <a name="retry-schedule-and-duration"></a>Harmonogram ponownych prób i czas trwania

Usługa Event Grid używa zasady ponawiania wykładniczego wycofywania, podczas dostarczania zdarzeń. Jeśli punkt końcowy przestaje odpowiadać, zwraca kod błędu usługi Event Grid ponawia próbę dostarczania według następującego harmonogramu na optymalne rozwiązanie:

1. 10 sekund
1. 30 sekund
1. 1 min
1. 5 minut
1. 10 minut
1. 30 minut
1. 1 godzina
1. Co godzinę przez maksymalnie 24 godziny

Usługa Event Grid dodaje małe losowe do wszystkich kroków ponownych prób i tylko wtedy może pominąć niektóre ponownych prób, jeśli punkt końcowy jest stale złej kondycji, dół przez długi czas lub wydaje się być przeciążeniu.

Deterministyczne zachowania, ustaw czas zdarzenia na żywo i w próby max dostarczania [zasady ponawiania prób subskrypcji](manage-event-delivery.md).

Domyślnie usługi Event Grid wygasa wszystkie zdarzenia, które nie są dostarczane w ciągu 24 godzin. Możesz [Dostosuj zasady ponawiania](manage-event-delivery.md) podczas tworzenia subskrypcji zdarzeń. Podaj maksymalną liczbę prób dostarczenia (wartość domyślna to 30) i zdarzenia time to live (wartość domyślna to 1440 minut).

## <a name="dead-letter-events"></a>Zdarzenia utraconych wiadomości

Kiedy usługi Event Grid może dostarczyć zdarzenia, może wysłać zdarzenia niedostarczone do konta magazynu. Ten proces jest nazywany Obsługa utraconych komunikatów. Domyślnie obsługa utraconych komunikatów nie Włącz usługi Event Grid. Aby ją włączyć, należy określić konto magazynu do przechowywania zdarzeń niedostarczone podczas tworzenia subskrypcji zdarzeń. Możesz pobierać zdarzenia z tego konta magazynu, aby rozwiązać dostaw.

Usługi Event Grid wysyła zdarzenie do lokalizacji utraconych wiadomości, gdy wypróbuje wszystkich jego ponownych prób. Jeśli usługa Event Grid otrzymuje 400 (nieprawidłowe żądanie) lub 413 (żądanie podmiotu zbyt duże) kod odpowiedzi, natychmiast wysyła zdarzenia do punktu końcowego utraconych wiadomości. Te kody odpowiedzi wskazują, że nigdy nie powiedzie się dostarczania zdarzeń.

Występuje opóźnienie pięciu minut od ostatniej próby dostarczenia zdarzeń i kiedy są dostarczane do lokalizacji utraconych wiadomości. To opóźnienie jest przeznaczona do zmniejszenia liczba operacji magazynu obiektów Blob. Jeśli lokalizacja utraconych wiadomości jest niedostępna przez kilka godzin, zdarzenie zostało porzucone.

Przed ustawieniem lokalizacja utraconych wiadomości, musi mieć konto magazynu przy użyciu kontenera. Możesz podać punktu końcowego dla tego kontenera, podczas tworzenia subskrypcji zdarzeń. Punkt końcowy jest w formacie: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Możesz otrzymywać powiadomienia, gdy zdarzenie zostało wysłane do lokalizacji utraconych wiadomości. Odpowiadanie na zdarzenia niedostarczone za pomocą usługi Event Grid [utworzysz subskrypcję zdarzeń](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) dla magazynu obiektów blob utraconych wiadomości. Za każdym razem, gdy magazynu obiektów blob utraconych odbiera niedostarczone zdarzeń usługi Event Grid powiadamia programu obsługi. Program obsługi odpowiada za pomocą akcji, które mają zostać podjęte, jaką niedostarczone zdarzenia.

Na przykład konfigurowania lokalizacja utraconych zobacz [Dead litera, a następnie spróbuj ponownie zasady](manage-event-delivery.md).

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
- Jednostka 413 żądania jest zbyt duża
- Identyfikator URI 414 za długa
- 429 zbyt wiele żądań
- 500 — wewnętrzny błąd serwera
- 503 — usługa niedostępna
- 504 — limit czasu bramy

## <a name="next-steps"></a>Kolejne kroki

* Aby wyświetlić stan wysyłki zdarzeń, zobacz [dostarczanie komunikatów Monitor Event Grid](monitor-event-delivery.md).
* Aby dostosować opcje dostarczania zdarzeń, zobacz [Dead litera, a następnie spróbuj ponownie zasady](manage-event-delivery.md).