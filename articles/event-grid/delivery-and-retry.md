---
title: Usługa Azure Event Grid dostarczanie i ponawianie prób
description: W tym artykule opisano, jak usługi Azure Event Grid dostarcza zdarzenia i sposób obsługi niedostarczone wiadomości.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: spelluru
ms.openlocfilehash: 0945b06f78ac34500f0b16a4a419cff12d1a4734
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812923"
---
# <a name="event-grid-message-delivery-and-retry"></a>Dostarczanie komunikatów siatki zdarzeń, a następnie spróbuj ponownie

W tym artykule opisano, jak usługi Azure Event Grid obsługuje zdarzenia, gdy nie ma potwierdzenia dostarczenia.

Usługi Event Grid oferuje niezawodne dostarczanie. System ten zapewnia każdy komunikat co najmniej raz dla każdej subskrypcji. Zdarzenia są wysyłane bezpośrednio do zarejestrowanych punktu końcowego w każdej subskrypcji. Jeśli punkt końcowy nie otrzymanie zdarzenia, usługi Event Grid ponawia próbę dostarczaniu zdarzenia.

Obecnie usługa Event Grid wysyła każde zdarzenie indywidualnie do subskrybentów. Subskrybent otrzymuje tablicy o liczbie pojedyncze zdarzenie.

## <a name="retry-schedule-and-duration"></a>Harmonogram ponownych prób i czas trwania

Usługa Event Grid czeka 30 sekund na odpowiedź od dostarczania wiadomości. Po 30 sekundach Jeśli punkt końcowy nie odpowiedział, komunikat jest kolejkowana dla ponownych prób. Usługa Event Grid używa zasady ponawiania wykładniczego wycofywania, podczas dostarczania zdarzeń. Usługa Event Grid ponownych prób dostarczenia według następującego harmonogramu na optymalne rozwiązanie:

- 10 sekund
- 30 sekund
- 1 min
- 5 minut
- 10 minut
- 30 minut
- 1 godzina
- Co godzinę przez maksymalnie 24 godziny

Jeśli punkt końcowy odpowie w ciągu 3 minut, usługi Event Grid będzie podejmować próby usunięcia zdarzenia z kolejki ponownych prób na optymalne rozwiązanie, ale duplikaty może nadal można odbierać dane.

Usługa Event Grid dodaje małe losowe do wszystkich kroków ponownych prób i tylko wtedy może pominąć niektóre ponownych prób, jeśli punkt końcowy jest stale złej kondycji, dół przez długi czas lub wydaje się być przeciążeniu.

Deterministyczne zachowania, ustaw czas zdarzenia na żywo i w próby max dostarczania [zasady ponawiania prób subskrypcji](manage-event-delivery.md).

Domyślnie usługi Event Grid wygasa wszystkie zdarzenia, które nie są dostarczane w ciągu 24 godzin. Możesz [Dostosuj zasady ponawiania](manage-event-delivery.md) podczas tworzenia subskrypcji zdarzeń. Podaj maksymalną liczbę prób dostarczenia (wartość domyślna to 30) i zdarzenia time to live (wartość domyślna to 1440 minut).

## <a name="delayed-delivery"></a>Dostarczenie opóźnione

Jako punkt końcowy napotka błędy dostarczania, usługa Event Grid rozpocznie się opóźnienia, dostarczanie i ponawianie prób, zdarzeń do określonego punktu końcowego. Na przykład, jeśli pierwszych dziesięciu zdarzeń publikowanych do punktu końcowego kończą się niepowodzeniem, usługi Event Grid będzie założono, że punkt końcowy ma problemy i zostanie opóźnione, wszystkie kolejne próby *i nowe* dostaw przez pewien czas — w niektórych przypadkach do kilku godzin .

Funkcjonalności opóźnione dostawy ma na celu ochronę punktów końcowych w złej kondycji, a także system usługi Event Grid. Bez wycofywania i Opóźnienie dostarczania do punktów końcowych w złej kondycji zasady ponawiania usługi Event Grid i możliwości woluminu może łatwo przeciążyć systemu.

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

Uwzględnia usługi Event Grid **tylko** następujące kody odpowiedzi HTTP jako pomyślnych dostarczeń. Wszystkie inne stan kody są uważane za dostarczenie nie powiodło się i zostanie ponowiona lub deadlettered zgodnie z potrzebami. Po otrzymaniu kod stanu powodzenia, pełną dostarczania uwzględnia usługi Event Grid.

- 200 OK
- 201 utworzono
- 202 zaakceptowano
- 203 Informacje nieautorytatywne
- 204 No Content

### <a name="failure-codes"></a>Kody błędów

Wszystkie kody w zestawie powyżej (200 204) nie są traktowane jako błędy i zostanie ponowiona. Niektóre z nich mają zasady ponawiania określone, powiązane z nimi opisane poniżej, wykonaj pozostałe wykładniczego wycofywania modelu standardowego. Należy pamiętać, że ze względu na charakter wysoce równoległe architektury usługi Event Grid, sposób ponawiania jest niedeterministyczny. 

| Kod stanu | Zachowanie przy ponowieniu próby |
| ------------|----------------|
| 400 Niewłaściwe żądanie | Spróbuj ponownie za 5 minut lub dłużej (utraconych natychmiast, jeśli Instalator utraconych wiadomości) |
| 401 Brak autoryzacji | Spróbuj ponownie za 5 minut lub dłużej |
| 403 Zabroniony | Spróbuj ponownie za 5 minut lub dłużej |
| 404 — Nie odnaleziono | Spróbuj ponownie za 5 minut lub dłużej |
| 408 — limit czasu żądania | Ponów próbę po 2 minuty lub więcej |
| Jednostka 413 żądania jest zbyt duża | Ponów próbę po 10 sekund lub więcej (utraconych natychmiast, jeśli Instalator utraconych wiadomości) |
| 503 — usługa niedostępna | Po 30 sekundach ponawiania próby lub więcej |
| Wszystkie inne | Ponów próbę po 10 sekund lub więcej |


## <a name="next-steps"></a>Kolejne kroki

* Aby wyświetlić stan wysyłki zdarzeń, zobacz [dostarczanie komunikatów Monitor Event Grid](monitor-event-delivery.md).
* Aby dostosować opcje dostarczania zdarzeń, zobacz [Dead litera, a następnie spróbuj ponownie zasady](manage-event-delivery.md).
