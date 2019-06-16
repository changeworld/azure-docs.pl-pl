---
title: Pisanie zapytań w Eksploratorze danych platformy Azure
description: W tym instruktażu dowiesz się, jak przeprowadzić podstawowych i bardziej zaawansowanych zapytań do Eksploratora danych usługi Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: b1a7e64cf6b85b517bc027d6541d63c9be729734
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60773982"
---
# <a name="write-queries-for-azure-data-explorer"></a>Pisanie zapytań w Eksploratorze danych platformy Azure

W tym artykule dowiesz się, jak używać języka zapytań w Eksploratorze danych w usłudze Azure wykonywać podstawowe zapytania przy użyciu typowych operatorów. Możesz także uzyskać narażenia na niektóre bardziej zaawansowane funkcje języka.

## <a name="prerequisites"></a>Wymagania wstępne

Można uruchomić zapytania, w tym artykule w jeden z dwóch sposobów:

- W Eksploratorze danych platformy Azure *klastrze help* czy skonfigurowaliśmy ułatwiające uczenia.
    [Zaloguj się do klastra](https://dataexplorer.azure.com/clusters/help/databases/samples) za pomocą konta e-mail organizacji, należącym do usługi Azure Active directory.

- Na własny klaster, który zawiera StormEvents przykładowych danych. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie klastra Eksplorator danych platformy Azure i bazy danych](create-cluster-database-portal.md) i [pozyskiwanie danych przykładowych do Eksploratora danych usługi Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>Omówienie języka zapytań

Język zapytań w Eksploratorze danych platformy Azure to żądanie tylko do odczytu do przetwarzania danych i zwracają wartości. Żądanie jest wyrażona w postaci zwykłego tekstu, przy użyciu modelu przepływu danych przeznaczona do umożliwiają łatwe do odczytu, autora i zautomatyzować składni. Zapytanie używa schematu jednostek, które są zorganizowane w hierarchii, które są podobne do bazy danych SQL: baz danych, tabele i kolumny.

Zapytanie składa się z sekwencją instrukcji zapytań, rozdzielone średnikami (`;`), co najmniej jedną instrukcję trwa instrukcji wyrażenia tabelarycznego, który jest instrukcję, która generuje dane uporządkowane według siatki podobne do tabel, kolumn i wierszy. Zapytania tabelaryczne wyrażeń dawać wyników zapytania.

Składnia instrukcji wyrażenia tabelarycznych zawiera dane tabelaryczne przepływu z jednym — operator zapytań tabelarycznych do innego, począwszy od źródła danych (na przykład tabela w bazie danych lub operator, który generuje dane), a następnie przepływają przez zbiór przekształceń danych operatory, które są powiązane ze sobą przy użyciu potoku (`|`) ogranicznik.

Na przykład poniższe zapytanie daje pojedynczej instrukcji, co jest instrukcją tabelarycznych wyrażenia. Wykonywanie instrukcji zaczyna się od odwołania do tabeli o nazwie `StormEvents` (bazy danych, który hostował tej tabeli jest niejawny w tym miejscu, a część informacji o połączeniu). Dane (wiersze) dla tej tabeli następnie są filtrowane według wartości `StartTime` kolumny, a następnie są filtrowane według wartości `State` kolumny. Zapytanie zwraca liczbę wierszy "pozostałych".

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA) **\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

W tym przypadku wynik to:

|Count|
|-----|
|   23|
| |

