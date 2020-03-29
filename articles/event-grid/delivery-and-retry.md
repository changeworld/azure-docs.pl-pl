---
title: Dostarczanie i ponawianie próby usługi Azure Event Grid
description: W tym artykule opisano, jak usługa Azure Event Grid dostarcza zdarzenia i jak obsługuje niedostarczone wiadomości.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: spelluru
ms.openlocfilehash: dda2fd98c4c0d330059156a5ec00baa97ffaf627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921066"
---
# <a name="event-grid-message-delivery-and-retry"></a>Dostarczanie i ponawianie próby dostarczania i ponawianie komunikatów w siatce zdarzeń

W tym artykule opisano, jak usługa Azure Event Grid obsługuje zdarzenia, gdy dostarczanie nie jest potwierdzone.

Usługa Event Grid zapewnia trwałe dostarczanie. Dostarcza każdej wiadomości co najmniej raz dla każdej subskrypcji. Zdarzenia są wysyłane do zarejestrowanego punktu końcowego każdej subskrypcji natychmiast. Jeśli punkt końcowy nie potwierdza odbioru zdarzenia, usługa Event Grid ponawia ponawia dostarczenie zdarzenia.

## <a name="batched-event-delivery"></a>Dostarczanie zdarzeń wsadowych

Domyślnie usługa Event Grid wysyła każde zdarzenie indywidualnie do subskrybentów. Subskrybent odbiera tablicę z jednym zdarzeniem. Można skonfigurować event grid do zdarzeń wsadowych do dostarczania dla lepszej wydajności HTTP w scenariuszach o wysokiej przepływności.

Dostawa wsadowa ma dwa ustawienia:

* **Maksymalna liczba zdarzeń na partię** — maksymalna liczba zdarzeń, które będzie dostarczana w ramach usługi Event Grid na partię. Liczba ta nigdy nie zostanie przekroczona, jednak mniej zdarzeń może zostać dostarczonych, jeśli w momencie publikowania nie są dostępne żadne inne zdarzenia. Usługa Event Grid nie powoduje opóźnienia zdarzeń w celu utworzenia partii, jeśli dostępnych jest mniej zdarzeń. Musi wynosić od 1 do 5000.
* **Preferowany rozmiar partii w kilobajtach** — docelowy pułap rozmiaru partii w kilobajtach. Podobnie jak max zdarzeń, rozmiar partii może być mniejsza, jeśli więcej zdarzeń nie są dostępne w momencie publikowania. Jest możliwe, że partia jest większa niż preferowany rozmiar *partii, jeśli* pojedyncze zdarzenie jest większy niż preferowany rozmiar. Na przykład jeśli preferowany rozmiar jest 4 KB i zdarzenie 10 KB jest wypychany do siatki zdarzeń, zdarzenie 10 KB nadal będą dostarczane w własnej partii, a nie są usuwane.

Dostarczanie wsadowe skonfigurowane na podstawie subskrypcji dla zdarzenia za pośrednictwem portalu, interfejsu wiersza polecenia, programu PowerShell lub SDK.

