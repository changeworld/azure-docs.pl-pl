---
title: Obsługa zduplikowanych danych na platformie Azure Eksplorator danych
description: W tym temacie przedstawiono różne podejścia do obsługi zduplikowanych danych w przypadku korzystania z usługi Azure Eksplorator danych.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 60ec2b86e0205060f907f1fe39d084dca3aac1cd
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608232"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Obsługa zduplikowanych danych na platformie Azure Eksplorator danych

Urządzenia wysyłające dane do chmury utrzymują lokalną pamięć podręczną danych. W zależności od rozmiaru danych lokalna pamięć podręczna może przechowywać dane przez dni lub nawet miesiące. Chcesz chronić analityczne bazy danych przed nieprawidłowymi urządzeniami, które wysyłają ponownie dane z pamięci podręcznej i powodują duplikowanie danych w analitycznej bazie danych. W tym temacie opisano najlepsze rozwiązania dotyczące obsługi zduplikowanych danych dla tego typu scenariuszy.

Najlepszym rozwiązaniem dla duplikowania danych jest uniemożliwianie duplikowania. Jeśli to możliwe, należy rozwiązać problem wcześniej w potoku danych, co powoduje zaoszczędzenie kosztów związanych z przenoszeniem danych i potoku danych oraz uniknięcie wysyłania zasobów przy użyciu polecenia ping z duplikowanymi danymi pozyskanymi w systemie. Jednak w sytuacjach, w których nie można zmodyfikować systemu źródłowego, istnieją różne sposoby postępowania z tym scenariuszem.

## <a name="understand-the-impact-of-duplicate-data"></a>Zrozumienie wpływu zduplikowanych danych

Monitoruj procent zduplikowanych danych. Po odnalezieniu wartości procentowej zduplikowanych danych można analizować zakres problemu i wpływu na działalność biznesową oraz wybrać odpowiednie rozwiązanie.

Przykładowe zapytanie umożliwiające zidentyfikowanie wartości procentowej zduplikowanych rekordów:

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

## <a name="solutions-for-handling-duplicate-data"></a>Rozwiązania obsługujące zduplikowane dane

### <a name="solution-1-dont-remove-duplicate-data"></a>#1 rozwiązania: Nie usuwaj zduplikowanych danych

Zapoznaj się z wymaganiami biznesowymi i odpornością na zduplikowane dane. Niektóre zestawy danych mogą zarządzać określoną wartością procentową zduplikowanych danych. Jeśli zduplikowane dane nie mają istotnego wpływu, można zignorować jego obecność. Zaletą nieusuwania zduplikowanych danych jest brak dodatkowych nakładów na proces pozyskiwania lub wydajność zapytań.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>#2 rozwiązania: Obsługuj zduplikowane wiersze podczas zapytania

Kolejną opcją jest odfiltrowanie zduplikowanych wierszy w danych podczas zapytania. Funkcja [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) agregująca może służyć do filtrowania duplikatów rekordów i zwracania ostatniego rekordu na podstawie sygnatury czasowej (lub innej kolumny). Zaletą korzystania z tej metody jest szybsze pozyskiwanie, ponieważ podczas wykonywania zapytania występuje nieduplikowanie. Ponadto wszystkie rekordy (w tym duplikaty) są dostępne do inspekcji i rozwiązywania problemów. Wadą korzystania `arg_max` z funkcji jest dodatkowy czas zapytania i obciążenie procesora za każdym razem, gdy są wykonywane zapytania dotyczące danych. W zależności od ilości danych, które są badane, to rozwiązanie może stać się niefunkcjonalne lub zużywać pamięć i będzie wymagało przełączenia na inne opcje.

W poniższym przykładzie wysyłamy zapytanie do ostatniego rekordu pozyskanego dla zestawu kolumn, który określa unikatowe rekordy:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

To zapytanie można także umieścić wewnątrz funkcji, zamiast bezpośrednio wysyłać zapytania do tabeli:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>#3 rozwiązania: Filtruj duplikaty w procesie pozyskiwania

Innym rozwiązaniem jest filtrowanie duplikatów w procesie pozyskiwania. System ignoruje zduplikowane dane podczas pozyskiwania w tabelach Kusto. Dane są pozyskiwane w tabeli przemieszczania i kopiowane do innej tabeli po usunięciu zduplikowanych wierszy. Zaletą tego rozwiązania jest to, że wydajność zapytań znacznie się zwiększa w porównaniu do poprzedniego rozwiązania. Wady obejmują zwiększony czas pozyskiwania i dodatkowe koszty magazynowania danych. Dodatkowe rozwiązanie działa tylko wtedy, gdy duplikowanie nie jest jednocześnie pozyskiwane. Jeśli istnieje wiele współbieżnych operacji pozyskiwania zawierających zduplikowane rekordy, wszystkie mogą zostać pozyskane, ponieważ proces deduplikacji nie odnajdzie żadnych istniejących pasujących rekordów w tabeli.    

Poniższy przykład ilustruje tę metodę:

1. Utwórz kolejną tabelę z tym samym schematem:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Utwórz funkcję, aby odfiltrować zduplikowane rekordy przez odłączenie nowych rekordów z wcześniej pozyskanymi.

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
    > Sprzężenia są operacjami związanymi z PROCESORem i Dodawanie dodatkowego obciążenia w systemie.

1. Ustaw [zasady aktualizacji](/azure/kusto/management/update-policy) w `DeviceEventsUnique` tabeli. Zasady aktualizacji są aktywowane, gdy nowe dane przechodzą do `DeviceEventsAll` tabeli. Aparat Kusto będzie automatycznie wykonywał funkcję w miarę tworzenia nowych [zakresów](/azure/kusto/management/extents-overview) . Przetwarzanie jest ograniczone do nowo utworzonych danych. Następujące polecenie powiąże tabelę źródłową (`DeviceEventsAll`), tabelę docelową (`DeviceEventsUnique`) i funkcję `RemoveDuplicatesDeviceEvents` w celu utworzenia zasad aktualizacji.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Zasady aktualizacji rozszerzają czas pozyskiwania, ponieważ dane są filtrowane podczas pozyskiwania, a następnie pozyskane dwukrotnie (do `DeviceEventsAll` tabeli i `DeviceEventsUnique` tabeli).

1. Obowiązkowe Ustaw niższe przechowywanie danych w `DeviceEventsAll` tabeli, aby uniknąć przechowywania kopii danych. Wybierz liczbę dni w zależności od ilości danych i czas przechowywania danych na potrzeby rozwiązywania problemów. Możesz ustawić tę wartość na `0d` dni przechowywania, aby zaoszczędzić KWS i zwiększyć wydajność, ponieważ dane nie są przekazywane do magazynu.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Podsumowanie

Duplikowanie danych może być obsługiwane na wiele sposobów. Dokładnie Oceń opcje, uwzględniając ceny i wydajność, aby określić poprawną metodę dla firmy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Pisanie zapytań dla usługi Azure Data Explorer](write-queries.md)