Aby uzyskać więcej informacji, zobacz [dokumentacja języka zapytań](https://aka.ms/kustolangref).

## <a name="most-common-operators"></a>Najbardziej typowe operatory

Operatory opisanych w tej sekcji są blokami do zrozumienia zapytań w Eksploratorze danych platformy Azure. Większość zapytań, które piszesz będzie zawierać kilka z tych operatorów.

Do uruchamiania zapytań w klastrze help: Wybierz **kliknij, aby uruchomić zapytanie** powyżej każdego zapytania.

Aby uruchamiać kwerendy na własny klaster:

1. Kopiowanie każdego zapytania do zapytania oparte na sieci web aplikacji, a następnie wybierz zapytanie lub umieść kursor w zapytaniu.

1. W górnej części aplikacji wybierz **Uruchom**.

### <a name="count"></a>count

[**count**](https://docs.microsoft.com/azure/kusto/query/countoperator): Zwraca liczbę wierszy w tabeli.

Następujące zapytanie zwraca liczbę wierszy w tabeli StormEvents.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA) **\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>Wypełnij

[**take**](https://docs.microsoft.com/azure/kusto/query/takeoperator): Zwraca maksymalnie określoną liczbę wierszy danych.

Następujące zapytanie zwraca pięć wierszy z tabeli StormEvents. Słowo kluczowe *limit* jest aliasem dla *zająć.*

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d) **\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Nie ma żadnej gwarancji, które rekordy są zwracane, o ile nie są sortowane dane źródłowe.

### <a name="project"></a>project

[**project**](https://docs.microsoft.com/azure/kusto/query/projectoperator): Określa podzbiór kolumn.

Następujące zapytanie zwraca określonego zestawu kolumn.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA) **\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>gdzie

[**gdzie**](https://docs.microsoft.com/azure/kusto/query/whereoperator): Filtruje tabelę do podzestawu wierszy, które spełniają predykat.

Następujące zapytanie filtruje dane według `EventType` i `State`.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>Sortowanie

[**sort**](https://docs.microsoft.com/azure/kusto/query/sortoperator): Sortuj wiersze tabeli wejściowej w kolejności, przez co najmniej jedną kolumnę.

Następujące zapytanie, sortując dane w kolejności według malejącej `DamageProperty`.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> Ważna jest kolejność operacji. Spróbuj umieścić `take 5` przed `sort by`. Pozwala to uzyskać różne wyniki?

### <a name="top"></a>top

[**top**](https://docs.microsoft.com/azure/kusto/query/topoperator): Zwraca pierwszy *N* rekordów posortowanych według określonych kolumn.

Następujące zapytanie zwraca te same wyniki powyżej za pomocą jednego mniej operatora.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>Rozszerzanie

[**Rozszerzanie**](https://docs.microsoft.com/azure/kusto/query/extendoperator): Oblicza pochodnej kolumny.

Następujące zapytanie tworzy nową kolumnę przez obliczenie wartości w każdym wierszu.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

Wyrażenia mogą zawierać zwykłych operatorów (+, -, *, /, %), a szeroką gamę przydatnych funkcji, które można wywoływać.

### <a name="summarize"></a>Podsumowanie

[**Podsumowanie**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): Agreguje grupy wierszy.

Następujące zapytanie zwraca liczbę zdarzeń przez `State`.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA) **\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

**Podsumuj** operator grupowanie wierszy, które mają takie same wartości w **przez** klauzuli, a następnie używa funkcji agregacji (takich jak **liczba**) do łączenia każdego grupy w jednym wierszu. Tak w tym przypadku istnieje wiersz dla każdego stanu i kolumny liczbę wierszy, w tym stanie.

Istnieje szereg funkcje agregacji, a używasz kilka z nich w jednym **Podsumuj** operatora, aby wygenerować kilka kolumn obliczanych. Na przykład użytkownik może uzyskać licznika "burz" w każdym stanie i unikatowy numer "burz" stan, a następnie użyj **górnej** można pobrać stanów najbardziej dotyczy platformy storm.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d) **\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Wynik **Podsumuj** operacji:

- Każda kolumna o nazwie w **przez**

- Dla każdej kolumny obliczane wyrażenie

- Wiersz dla każdej kombinacji według wartości

### <a name="render"></a>Renderowanie

[**Renderowanie**](https://docs.microsoft.com/azure/kusto/query/renderoperator): Renderuje wyniki jako graficzne dane wyjściowe.

Następujące zapytanie Wyświetla wykres kolumnowy.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

Następujące zapytanie Wyświetla wykres czasu proste.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA) **\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

Następujące zapytanie jest liczona zdarzenia według czasu modulo jeden dzień kwanty na godziny i przedstawia wykres czasu.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

Następujące zapytanie porównuje wielu codzienne serii na wykresie czasu.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA) **\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> **Renderowania** operator jest funkcja po stronie klienta, a nie część silnika. Jest on zintegrowany języka w celu ułatwienia. Aplikacja sieci web obsługuje następujące opcje: barchart, columnchart, diagram kołowy, wykres czasu, a linechart. 

## <a name="scalar-operators"></a>Operatory skalarne

W tej sekcji opisano niektóre z najważniejszych operatory skalarne.

### <a name="bin"></a>bin()

[**bin()** ](https://docs.microsoft.com/azure/kusto/query/binfunction): Zaokrągla w dół do liczby całkowitej wielokrotności rozmiar danego pojemnika.

Następujące zapytanie oblicza liczbę o rozmiarze zasobnika jeden dzień.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>case()

[**case()** ](https://docs.microsoft.com/azure/kusto/query/casefunction): Oblicza listę predykatów i zwraca pierwsze wyrażenie wynik, którego predykat jest spełniony, lub ostatecznego **else** wyrażenia. Ten operator umożliwia klasyfikowanie, lub pogrupować dane:

Następujące zapytanie zwraca nową kolumnę `deaths_bucket` i likwidacji numerem grupy.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d) **\]**

```Kusto
StormEvents
| summarize deaths = sum(DeathsDirect) by State
| extend deaths_bucket = case (
    deaths > 50, "large",
    deaths > 10, "medium",
    deaths > 0, "small",
    "N/A")
| sort by State asc
```

### <a name="extract"></a>extract()

[**extract()** ](https://docs.microsoft.com/azure/kusto/query/extractfunction): Pobiera pasuje do wyrażenia regularnego z ciągu tekstowego.

Następujące zapytanie pobiera określone wartości danego atrybutu z śledzenia.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d) **\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

To zapytanie używa **umożliwiają** instrukcję, która wiąże nazwę (w tym przypadku `MyData`) do wyrażenia. W pozostałej części zakresu, w którym **umożliwiają** występuje instrukcja (zakres globalny lub w zakresie treści funkcji), nazwę może służyć do odwoływania się do jego wiązana wartość.

### <a name="parsejson"></a>parse_json()

[**parse_json()** ](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): Interpretuje ciąg jako wartość JSON i zwraca wartość jako dynamiczną. To doskonałe za pomocą **extractjson()** działa, gdy należy wyodrębnić więcej niż jeden element złożony obiekt JSON.

Następujące zapytanie pobiera elementów JSON z tablicy.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d) **\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

Następujące zapytanie pobiera elementy JSON.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA) **\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