### <a name="azure-portal"></a>Azure Portal: 
![Ustawienia dostarczania wsadowego](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Podczas tworzenia subskrypcji zdarzeń należy użyć następujących parametrów: 

- **max-events-per-batch** — maksymalna liczba zdarzeń w partii. Musi być liczbą z 1 do 5000.
- **preferowany rozmiar partii w kilobajtach** — preferowany rozmiar partii w kilobajtach. Musi być liczbą z 1 a 1024.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Aby uzyskać więcej informacji na temat korzystania z interfejsu wiersza polecenia platformy Azure z siatką zdarzeń, zobacz [Kierowanie zdarzeń magazynu do punktu końcowego sieci Web za pomocą interfejsu wiersza polecenia platformy Azure.](../storage/blobs/storage-blob-event-quickstart.md)

## <a name="retry-schedule-and-duration"></a>Ponowić harmonogram i czas trwania

Usługa Event Grid czeka 30 sekund na odpowiedź po dostarczeniu wiadomości. Po 30 sekundach, jeśli punkt końcowy nie odpowiedział, wiadomość jest umieszczana w kolejce do ponowiania próby. Usługa Event Grid używa wykładniczej zasady ponawiania ponawiania wstecznej dla dostarczania zdarzeń. Usługa Event Grid ponawia próbę dostawy zgodnie z następującym harmonogramem:

- 10 sekund
- 30 sekund
- 1 min
- 5 minut
- 10 minut
- 30 minut
- 1 godzina
- Co godzinę do 24 godzin

Jeśli punkt końcowy odpowiada w ciągu 3 minut, usługa Event Grid podejmie próbę usunięcia zdarzenia z kolejki ponownych prób na podstawie najlepszego wysiłku, ale duplikaty mogą być nadal odbierane.

Usługa Event Grid dodaje małą randomizację do wszystkich kroków ponawiania próby i może oportunistycznie pominąć niektóre ponownych prób, jeśli punkt końcowy jest stale w złej kondycji, w dół przez długi okres lub wydaje się być przeciążony.

W przypadku zachowania deterministycznego ustaw czas zdarzenia na żywo i maksymalną liczbę prób dostarczenia w [zasadach ponawiania prób subskrypcji](manage-event-delivery.md).

Domyślnie usługa Event Grid wygasa wszystkie zdarzenia, które nie są dostarczane w ciągu 24 godzin. Zasady [ponawiania prób](manage-event-delivery.md) można dostosować podczas tworzenia subskrypcji zdarzeń. Podasz maksymalną liczbę prób dostarczenia (domyślnie jest to 30) i czas zdarzenia do żywo (domyślnie jest to 1440 minut).

## <a name="delayed-delivery"></a>Opóźniona dostawa

Jako punkt końcowy wystąpi błędy dostarczania, usługa Event Grid rozpocznie opóźnianie dostarczania i ponawianie prób wystąpienia zdarzeń do tego punktu końcowego. Na przykład jeśli pierwsze 10 zdarzeń opublikowanych w punkcie końcowym zakończyć zakończyć się niepowodzeniem, usługa Event Grid przyjmie, że punkt końcowy występują problemy i opóźni wszystkie kolejne ponownych prób *i nowych* dostaw przez pewien czas — w niektórych przypadkach do kilku godzin.

Funkcjonalnym celem opóźnionego dostarczania jest ochrona niezdrowych punktów końcowych, a także systemu siatki zdarzeń. Bez wycofywania i opóźnienia dostarczania do niezdrowych punktów końcowych zasady ponawiania prób i możliwości woluminu w układzie zdarzeń mogą łatwo przeciążeć system.

## <a name="dead-letter-events"></a>Zdarzenia z martwą literą

Gdy usługa Event Grid nie może dostarczyć zdarzenia, może wysłać niedostarczone zdarzenie na konto magazynu. Proces ten jest znany jako dead-lettering. Domyślnie siatka zdarzeń nie włącza dead-lettering. Aby go włączyć, należy określić konto magazynu do przechowywania niedostarczonych zdarzeń podczas tworzenia subskrypcji zdarzenia. Ściągasz zdarzenia z tego konta magazynu, aby rozwiązać dostawy.

Usługa Event Grid wysyła zdarzenie do lokalizacji utraconej wiadomości, gdy wypróbowała wszystkie próby ponawiania próby. Jeśli usługa Event Grid otrzyma kod odpowiedzi 400 (złe żądanie) lub 413 (żądanie jednostki zbyt dużej), natychmiast wysyła zdarzenie do punktu końcowego utraconej litery. Te kody odpowiedzi wskazują, że dostarczenie zdarzenia nigdy nie zakończy się pomyślnie.

Istnieje pięć minut opóźnienia między ostatnią próbą dostarczenia zdarzenia i kiedy jest dostarczany do lokalizacji martwej litery. To opóźnienie ma na celu zmniejszenie liczby operacji magazynu obiektów Blob. Jeśli lokalizacja utraconej litery jest niedostępna przez cztery godziny, zdarzenie zostanie przerwane.

Przed ustawieniem lokalizacji utraconej litery musisz mieć konto magazynu z kontenerem. Należy podać punkt końcowy dla tego kontenera podczas tworzenia subskrypcji zdarzenia. Punkt końcowy jest w formacie:`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Możesz chcieć otrzymywać powiadomienia, gdy zdarzenie zostało wysłane do lokalizacji martwego listu. Aby użyć usługi Event Grid do reagowania na niedostarczone zdarzenia, [utwórz subskrypcję zdarzeń](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) dla magazynu obiektów blob z utraconymi literami. Za każdym razem, gdy magazyn obiektów blob utraconych odbiera niedostarczone zdarzenie, usługa Event Grid powiadamia program obsługi. Program obsługi odpowiada za pomocą akcji, które chcesz podjąć w celu pogodzenia niedostarczonych zdarzeń.

Na przykład konfigurowania lokalizacji martwej litery zobacz [Martwa litera i zasady ponawiania prób](manage-event-delivery.md).

## <a name="message-delivery-status"></a>Stan dostarczania wiadomości

Usługa Event Grid używa kodów odpowiedzi HTTP do potwierdzenia otrzymania zdarzeń. 

### <a name="success-codes"></a>Kody sukcesu

Usługa Event Grid uwzględnia **tylko** następujące kody odpowiedzi HTTP jako pomyślne dostawy. Wszystkie inne kody statusu są uważane za nieudane dostawy i zostaną odpowiednio ponowione lub zasypowane. Po otrzymaniu pomyślnego kodu stanu usługa Event Grid uznaje dostarczanie za kompletne.

- 200 ok.
- 201 Utworzono
- 202 Zaakceptowane
- 203 Informacje nieeuzyskające
- 204 Brak treści

### <a name="failure-codes"></a>Kody awarii

Wszystkie inne kody, które nie zostały wymienione w powyższym zestawie (200-204), są uważane za błędy i zostaną ponowione. Niektóre mają określone zasady ponawiania związane z nimi opisane poniżej, wszystkie inne są zgodne ze standardowym wykładniczym modelem wycofywania. Ważne jest, aby pamiętać, że ze względu na bardzo równoległy charakter architektury usługi Event Grid, zachowanie ponawiania próby jest niedeterministyczne. 

| Kod stanu | Ponów próbę zachowania |
| ------------|----------------|
| 400 Zła prośba | Ponów próbę po 5 minutach lub więcej (Deadletter natychmiast, jeśli deadletter konfiguracji) |
| 401 Nieautoryzowane | Ponów próbę po 5 minutach lub więcej |
| 403 Zakazane | Ponów próbę po 5 minutach lub więcej |
| 404 — Nie znaleziono | Ponów próbę po 5 minutach lub więcej |
| 408 — limit czasu żądania | Ponów próbę po 2 minutach lub więcej |
| 413 Jednostka żądania za duża | Ponów próbę po 10 sekundach lub więcej (Deadletter natychmiast, jeśli deadletter konfiguracji) |
| 503 — usługa niedostępna | Ponów próbę po 30 sekundach lub więcej |
| Wszystkie inne | Ponów próbę po 10 sekundach lub więcej |


## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić stan dostaw zdarzeń, zobacz [Wyświetlanie komunikatów w sieci zdarzeń .](monitor-event-delivery.md)
* Aby dostosować opcje dostarczania zdarzeń, zobacz [Martwe listy i zasady ponawiania prób](manage-event-delivery.md).
