---
title: Obsługa zduplikowanych danych w Eksploratorze danych platformy Azure
description: W tym temacie zostaną wyświetleni różne podejścia do radzenia sobie z duplikatami danych podczas korzystania z Usługi Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 60ec2b86e0205060f907f1fe39d084dca3aac1cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68608232"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Obsługa zduplikowanych danych w Eksploratorze danych platformy Azure

Urządzenia wysyłające dane do chmury utrzymują lokalną pamięć podręczną danych. W zależności od rozmiaru danych lokalna pamięć podręczna może przechowywać dane przez dni lub nawet miesiące. Chcesz chronić analityczne bazy danych przed nieprawidłowo działającymi urządzeniami, które ponownie wysuń buforowane dane i powodują powielanie danych w analitycznej bazie danych. W tym temacie opisano najlepsze rozwiązania dotyczące obsługi zduplikowanych danych dla tych typów scenariuszy.

Najlepszym rozwiązaniem do powielania danych jest zapobieganie powielaniu. Jeśli to możliwe, rozwiąż problem wcześniej w potoku danych, który oszczędza koszty związane z przenoszeniem danych wzdłuż potoku danych i pozwala uniknąć wydatków zasobów na radzenie sobie z duplikatami danych polanych do systemu. Jednak w sytuacjach, gdy system źródłowy nie można zmodyfikować, istnieją różne sposoby radzenia sobie z tym scenariuszem.

## <a name="understand-the-impact-of-duplicate-data"></a>Zrozumienie wpływu zduplikowanych danych

Monitorowanie procentu zduplikowanych danych. Po wykryciu procentu zduplikowanych danych można analizować zakres problemu i wpływ na działalność oraz wybrać odpowiednie rozwiązanie.

Przykładowa kwerenda identyfikująca procent zduplikowanych rekordów:

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

### <a name="solution-1-dont-remove-duplicate-data"></a>#1 rozwiązania: nie usuwaj zduplikowanych danych

Poznaj wymagania biznesowe i tolerancję zduplikowanych danych. Niektóre zestawy danych mogą zarządzać z pewnym procentem zduplikowanych danych. Jeśli zduplikowane dane nie mają większego wpływu, można zignorować ich obecność. Zaletą nie usuwanie zduplikowanych danych nie jest dodatkowe obciążenie procesu pozyskiwania lub wydajności kwerendy.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>#2 rozwiązania: Obsługa zduplikowanych wierszy podczas kwerendy

Inną opcją jest odfiltrowanie zduplikowanych wierszy w danych podczas kwerendy. Zagregowana [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) funkcja może służyć do filtrowania zduplikowanych rekordów i zwracania ostatniego rekordu na podstawie sygnatury czasowej (lub innej kolumny). Zaletą korzystania z tej metody jest szybsze pozyskiwania, ponieważ de-duplikacji występuje w czasie kwerendy. Ponadto wszystkie rekordy (w tym duplikaty) są dostępne do inspekcji i rozwiązywania problemów. Wadą `arg_max` korzystania z funkcji jest dodatkowy czas kwerendy i obciążenia procesora CPU za każdym razem, gdy dane są wyszukiwane. W zależności od ilości danych, o które się pytano, to rozwiązanie może stać się nie funkcjonalne lub zużywające pamięć i będzie wymagać przełączenia na inne opcje.

W poniższym przykładzie możemy zbadać ostatni rekord po spożycony dla zestawu kolumn, które określają unikatowe rekordy:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Ta kwerenda może być również umieszczona wewnątrz funkcji zamiast bezpośredniego wykonywania zapytań w tabeli:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>#3 rozwiązanie: Filtrowanie duplikatów podczas procesu pozyskiwania

Innym rozwiązaniem jest filtrowanie duplikatów podczas procesu pozyskiwania. System ignoruje zduplikowane dane podczas pozyskiwania do tabel Kusto. Dane są ponajmowane do tabeli przemieszczania i kopiowane do innej tabeli po usunięciu zduplikowanych wierszy. Zaletą tego rozwiązania jest to, że wydajność kwerendy znacznie poprawia się w porównaniu do poprzedniego rozwiązania. Wady obejmują wydłużenie czasu pozyskiwania i dodatkowe koszty przechowywania danych. Ponadto to rozwiązanie działa tylko wtedy, gdy duplikacje nie są jednocześnie poławiane. Jeśli istnieje wiele równoczesnych pozyskiwania zawierających zduplikowane rekordy, wszystkie mogą być pojmowane, ponieważ proces deduplikacji nie znajdzie żadnych istniejących pasujących rekordów w tabeli.    

Poniższy przykład przedstawia tę metodę:

1. Utwórz inną tabelę z tym samym schematem:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Utwórz funkcję, aby odfiltrować zduplikowane rekordy, zapobiegając dołączaniu do nowych rekordów z wcześniej pożyczonymi rekordami.

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
    > Sprzężenia są operacje związane z procesorem CPU i dodać dodatkowe obciążenie w systemie.

1. Ustaw [zasady](/azure/kusto/management/update-policy) `DeviceEventsUnique` aktualizacji na stole. Zasady aktualizacji są aktywowane, gdy `DeviceEventsAll` nowe dane trafiają do tabeli. Aparat Kusto automatycznie wykona tę funkcję w miarę tworzenia nowych [zakresów.](/azure/kusto/management/extents-overview) Przetwarzanie jest ograniczone do nowo utworzonych danych. Następujące polecenie łączy tabelę`DeviceEventsAll`źródłową (`DeviceEventsUnique`), tabelę docelową ( i funkcję `RemoveDuplicatesDeviceEvents` razem w celu utworzenia zasad aktualizacji.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Aktualizuj zasady wydłuża czas trwania pozyskiwania, ponieważ dane są filtrowane podczas pozyskiwania, a `DeviceEventsAll` następnie pociągnięte dwukrotnie (do tabeli i do `DeviceEventsUnique` tabeli).

1. (Opcjonalnie) Ustaw mniejsze przechowywanie danych `DeviceEventsAll` w tabeli, aby uniknąć przechowywania kopii danych. Wybierz liczbę dni w zależności od ilości danych i czasu przechowywania danych w celu rozwiązania problemu. Możesz ustawić go `0d` na przechowywanie dni, aby zapisać COGS i zwiększyć wydajność, ponieważ dane nie są przekazywane do magazynu.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Podsumowanie

Powielanie danych może być obsługiwane na wiele sposobów. Dokładnie ocenić opcje, biorąc pod uwagę cenę i wydajność, aby określić właściwą metodę dla swojej firmy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Pisanie zapytań dla Eksploratora danych platformy Azure](write-queries.md)