Następujące zapytanie pobiera elementów JSON z typem danych dynamicznych.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA) **\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ago()** ](https://docs.microsoft.com/azure/kusto/query/agofunction): Odejmuje dany przedział czasu od bieżącej godziny zegara czasu UTC.

Następujące zapytanie zwraca dane dla ostatnich 12 godzin.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA) **\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek()

[**startofweek()** ](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): Zwraca początek tygodnia zawierającą datę, przesunięte przez przesunięcie, jeśli podany

Następujące zapytanie zwraca początek tygodnia dla innego przesunięcia.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d) **\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

To zapytanie używa **zakres** operatora, który generuje jednokolumnowej tabeli wartości. Zobacz również: [ **startofday()** ](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [ **startofweek()** ](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [ **startofyear()** ](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)), [ **startofmonth()** ](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [ **endofday()** ](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [ **endofweek()**  ](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [ **endofmonth()** ](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction), i [ **endofyear()** ](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>between()

[**between()** ](https://docs.microsoft.com/azure/kusto/query/betweenoperator): Reprezentuje dane wejściowe, która znajduje się wewnątrz zakresu (włącznie).

Następujące zapytanie filtruje dane według w danym zakresie dat.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

Następujące zapytanie filtruje dane według w danym zakresie dat, za pomocą niewielkie zmiany trzy dni (`3d`) od daty rozpoczęcia.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Operatory z tabelarycznego

Kusto ma wiele operatorów tabelarycznych, niektóre z nich znajdują się w innych częściach tego artykułu. W tym miejscu skupimy się na **przeanalizować**. 

### <a name="parse"></a>Analizy

[**analizowanie**](https://docs.microsoft.com/azure/kusto/query/parseoperator): Wylicza wartość wyrażenia ciągu i analizuje jego wartość w co najmniej jedną kolumnę obliczeniową. Istnieją trzy sposoby, aby przeanalizować: proste (ustawienie domyślne), wyrażeń regularnych i spokój.

Następujące zapytanie analizuje śledzenia i wyodrębnia istotne wartości, przy użyciu domyślnej proste analizy. Wyrażenie (nazywane StringConstant) jest wartością ciągu regularnych i wykonanie dopasowania nie jest ścisłym: rozszerzona kolumn muszą być zgodne wymaganych typów.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse EventTrace with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

Następujące zapytanie śledzenia analizuje i wyodrębnia istotne wartości, przy użyciu `kind = regex`. StringConstant może być wyrażeniem regularnym.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind = regex EventTrace with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

Następujące zapytanie śledzenia analizuje i wyodrębnia istotne wartości, przy użyciu `kind = relaxed`. StringConstant jest wartością ciągu regularnych i dopasowanie jest złagodzone: kolumny rozszerzone częściowo może dopasować wymaganych typów.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d) **\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind=relaxed "Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=NULL, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=NULL, previousLockTime=02/17/2016 08:39:01)" with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

## <a name="time-series-analysis"></a>Analiza szeregów czasowych

### <a name="make-series"></a>Marka serii

[**Seria upewnij**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): agreguje razem grup wierszy, takich jak [Podsumuj](https://docs.microsoft.com/azure/kusto/query/summarizeoperator), ale generuje serii (czas) wektor dla każdej kombinacji według wartości.

Następujące zapytanie zwraca zbiór szeregów czasowych dla łącznej liczby zdarzeń storm każdego dnia. Zapytanie obejmuje okres trzech miesięcy, dla każdego stanu wypełnianie Brak pojemniki stała 0:

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Po utworzeniu zestawu serii (czas), można stosować funkcje serii do wykrywania nietypowych kształtów, sezonowych wzorców i wiele innych.

Następujące zapytanie wyodrębnia najważniejsze trzy stany, które dotyczą większości zdarzeń w określonym dniu:

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Aby uzyskać więcej informacji, zapoznaj się z pełną listą [funkcje serii](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions).

## <a name="advanced-aggregations"></a>Agregacje zaawansowane

Omówiono podstawowe agregacje, takie jak **liczba** i **Podsumuj**, we wcześniejszej części tego artykułu. W tej sekcji przedstawiono bardziej zaawansowane opcje.

### <a name="top-nested"></a>zagnieżdżone TOP

[**top-nested**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): Generuje hierarchiczne wyniki górnym, w których każdy poziom jest na podstawie poprzedniego poziomu wartości Przechodzenie do szczegółów.

Ten operator jest przydatne w scenariuszach wizualizacji pulpitu nawigacyjnego lub gdy jest to konieczne odpowiedzieć na pytanie, jak pokazano poniżej: "Znajdź wartość pierwszych N K1 (przy użyciu niektórych agregacji); dla każdego z nich Dowiedz się, jakie są wartości top-M K2 (przy użyciu innego agregacji); ..."

Następujące zapytanie zwraca tabelę hierarchiczną z `State` na najwyższym poziomie, a następnie `Sources`.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>pivot() plugin

[**pivot() plugin**](https://docs.microsoft.com/azure/kusto/query/pivotplugin): Obraca tabelę, włączając niepowtarzalne wartości z jednej kolumny w tabeli wejściowej na wiele kolumn w tabeli wyników. Operator wykonuje agregacje, w którym są wymagane wszystkie pozostałe wartości w kolumnach w końcowych danych wyjściowych.

Następujące zapytanie filtru i obracając wiersze w kolumny.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>dcount()

[**dcount()** ](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): Zwraca szacunkową liczbę unikatowych wartości wyrażenia w tej grupie. Użyj [ **count()** ](https://docs.microsoft.com/azure/kusto/query/countoperator) do policzenia wszystkich wartości.

Następujące zapytanie zlicza różne `Source` przez `State`.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d) **\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()** ](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): Zwraca szacunkową liczbę unikatowych wartości właściwości wyrażenia wierszy, dla których predykat jest wartość true.

Następujące zapytanie liczby unikatowych wartości właściwości `Source` gdzie `DamageProperty < 5000`.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d) **\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcounthll"></a>dcount_hll()

[**dcount_hll()** ](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): Oblicza **funkcja** HyperLogLog wyników (generowany przez [**hll**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) lub [**hll_merge** ](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction).

Następujące zapytanie używa algorytmu HLL do generowania liczby.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA) **\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="argmax"></a>arg_max()

[**arg_max()** ](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): Umożliwia znalezienie wiersz w grupie, która maksymalizuje wyrażenia i zwraca wartość wyrażenia innego (lub * do zwrócenia cały wiersz).

Następujące zapytanie zwraca czas ostatniego raportu powódź w każdym stanie.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()** ](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): Zwraca zestawu różne wartości, które przyjmuje wyrażenia tablic dynamicznych (JSON) w grupie.

Następujące zapytanie zwraca cały czas, po całkowitym został zgłoszony przez każdy stan i tworzy tablicę z zestawu unikatowych wartości.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-expand

[**mV-rozwiń**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): Rozwija próbnego wielu wartości z kolumny dynamicznym typie tak, aby każda wartość w kolekcji pobiera w oddzielnym wierszu. Wszystkie pozostałe kolumny w rozwiniętym wierszu są duplikowane. Jest to odwrotność makelist.

Następujące zapytanie generuje przykładowe dane, tworząc zestaw, a następnie użycie go do zademonstrowania **mv-rozwiń** możliwości.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA) **\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentiles()

[**percentiles()** ](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): Zwraca szacunkową dla określonego [**percentyl najbliższym ranga**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) populacji zdefiniowany przez wyrażenie. Dokładność zależy od gęstości populacji w regionie percentyla. Można używać tylko w kontekście agregacji wewnątrz [**Podsumuj**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator).

Następujące zapytanie oblicza percentyle czasu trwania storm.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Następujące zapytanie oblicza percentyle czasu trwania storm według stanu i normalizuje dane przez 5 minutową pojemników (`5m`).

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Krzyżowe zestawu danych

W tej sekcji opisano elementy, które umożliwiają tworzenie bardziej złożonych zapytań, dołączyć dane w tabelach i zapytań między bazami danych i klastrami.

### <a name="let"></a>Let

[**let**](https://docs.microsoft.com/azure/kusto/query/letstatement): Zwiększa modułowości i ponownego użycia. **Umożliwiają** instrukcji umożliwia przerwać potencjalnie złożonego wyrażenia wiele części, każda powiązana z nazwą i narzędzia compose tych części ze sobą. A **umożliwiają** instrukcji można również tworzyć funkcje zdefiniowane przez użytkownika i widoków (wyrażenia za pośrednictwem tabel, których wyniki powinny wyglądać nowa tabela). Wyrażenia związane **umożliwiają** instrukcji może być typu skalarne tabelarycznych typu lub funkcji zdefiniowanej przez użytkownika (wyrażenia lambda).

Poniższy przykład tworzy zmienną typu tabelarycznym i używa go w wyrażeniu kolejne.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d) **\]**

```Kusto
let LightningStorms =
StormEvents
| where EventType == "Lightning";
let AvalancheStorms =
StormEvents
| where EventType == "Avalanche";
LightningStorms
| join (AvalancheStorms) on State
| distinct State
```

### <a name="join"></a>join

[**join**](https://docs.microsoft.com/azure/kusto/query/joinoperator): Scalenie wierszy z dwóch tabel w celu utworzenia nowej tabeli, dopasowując wartości określone kolumny z każdej tabeli. Kusto obsługuje wiele różnych typów sprzężeń: **fullouter**, **wewnętrzny**, **innerunique**, **leftanti**, **leftantisemi **, **wartości leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter **, **rightsemi**.

Poniższy przykład łączy dwie tabele, w przypadku sprzężenia wewnętrznego.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==) **\]**

```Kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X 
| join kind=inner Y on Key
```

> [!TIP]
> Użyj **gdzie** i **projektu** operatorów w celu zmniejszenia liczby wierszy i kolumn w tabelach danych wejściowych, zanim sprzężenia. Jeśli jedna tabela jest zawsze mniejsza niż ten drugi, użyć jej jako po lewej stronie (gazociągami) sprzężenia. Kolumny dla dopasowania join musi mieć taką samą nazwę. Użyj **projektu** operatora, jeśli to konieczne zmienić nazwę kolumny w jednej z tabel.

### <a name="serialize"></a>Serializacji

[**serialize**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): Serializuje zestawu, aby można było używać funkcji, które wymagają serializowane dane, takie jak wierszy **row_number()** .

Następujące zapytanie powiedzie się, ponieważ dane jest serializowana.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

Zestaw wierszy jest również uznawana za serializacji, jeśli jest to wynikiem: **sortowania**, **górnej**, lub **zakres** operatorów i, opcjonalnie, **projektu**, **away projektu**, **rozszerzyć**, **gdzie**, **przeanalizować**, **mv-rozwiń**, lub **zająć** operatorów.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA) **\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Zapytania wielu baz danych i dla wielu klastrów

[Zapytania wielu baz danych i dla wielu klastrów](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries): Można tworzyć zapytania w bazie danych na tym samym klastrze, odwołując się go jako `database("MyDatabase").MyTable`. Można tworzyć zapytania bazy danych w klastrze zdalnym, odwołując się do niego jako `cluster("MyCluster").database("MyDatabase").MyTable`.

Następujące zapytanie jest wywoływana z jednego klastra i wykonuje zapytania o dane z `MyCluster` klastra. Aby uruchomić to zapytanie, należy użyć własną nazwę klastra i nazwę bazy danych.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Analizowanie użytkowników

Ta sekcja zawiera elementy i zapytania, które pokazują, jak łatwo jest analiza zachowania użytkowników Kusto.

### <a name="activitycountsmetrics-plugin"></a>activity_counts_metrics plugin

[**Wtyczka activity_counts_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): Obliczanie metryki przydatne działań (łączna liczba wartości, wartości liczności unikatowych wartości, liczność unikatowych wartości nowe wartości i zagregowane liczności unikatowych wartości). Metryki są obliczane dla każdego przedziału czasu, a następnie są porównywane i agregowane do i z wszystkich poprzednich okna czasowe.

Następujące zapytanie analizuje rozpowszechnienia wśród użytkowników, obliczając codziennie liczby działań.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d) **\]**

```Kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01),
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
T
| evaluate activity_counts_metrics(UserId, Timestamp, start, end,
window)
```

### <a name="activityengagement-plugin"></a>Wtyczka activity_engagement

[**Wtyczka activity_engagement**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin): Obliczy stosunek zaangażowania działania na podstawie Identyfikatora kolumny przesuwającego się okna osi czasu. **Wtyczka activity_engagement** może służyć do obliczania DAU WAU i użytkownika aktywnego w Miesiącu (codziennych, cotygodniowych i comiesięcznych aktywnych użytkowników).

Następujące zapytanie zwraca współczynnik łączna liczba unikatowych użytkowników, za pomocą aplikacji codziennie w porównaniu z całkowitą liczbą unikatowych użytkowników za pomocą aplikacji, co tydzień, na to okno ruchome siedmiu dni.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> Podczas obliczania DAU/użytkownika aktywnego w Miesiącu, zmień data zakończenia i przenoszenie okresu okna (OuterActivityWindow).

### <a name="activitymetrics-plugin"></a>activity_metrics plugin

[**Wtyczka activity_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin): Obliczanie metryki przydatne działań (Policz różne wartości, liczności unikatowych wartości w nowe wartości, współczynnik utrzymania i współczynnik zmian danych) oparte na bieżące okno okresu, a poprzednie okno okresu.

