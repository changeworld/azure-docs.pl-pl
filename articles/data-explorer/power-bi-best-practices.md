---
title: Najlepsze rozwiązania dotyczące używania Power BI do wykonywania zapytań i wizualizacji danych Eksplorator danych platformy Azure
description: Ten artykuł zawiera informacje o najlepszych rozwiązaniach dotyczących używania Power BI do wykonywania zapytań i wizualizacji danych Eksplorator danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024036"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Najlepsze rozwiązania dotyczące używania Power BI do wykonywania zapytań i wizualizacji danych Eksplorator danych platformy Azure

Azure Data Explorer to szybka i wysoce skalowalna usługa eksploracji danych na potrzeby danych dziennika i telemetrycznych. [Power BI](https://docs.microsoft.com/power-bi/) to rozwiązanie do analizy biznesowej, które pozwala na wizualizację danych i udostępnianie wyników w całej organizacji. Usługa Azure Eksplorator danych oferuje trzy opcje łączenia się z danymi w Power BI. Użyj [wbudowanego łącznika](power-bi-connector.md), [zaimportuj zapytanie z usługi Azure Eksplorator danych do Power BI](power-bi-imported-query.md)lub Użyj [zapytania SQL](power-bi-sql-query.md). Ten artykuł zawiera wskazówki dotyczące wykonywania zapytań i wizualizacji danych Eksplorator danych platformy Azure przy użyciu Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Najlepsze rozwiązania dotyczące korzystania z Power BI 

Podczas pracy z terabajtami świeżych danych pierwotnych postępuj zgodnie z poniższymi wskazówkami, aby zachować przyciąganie i aktualizowanie Power BI pulpitów nawigacyjnych i raportów:

* **Oświetlenie świetlne** — Przenieś tylko te dane, które są potrzebne do raportów, Power BI. Aby uzyskać szczegółowej analizy interaktywnej, użyj [internetowego interfejsu użytkownika usługi Azure Eksplorator danych](web-query-data.md) , który jest zoptymalizowany pod kątem eksploracji ad hoc przy użyciu języka zapytań Kusto.

* **Model złożony** — używanie [modelu złożonego](https://docs.microsoft.com/power-bi/desktop-composite-models) do łączenia zagregowanych danych dla pulpitów nawigacyjnych najwyższego poziomu z przefiltrowanymi pierwotnych danych operacyjnych. Można jasno zdefiniować, kiedy należy używać danych pierwotnych i kiedy używać widoku agregowanego. 

* **Tryb importu i tryb zapytania bezpośredniego** — Używanie trybu **importu** do interakcji z mniejszymi zestawami danych. Używaj trybu **zapytania bezpośredniego** w przypadku dużych, często aktualizowanych zestawów danych. Na przykład Utwórz tabele wymiarów przy użyciu trybu **importu** , ponieważ są one małe i rzadko nie zmieniają się. Ustaw interwał odświeżania zgodnie z oczekiwaną częstotliwością aktualizacji danych. Utwórz tabele faktów przy użyciu trybu **zapytania bezpośredniego** , ponieważ te tabele są duże i zawierają dane pierwotne. Te tabele służą do prezentowania przefiltrowanych danych przy użyciu Power BI [przeglądania szczegółowego](https://docs.microsoft.com/power-bi/desktop-drillthrough).

* **Równoległość** — platforma Azure Eksplorator danych jest skalowalną w poziomie platformą danych, dlatego można poprawić wydajność renderowania pulpitu nawigacyjnego przez zwiększenie równoległości przepływu pełnego w następujący sposób:

   * Zwiększ liczbę [jednoczesnych połączeń w zapytaniu bezpośrednim w Power BI](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * [Aby poprawić równoległość, należy użyć słabej spójności](/azure/kusto/concepts/queryconsistency). Może to mieć wpływ na świeżość danych.

* **Efektywne fragmentatory** — Użyj [fragmentatorów synchronizacji](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) , aby uniemożliwić raportom ładowanie danych przed ich przystąpieniem. Po dokonaniu struktury zestawu danych Umieść wszystkie wizualizacje i Oznacz wszystkie fragmentatory, możesz wybrać fragmentator synchronizacji, aby załadować tylko potrzebne dane.

* **Użyj filtrów** — można użyć dowolnej liczby filtrów Power BI, aby skoncentrować się na usłudze Azure Eksplorator danych Search na odpowiednich fragmentówch danych.

* **Wydajne wizualizacje** — wybierz najbardziej wykonane wizualizacje danych.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Porady dotyczące używania łącznika usługi Azure Eksplorator danych na potrzeby Power BI do wykonywania zapytań dotyczących danych

W poniższej sekcji przedstawiono porady i wskazówki dotyczące korzystania z języka zapytań Kusto z Power BI. Użyj [łącznika usługi Azure Eksplorator danych](power-bi-connector.md) , aby Power BI do wizualizacji danych

### <a name="complex-queries-in-power-bi"></a>Złożone zapytania w Power BI

Złożone zapytania są łatwiej wyrażone w Kusto niż w Power Query. Powinny być zaimplementowane jako [funkcje Kusto](/azure/kusto/query/functions)i wywoływane w Power BI. Ta metoda jest wymagana w przypadku używania **zapytania bezpośredniego** z instrukcjami `let` w zapytaniu Kusto. Ponieważ Power BI sprzęga dwa zapytania, a instrukcje `let` nie mogą być używane z operatorem `join`, mogą wystąpić błędy składniowe. W związku z tym Zapisz każdą część sprzężenia jako funkcję Kusto i zezwól Power BI do łączenia tych dwóch funkcji jednocześnie.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Symulowanie operatora względnej daty i godziny

Power BI nie zawiera operatora *względnej* daty i godziny, takiego jak `ago()`.
Aby symulować `ago()`, użyj kombinacji `DateTime.FixedLocalNow()` i `#duration` funkcji Power BI.

Zamiast tego zapytania przy użyciu operatora `ago()`:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Użyj następującego równoważnego zapytania:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Osiąganie limitów zapytania Kusto 

Kusto zapytania zwracają domyślnie do 500 000 wierszy lub 64 MB, zgodnie z opisem w [limitach zapytania](/azure/kusto/concepts/querylimits). Te ustawienia domyślne można zastąpić przy użyciu **opcji zaawansowanych** w oknie połączenia **usługi Azure Eksplorator danych (Kusto)** :

![Opcje zaawansowane](media/power-bi-best-practices/advanced-options.png)

Te opcje umożliwiają [Ustawianie instrukcji](/azure/kusto/query/setstatement) przy użyciu zapytania, aby zmienić domyślne limity zapytania:

  * **Limit rekordów wyników zapytania** , który generuje `set truncationmaxrecords`
  * **Limit rozmiaru danych wyników zapytania w bajtach** generuje `set truncationmaxsize`
  * **Wyłącz obcinanie zestawu wyników** generuje `set notruncation`

### <a name="using-query-parameters"></a>Korzystanie z parametrów zapytania

Możesz użyć [parametrów zapytania](/azure/kusto/query/queryparametersstatement) , aby dynamicznie modyfikować zapytanie. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Używanie parametru zapytania w szczegółach połączenia

Użyj parametru zapytania, aby odfiltrować informacje w kwerendzie i zoptymalizować wydajność zapytań.
 
W oknie **Edytuj zapytania** , > **Home** **Edytor zaawansowany**

1. Znajdź następującą sekcję zapytania:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Na przykład:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Zastąp odpowiednią część zapytania parametrem. Podziel zapytanie na wiele części i połącz je z powrotem przy użyciu znaku handlowego "i" (&) wraz z parametrem.

   Na przykład w powyższym zapytaniu zajmiemy się częścią `State == 'ALABAMA'` i dzieli ją na: `State == '` i `'` i umieścimy `State` parametr między nimi:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Jeśli zapytanie zawiera znaki cudzysłowu, zakoduj je poprawnie. Na przykład następujące zapytanie: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   pojawi się w **Edytor zaawansowany** w następujący sposób z dwoma cudzysłowami:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Powinien zostać zastąpiony następującym zapytaniem zawierającym trzy cudzysłowy:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Użyj parametru zapytania w krokach zapytania

W każdym kroku zapytania, który go obsługuje, można użyć parametru zapytania. Na przykład można filtrować wyniki na podstawie wartości parametru.

![Filtrowanie wyników przy użyciu parametru](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Nie używaj harmonogramu odświeżania danych Power BI do wydawania poleceń sterowania do Kusto

Power BI obejmuje harmonogram odświeżania danych, który może okresowo wydawać zapytania względem źródła danych. Tego mechanizmu nie należy używać do planowania poleceń sterowania do Kusto, ponieważ Power BI zakłada, że wszystkie zapytania są tylko do odczytu.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI może wysyłać tylko krótkie (&lt;2000 znaków) zapytania do Kusto

W przypadku uruchamiania zapytania w Power BI powoduje następujący błąd: _"DataSource. Error: Web. Contents nie może pobrać zawartości z..."_ długość zapytania jest najprawdopodobnie dłuższa niż 2000 znaków. Power BI używa **PowerQuery** do wysyłania zapytań do Kusto przez wystawienie żądania HTTP GET, które koduje zapytanie jako część pobieranego identyfikatora URI. W związku z tym zapytania Kusto wystawiane przez Power BI są ograniczone do maksymalnej długości identyfikatora URI żądania (2000 znaków minus małe przesunięcie). Jako obejście można zdefiniować [przechowywaną funkcję](/azure/kusto/query/schema-entities/stored-functions) w Kusto i mieć Power BI używać tej funkcji w zapytaniu.

## <a name="next-steps"></a>Następne kroki

[Wizualizuj dane przy użyciu łącznika Eksplorator danych platformy Azure dla Power BI](power-bi-connector.md)




