---
title: Obsługa zduplikowanych danych w Eksploratorze danych platformy Azure
description: W tym temacie opisano różne podejścia do przeciwdziałania zduplikowanych danych, korzystając z Eksploratora danych usługi Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 8f55b6dfb7b5bc9eda675aca4ed80a66b8a25a7f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60445774"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Obsługa zduplikowanych danych w Eksploratorze danych platformy Azure

Wysyłanie danych do chmury urządzeń Obsługa lokalnej pamięci podręcznej danych. W zależności od rozmiaru danych lokalnej pamięci podręcznej może być przechowywanie danych w celu dni lub nawet miesięcy. Chcesz chronić baz danych analitycznych z nieprawidłowo urządzeń, które ponownie wysłać dane w pamięci podręcznej i powodować duplikacji danych w bazie danych analitycznych. W tym temacie opisano najlepsze praktyki dotyczące obsługi zduplikowanych danych dla tego rodzaju scenariuszy.

Najlepsze rozwiązanie w zakresie duplikacji danych uniemożliwia dublowania. Jeśli to możliwe należy rozwiązać ten problem wcześniej w potoku danych, zapisuje koszty związane z przenoszenie danych do potoku danych, co pozwala uniknąć zasobów podstawą przedstawiają analizę wydatków radzić sobie z zduplikowane dane pozyskane do systemu. W sytuacjach, w których nie można zmodyfikować w systemie źródłowym, istnieją różne sposoby radzenia sobie z tego scenariusza.

## <a name="understand-the-impact-of-duplicate-data"></a>Zrozumienie wpływu zduplikowanych danych

Monitoruj procent zduplikowanych danych. Po odnalezieniu procent zduplikowane dane można analizować zakres problemu i wpływu na działalność i wybrać odpowiednie rozwiązanie.

Przykładowe zapytanie, aby zidentyfikować procent zduplikowane rekordy:

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>Rozwiązania do obsługi zduplikowanych danych

### <a name="solution-1-dont-remove-duplicate-data"></a>Rozwiązanie #1: Nie usuwaj zduplikowanych danych

Dowiedz się, Twoje wymagania biznesowe i tolerancję zduplikowanych danych. Niektóre zestawy danych można zarządzać za pomocą określonego procentu zduplikowanych danych. Jeśli zduplikowane dane nie ma duży wpływ, możesz zignorować swojej obecności. Zaletą nie usuwa zduplikowane dane to bez dodatkowych nakładów na wydajności procesu lub zapytanie wprowadzania.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Rozwiązanie #2: Obsługa zduplikowane wiersze w zapytaniu

Innym rozwiązaniem jest, aby odfiltrować zduplikowane wiersze danych podczas zapytania. [ `arg_max()` ](/azure/kusto/query/arg-max-aggfunction) Zagregowane funkcja może służyć do filtrowania zduplikowanych rekordów i zwraca ostatni rekord na podstawie sygnatura czasowa (lub inną kolumnę). Zaletą tej metody jest szybsze przetwarzanie, ponieważ deduplikacja występuje podczas przeszukiwania. Ponadto wszystkie rekordy (także zduplikowanych) są dostępne do inspekcji i rozwiązywania problemów. Wadą korzystania `arg_max` funkcji jest dodatkowe przeszukiwania i obciążenie procesora CPU, za każdym razem, gdy dane są badane. W zależności od ilości danych, którego dotyczy kwerenda to rozwiązanie może stać się nie działa lub korzystanie z pamięci i będzie wymagać przełączanie na inne opcje.

W poniższym przykładzie możemy wykonać zapytanie o ostatni rekord pozyskiwanych dla zestawu kolumn, które określają unikatowe rekordy:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

To zapytanie można także umieścić wewnątrz funkcji zamiast wysyła zapytanie bezpośrednio w tabeli:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Rozwiązanie #3: Filtrowanie duplikatów w procesie pozyskiwania

Innym rozwiązaniem jest filtrowanie duplikatów w procesie pozyskiwania. System zignoruje zduplikowanych danych, podczas wprowadzania do tabel Kusto. Dane są pozyskiwane do tabeli przejściowej i skopiowany do innej tabeli po usunięciu zduplikowanych wierszy. Zaletą tego rozwiązania jest to znacznie zwiększa wydajność zapytań w porównaniu do poprzedniego rozwiązania. Wady obejmują czas zwiększenia pozyskiwania i koszty przechowywania dodatkowych danych.

Poniższy przykład przedstawia tę metodę:

1. Tworzenie innej tabeli za pomocą tego samego schematu:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Tworzenie funkcji, aby odfiltrować zduplikowane rekordy, zapobiegających przyłączany nowych rekordów z nich, które wcześniej odebrane.

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > Sprzężeń są zależne od Procesora CPU, operacje i dodać dodatkowe obciążenia w systemie.

1. Ustaw [zasad aktualizacji](/azure/kusto/management/update-policy) na `DeviceEventsUnique` tabeli. Zasady aktualizacji jest aktywowany, gdy nowe dane są umieszczane w `DeviceEventsAll` tabeli. Aparat Kusto automatycznie powoduje uruchomienie funkcji jako nowe [zakresów](/azure/kusto/management/extents-overview) są tworzone. Obejmuje przetwarzanie danych nowo utworzone. Polecenie odwzorowywały tabeli źródłowej (`DeviceEventsAll`), tabela docelowa (`DeviceEventsUnique`), a funkcja `RemoveDuplicatesDeviceEvents` ze sobą, aby utworzyć zasady aktualizacji.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Zasady aktualizacji rozszerza czas trwania pozyskiwania, ponieważ dane są filtrowane podczas pozyskiwania, a następnie dwa razy pozyskane (Aby `DeviceEventsAll` tabeli i `DeviceEventsUnique` tabeli).

1. (Opcjonalnie) Ustaw niższy przechowywanie danych na `DeviceEventsAll` tabeli, aby uniknąć przechowywania kopii danych. Wybierz liczbę dni, w zależności od ilości danych i czas, aby zachować dane dotyczące rozwiązywania problemów. Możesz ustawić na `0d` dni przechowywania, aby zapisać KWS i zwiększyć wydajność, ponieważ dane nie przekazywane do magazynu.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Podsumowanie

Duplikacji danych mogą być obsługiwane na wiele sposobów. Ocenę opcji dokładnie, biorąc pod uwagę ceny do wydajności, aby określić poprawną metodę dla Twojej firmy.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Pisanie zapytań dla usługi Azure Data Explorer](write-queries.md)