Następujące zapytanie oblicza współczynnika zmian i okres przechowywania dla danego zestawu danych.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="newactivitymetrics-plugin"></a>Wtyczka new_activity_metrics

[**Wtyczka new_activity_metrics**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin): Oblicza metryki przydatne działań (Policz różne wartości, liczności unikatowych wartości w nowe wartości, współczynnik utrzymania i współczynnik zmian danych) dla kohorty nowych użytkowników. Koncepcja ta wtyczka jest podobny do [**wtyczki activity_metrics**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin), ale koncentruje się na nowych użytkowników.

Następujące zapytanie oblicza przechowywania oraz postęp dokonany w wysokości z oknem tydzień over tygodniowych dla nowej kohorty użytkowników (użytkowników, którzy już korzystać w pierwszym tygodniu).

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d) **\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="sessioncount-plugin"></a>Wtyczka session_count

[**Wtyczka session_count**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): Oblicza liczbę sesji na podstawie Identyfikatora kolumny na osi czasu.

Następujące zapytanie zwraca liczbę sesji. Sesja jest traktowany jako aktywny, jeśli identyfikator użytkownika pojawia się co najmniej raz na przedział czasowy miejsc 100-time, gdy okno ponownie spojrzenie sesji jest odstępów czasu 41.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d) **\]**

```Kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart
```

### <a name="funnelsequence-plugin"></a>Wtyczka funnel_sequence

[**Wtyczka funnel_sequence**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): Oblicza liczności unikatowych wartości użytkowników, którzy miały sekwencji Państw. Pokazuje rozkład poprzedni i dalej stanów, które doprowadziły do lub wykonano przez sekwencję.

Następujące zapytanie pokazuje, jakie zdarzenie się dzieje przed i po wszystkich zdarzeń Tornado w 2007.

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA) **\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnelsequencecompletion-plugin"></a>Wtyczka funnel_sequence_completion

[**Wtyczka funnel_sequence_completion**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): Oblicza lejka ukończonych kroków w ramach różnych okresach czasu.

Następujące zapytanie sprawdza lejka ukończenia sekwencji: `Hail -> Tornado -> Thunderstorm -> Wind` w "Ogólne" razy jednej godziny, cztery godziny, a jeden dzień (`[1h, 4h, 1d]`).

**\[** [**Kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA) **\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Funkcje

W tej sekcji omówiono [ **funkcje**](https://docs.microsoft.com/azure/kusto/query/functions): zapytania wielokrotnego użytku, które są przechowywane na serwerze. Funkcje mogą być wywoływane przez zapytania i inne funkcje (funkcji rekursywnych nie są obsługiwane).

> [!NOTE]
> Nie można utworzyć funkcji w klastrze help jest tylko do odczytu. W tej części, należy użyć własnego klastra testowego.

Poniższy przykład tworzy funkcję, która przyjmuje nazwę stanu (`MyState`) jako argument.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

Poniższy przykład wywołuje funkcję, która pobiera dane dla stanie Teksas.

```Kusto
MyFunction ("Texas")
| summarize count()
```

Poniższy przykład usuwa funkcja, która została utworzona w pierwszym kroku.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Kolejne kroki

[Dokumentacja języka zapytań Kusto](https://aka.ms/kustolangref)
