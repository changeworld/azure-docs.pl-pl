---
title: Najważniejsze wskazówki dotyczące używania usługi Power BI do wykonywania zapytań i wizualizacji danych eksploratora danych platformy Azure
description: W tym artykule poznasz najlepsze wskazówki dotyczące używania usługi Power BI do wykonywania zapytań i wizualizacji danych usługi Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251741"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Najważniejsze wskazówki dotyczące używania usługi Power BI do wykonywania zapytań i wizualizacji danych eksploratora danych platformy Azure

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. [Usługa Power BI](https://docs.microsoft.com/power-bi/) to rozwiązanie do analizy biznesowej, które umożliwia wizualizację danych i udostępnianie wyników w całej organizacji. Usługa Azure Data Explorer udostępnia trzy opcje łączenia się z danymi w usłudze Power BI. Użyj [wbudowanego łącznika,](power-bi-connector.md) [zaimportuj zapytanie z Eksploratora danych platformy Azure do usługi Power BI](power-bi-imported-query.md)lub użyj [kwerendy SQL](power-bi-sql-query.md). W tym artykule znajdziesz wskazówki dotyczące wykonywania zapytań i wizualizacji danych usługi Azure Data Explorer za pomocą usługi Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Najważniejsze wskazówki dotyczące korzystania z usługi Power BI 

Podczas pracy z terabajtami świeżych nieprzetworzonych danych postępuj zgodnie z tymi wskazówkami, aby pulpity nawigacyjne i raporty usługi Power BI były zgryźliwe i aktualizowane:

* **Światło podróży** — przesyłanie do usługi Power BI tylko danych potrzebnych do raportów. Aby uzyskać analizę interaktywną, użyj [interfejsu użytkownika sieci Web usługi Azure Data Explorer,](web-query-data.md) który jest zoptymalizowany pod kątem eksploracji ad hoc za pomocą języka zapytań Kusto.

* **Model złożony** — model [złożony](https://docs.microsoft.com/power-bi/desktop-composite-models) umożliwia łączenie zagregowanych danych dla pulpitów nawigacyjnych najwyższego poziomu z filtrowanymi operacyjnymi danymi pierwotnymi. Można jasno określić, kiedy używać nieprzetworzonych danych i kiedy używać widoku zagregowanego. 

* **Tryb importu a tryb zapytania bezpośredniego** — tryb **importu** służy do interakcji z mniejszymi zestawami danych. Tryb **zapytania bezpośredniego** służy do korzystania z dużych, często aktualizowanych zestawów danych. Na przykład utwórz tabele wymiarów przy użyciu trybu **importu,** ponieważ są małe i nie zmieniają się często. Ustaw interwał odświeżania zgodnie z oczekiwaną szybkością aktualizacji danych. Tworzenie tabel faktów przy użyciu trybu **zapytania bezpośredniego,** ponieważ te tabele są duże i zawierają nieprzetworzone dane. Te tabele służy do prezentowania filtrowanych danych za pomocą programu Power BI [w szczegółowym ciągu](https://docs.microsoft.com/power-bi/desktop-drillthrough).

* **Równoległość** — Usługa Azure Data Explorer jest liniowo skalowalną platformą danych, w związku z czym można poprawić wydajność renderowania pulpitu nawigacyjnego, zwiększając równoległość przepływu end-to-end w następujący sposób:

   * Zwiększ liczbę [równoczesnych połączeń w rozsyłaniu](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery)bezpośrednim w usłudze Power BI .

   * Użyj [słabej spójności, aby poprawić równoległość](/azure/kusto/concepts/queryconsistency). Może to mieć wpływ na świeżość danych.

* **Efektywne fragmentatory** — użyj [fragmentatorów synchronizacji,](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) aby zapobiec wczytywania danych raportów, zanim będziesz gotowy. Po ustrukturyzowania zestawu danych, umieszczeniu wszystkich wizualizacji i oznaczeniu wszystkich fragmentatorów można wybrać fragmentator synchronizacji, aby załadować tylko potrzebne dane.

* **Użyj filtrów** — użyj jak największej liczby filtrów usługi Power BI, aby skupić wyszukiwanie w Eksploratorze danych platformy Azure na odpowiednich fragmentach danych.

* **Wydajne wizualizacje** — wybierz najbardziej wydajne wizualizacje danych.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Porady dotyczące używania łącznika Usługi Azure Data Explorer dla usługi Power BI do wykonywania zapytań o dane

Poniższa sekcja zawiera wskazówki i wskazówki dotyczące używania języka zapytań Kusto w usłudze Power BI. Wizualizacja danych za pomocą [łącznika Usługi Azure Data Explorer dla usługi Power BI](power-bi-connector.md)

### <a name="complex-queries-in-power-bi"></a>Złożone zapytania w usłudze Power BI

Złożone zapytania są łatwiej wyrażane w Kusto niż w dodatku Power Query. Powinny one być implementowane jako [funkcje Kusto](/azure/kusto/query/functions)i wywoływane w usłudze Power BI. Ta metoda jest wymagana podczas korzystania `let` z zapytania **bezpośredniego** z instrukcjami w zapytaniu Kusto. Ponieważ usługa Power BI łączy `let` dwa zapytania, a instrukcji `join` nie można używać z operatorem, mogą wystąpić błędy składni. W związku z tym zapisz każdą część sprzężenia jako funkcję Kusto i pozwól usłudze Power BI połączyć te dwie funkcje razem.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Jak symulować względny operator daty i godziny

Usługa Power BI nie zawiera *względnego* operatora `ago()`daty i godziny, takiego jak .
Aby `ago()`symulować, użyj `DateTime.FixedLocalNow()` kombinacji `#duration` funkcji i usługi Power BI.

Zamiast tej kwerendy `ago()` przy użyciu operatora:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Użyj następującej kwerendy równoważnej:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Osiągnięcie limitów zapytań Kusto 

Zapytania Kusto zwracają domyślnie do 500 000 wierszy lub 64 MB, zgodnie z opisem w [limitach zapytań](/azure/kusto/concepts/querylimits). Te ustawienia domyślne można zastąpić za pomocą **opcji zaawansowanych** w oknie połączenia **Usługi Azure Data Explorer (Kusto):You** can override these defaults by using Advanced options in the Azure Data Explorer (Kusto) connection window:

![opcje zaawansowane](media/power-bi-best-practices/advanced-options.png)

Te opcje wystawiają [instrukcje zestaw](/azure/kusto/query/setstatement) z kwerendą, aby zmienić domyślne limity kwerendy:

  * **Ogranicz liczbę rekordów wyników kwerendy**`set truncationmaxrecords`
  * **Limit rozmiaru danych wynik kwerendy w bajtach** generuje`set truncationmaxsize`
  * **Wyłączenie obcinania zestawu wyników** generuje`set notruncation`

### <a name="using-query-parameters"></a>Korzystanie z parametrów kwerendy

Za pomocą [parametrów kwerendy](/azure/kusto/query/queryparametersstatement) można dynamicznie modyfikować kwerendę. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Używanie parametru kwerendy w szczegółach połączenia

Parametr kwerendy służy do filtrowania informacji w kwerendzie i optymalizacji wydajności kwerendy.
 
W oknie **Edytowanie kwerend** **domowych edytor** > **zaawansowany** w domu

1. Znajdź następującą sekcję kwerendy:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Przykład:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Zastąp odpowiednią część kwerendy parametrem. Podziel kwerendę na wiele części i połącz je z powrotem za pomocą ampersand (&) wraz z parametrem.

   Na przykład w powyższej kwerendzie weźmiemy udział i podzielimy go `State == 'ALABAMA'` na: `State == '` i `'` umieścimy `State` między nimi parametr:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Jeśli zapytanie zawiera cudzysłowy, należy je poprawnie zakodować. Na przykład następująca kwerenda: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   pojawi się w **Edytorze zaawansowanym** w następujący sposób z dwoma cudzysłowami:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Należy go zastąpić następującą kwerendą trzema cudzysłowami:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Używanie parametru kwerendy w krokach kwerendy

Parametr kwerendy można użyć w dowolnym kroku kwerendy, który go obsługuje. Na przykład filtruj wyniki na podstawie wartości parametru.

![filtrowanie wyników przy użyciu parametru](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Nie używaj harmonogramu odświeżania danych usługi Power BI do wydawania poleceń sterowania kusto

Usługa Power BI zawiera harmonogram odświeżania danych, który może okresowo wystawiać zapytania względem źródła danych. Ten mechanizm nie powinien być używany do planowania poleceń kontroli do Kusto, ponieważ usługa Power BI zakłada, że wszystkie kwerendy są tylko do odczytu.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Usługa Power BI może&lt;wysyłać do Kusto tylko krótkie ( 2000 znaków) zapytania

Jeśli uruchomienie kwerendy w usłudze Power BI powoduje następujący błąd: _"DataSource.Error: Web.Contents nie można uzyskać zawartości z..."_ kwerenda jest prawdopodobnie dłuższa niż 2000 znaków. Usługa Power BI używa **powerquery** do wykonywania zapytań do kusto, wystawiając żądanie HTTP GET, które koduje kwerendę jako część pobieranego identyfikatora URI. W związku z tym zapytania Kusto wydane przez program Power BI są ograniczone do maksymalnej długości identyfikatora URI żądania (2000 znaków, minus małe przesunięcie). Aby obejść ten problem, można zdefiniować [funkcję przechowywaną](/azure/kusto/query/schema-entities/stored-functions) w Kusto i mieć power bi używać tej funkcji w kwerendzie.

## <a name="next-steps"></a>Następne kroki

[Wizualizuj dane przy użyciu łącznika Usługi Azure Data Explorer dla usługi Power BI](power-bi-connector.md)




