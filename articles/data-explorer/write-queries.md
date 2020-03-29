---
title: Pisanie zapytań dla Eksploratora danych platformy Azure
description: W tym instrukcje dowiesz się, jak wykonywać podstawowe i bardziej zaawansowane zapytania dla Usługi Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 80d3eaaf7e588766d62f5e5885d75e61c590970e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68881193"
---
# <a name="write-queries-for-azure-data-explorer"></a>Pisanie zapytań dla Eksploratora danych platformy Azure

W tym artykule dowiesz się, jak używać języka zapytań w Eksploratorze danych platformy Azure do wykonywania podstawowych zapytań z najbardziej typowymi operatorami. Można również uzyskać ekspozycję na niektóre z bardziej zaawansowanych funkcji języka.

## <a name="prerequisites"></a>Wymagania wstępne

Kwerendy w tym artykule można uruchomić na jeden z dwóch sposobów:

- W Eksploratorze danych platformy Azure *program pomocy klastra,* który mamy skonfigurowany w celu ułatwienia nauki.
    [Zaloguj się do klastra](https://dataexplorer.azure.com/clusters/help/databases/samples) przy przyrządu organizacji konta e-mail, który jest członkiem usługi Azure Active Directory.

- We własnym klastrze, który zawiera stormevents przykładowe dane. Aby uzyskać więcej informacji, zobacz [Szybki start: Tworzenie klastra i bazy danych usługi Azure Data Explorer](create-cluster-database-portal.md) oraz pozyskiwania [przykładowych danych do Eksploratora danych platformy Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>Omówienie języka kwerendy

Język zapytań w Eksploratorze danych platformy Azure jest żądaniem tylko do odczytu do przetwarzania danych i zwracania wyników. Żądanie jest podane w postaci zwykłego tekstu, przy użyciu modelu przepływu danych, który ma na celu ułatwienie odczytu, autora i automatyzacji składni. Kwerenda używa jednostek schematu, które są zorganizowane w hierarchii podobnej do SQL: bazy danych, tabele i kolumny.

Kwerenda składa się z sekwencji instrukcji kwerendy, rozdzielonych średnikiem (`;`), przy czym co najmniej jedna instrukcja jest instrukcją wyrażenia tabelaryczne, która jest instrukcją, która generuje dane rozmieszczone w siatce przypominającej tabelę kolumn i wierszy. Instrukcje wyrażeń tabelarów kwerendy dają wyniki kwerendy.

Składnia instrukcji wyrażenia tabelaryczne zawiera tabelaryczny przepływ danych z jednego operatora kwerendy tabelarycznej do drugiego, począwszy od źródła danych (na przykład tabeli w bazie danych lub operatora, który`|`generuje dane), a następnie przepływając przez zestaw operatorów transformacji danych, które są powiązane ze sobą za pomocą potoku ( ) ogranicznika.

Na przykład następująca kwerenda ma pojedynczą instrukcję, która jest instrukcją wyrażenia tabelaryczne. Instrukcja rozpoczyna się od odwołania `StormEvents` do tabeli o nazwie (bazy danych, które hostują tę tabelę jest niejawna w tym miejscu i część informacji o połączeniu). Dane (wiersze) dla tej tabeli są następnie `StartTime` filtrowane według wartości kolumny, a `State` następnie filtrowane według wartości kolumny. Następnie kwerenda zwraca liczbę wierszy "zachowanych".

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA)**\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

W takim przypadku wynik jest:

|Liczba|
|-----|
|   23|
| |

Aby uzyskać więcej informacji, zobacz [odwołanie do języka kwerendy](https://aka.ms/kustolangref).

## <a name="most-common-operators"></a>Najczęściej operatorzy

Operatory opisane w tej sekcji są blokami konstrukcyjnymi do zrozumienia zapytań w Eksploratorze danych platformy Azure. Większość zapytań, które piszesz będzie zawierać kilka z tych operatorów.

Aby uruchomić kwerendy w klastrze pomocy: wybierz **opcję Kliknij, aby uruchomić kwerendę** nad każdą kwerendą.

Aby uruchomić kwerendy we własnym klastrze:

1. Skopiuj każdą kwerendę do aplikacji kwerendy opartej na sieci Web, a następnie wybierz kwerendę lub umieść kursor w kwerendzie.

1. U góry aplikacji wybierz pozycję **Uruchom**.

### <a name="count"></a>count

[**count**](https://docs.microsoft.com/azure/kusto/query/countoperator): Zwraca liczbę wierszy w tabeli.

Poniższa kwerenda zwraca liczbę wierszy w tabeli StormEvents.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA)**\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>wziąć

[**take**](https://docs.microsoft.com/azure/kusto/query/takeoperator): Zwraca do określonej liczby wierszy danych.

Poniższa kwerenda zwraca pięć wierszy z tabeli StormEvents. *Limit* słów kluczowych jest aliasem do *podjęcia.*

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d)**\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Nie ma żadnej gwarancji, które rekordy są zwracane, chyba że dane źródłowe są sortowane.

### <a name="project"></a>projekt

[**projekt**](https://docs.microsoft.com/azure/kusto/query/projectoperator): Wybiera podzbiór kolumn.

Następująca kwerenda zwraca określony zestaw kolumn.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA)**\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>where

[**gdzie**](https://docs.microsoft.com/azure/kusto/query/whereoperator): Filtruje tabelę do podzbioru wierszy, które spełniają predykatu.

Poniższa kwerenda filtruje `EventType` `State`dane według i .

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>sort

[**sortowanie:**](https://docs.microsoft.com/azure/kusto/query/sortoperator)Sortowanie wierszy tabeli wprowadzania według kolejności według jednej lub więcej kolumn.

Następująca kwerenda sortuje dane `DamageProperty`w kolejności malejącej według .

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> Kolejność operacji jest ważna. Spróbuj `take 5` umieścić `sort by`przed . Czy otrzymujesz różne wyniki?

### <a name="top"></a>top

[**górna**](https://docs.microsoft.com/azure/kusto/query/topoperator). *N*

Poniższa kwerenda zwraca te same wyniki, jak powyżej z jednym operatorem mniej.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>Rozszerzanie

[**extend**](https://docs.microsoft.com/azure/kusto/query/extendoperator): Oblicza kolumny pochodne.

Poniższa kwerenda tworzy nową kolumnę, obliczając wartość w każdym wierszu.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

Wyrażenia mogą zawierać wszystkie operatory zwykłych (+, -, *, /, /, %) i istnieje szereg przydatnych funkcji, które można wywołać.

### <a name="summarize"></a>Podsumować

[**podsumuj**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): Agreguje grupy wierszy.

Następująca kwerenda zwraca liczbę `State`zdarzeń według .

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA)**\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

Operator **podsumuj** grupy operatorów razem wiersze, które mają te same wartości w **klauzuli według,** a następnie używa funkcji agregacji (takich jak **count),** aby połączyć każdą grupę w jeden wiersz. Tak więc w tym przypadku istnieje wiersz dla każdego stanu i kolumna dla liczby wierszy w tym stanie.

Istnieje szereg funkcji agregacji i można użyć kilku z nich w jednym operatorze **podsumowania** do tworzenia kilku kolumn obliczeniowych. Na przykład można uzyskać liczbę burz w każdym stanie i unikatową liczbę burz w każdym stanie, a następnie użyć **góry,** aby uzyskać najbardziej dotkniętych burzami stanów.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d)**\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Wynik operacji **podsumowania** ma:

- Każda kolumna **nazwana przez**

- Kolumna dla każdego obliczonego wyrażenia

- Wiersz dla każdej kombinacji przez wartości

### <a name="render"></a>Renderowania

[**render**](https://docs.microsoft.com/azure/kusto/query/renderoperator): Renderuje wyniki jako dane wyjściowe graficzne.

W poniższej kwerendzie jest wyświetlany wykres kolumnowy.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

W poniższej kwerendzie jest wyświetlany prosty wykres czasu.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA)**\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

Następująca kwerenda zlicza zdarzenia według modulo czasu jeden dzień, binned w godzinach i wyświetla wykres czasu.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

Poniższa kwerenda porównuje wiele serii dziennych na wykresie czasu.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA)**\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> Operator **renderowania** jest funkcją po stronie klienta, a nie częścią aparatu. Jest zintegrowany z językiem dla ułatwienia użytkowania. Aplikacja internetowa obsługuje następujące opcje: barchart, columnchart, piechart, timechart i linechart. 

## <a name="scalar-operators"></a>Operatory skalarne

Ta sekcja obejmuje niektóre z najważniejszych operatorów skalarnych.

### <a name="bin"></a>bin()

[**bin()**](https://docs.microsoft.com/azure/kusto/query/binfunction): Zaokrągla wartości w dół do liczby całkowitej wielokrotności danego rozmiaru pojemnika.

Poniższa kwerenda oblicza liczbę o rozmiarze zasobnika jednego dnia.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>sprawa()

[**case()**](https://docs.microsoft.com/azure/kusto/query/casefunction): Ocenia listę predykatów i zwraca pierwsze wyrażenie wynik, którego predykat jest spełniony, lub wyrażenie final **else.** Za pomocą tego operatora można kategoryzować lub grupować dane:

Następująca kwerenda zwraca `deaths_bucket` nową kolumnę i grupuje zgony według liczby.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d)**\]**

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

### <a name="extract"></a>wyciąg()

[**extract()**](https://docs.microsoft.com/azure/kusto/query/extractfunction): Pobiera dopasowanie dla wyrażenia regularnego z ciągu tekstowego.

Poniższa kwerenda wyodrębnia określone wartości atrybutów z śledzenia.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d)**\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Ta kwerenda używa **let** instrukcji, która wiąże nazwę `MyData`(w tym przypadku) do wyrażenia. Dla pozostałej części zakresu, w którym pojawia się **let** instrukcji (zakres globalny lub w zakresie treści funkcji), nazwa może służyć do odwoływania się do jego wartości powiązanej.

### <a name="parse_json"></a>parse_json()

[**parse_json()**](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): Interpretuje ciąg jako wartość JSON i zwraca wartość jako dynamiczną. Jest lepszy od używania funkcji **extractjson(),** gdy trzeba wyodrębnić więcej niż jeden element złożonego obiektu JSON.

Następująca kwerenda wyodrębnia elementy JSON z tablicy.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d)**\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

Poniższa kwerenda wyodrębnia elementy JSON.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA)**\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

Następująca kwerenda wyodrębnia elementy JSON z dynamicznym typem danych.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA)**\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>temu()

[**ago()**](https://docs.microsoft.com/azure/kusto/query/agofunction): Odejmuje dany czas od bieżącego czasu czasu czasu UTC.

Następująca kwerenda zwraca dane z ostatnich 12 godzin.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA)**\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek()

[**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): Zwraca początek tygodnia zawierającego datę przesunięte o przesunięcie, jeśli jest podana

Następująca kwerenda zwraca początek tygodnia z różnymi przesunięciami.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d)**\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Ta kwerenda używa operatora **zakresu,** który generuje jednokolumnową tabelę wartości. Zobacz także: [**startofday()**](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [**startofyear()**](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)), [**startofmonth()**](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [**endofday()**](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [**endofweek()**](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [**endofmonth()**](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction)i [**endofyear()**](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>między()

[**between()**](https://docs.microsoft.com/azure/kusto/query/betweenoperator): Dopasowuje dane wejściowe znajdujące się wewnątrz zakresu włącznie.

Poniższa kwerenda filtruje dane według danego zakresu dat.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

Poniższa kwerenda filtruje dane według danego zakresu dat, z`3d`niewielką zmiennością trzech dni ( ) od daty rozpoczęcia.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Operatorzy tabelaryczne

Kusto ma wielu operatorów tabelaricznych, z których niektóre są omówione w innych sekcjach tego artykułu. Tutaj skupimy się na **przeanalizowaniu**. 

### <a name="parse"></a>parse

[**analizować**](https://docs.microsoft.com/azure/kusto/query/parseoperator): Oblicza wyrażenie ciągu i analizuje jego wartość w jednej lub kilku kolumnach obliczeniowych. Istnieją trzy sposoby analiz: proste (domyślne), wyrażenie regularne i zrelaksowany.

Następująca kwerenda analizuje śledzenia i wyodrębnia odpowiednie wartości, przy użyciu domyślnej analizy proste. Wyrażenie (określane jako StringConstant) jest wartością ciągu regularnego i dopasowanie jest ścisłe: kolumny rozszerzone muszą być zgodne z wymaganymi typami.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d)**\]**

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

Następująca kwerenda analizuje śledzenia i wyodrębnia odpowiednie `kind = regex`wartości, za pomocą . StringConstant może być wyrażeniem regularnym.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA)**\]**

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

Następująca kwerenda analizuje śledzenia i wyodrębnia odpowiednie `kind = relaxed`wartości, za pomocą . StringConstant jest wartością ciągu regularnego i dopasowanie jest złagodzone: kolumny rozszerzone mogą częściowo odpowiadać wymaganym typom.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d)**\]**

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

### <a name="make-series"></a>make-series

[**make-series:**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator)agreguje razem grupy wierszy, takie jak [podsumowanie,](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)ale generuje wektor serii (czasowej) na każdą kombinację wartości.

Następująca kwerenda zwraca zestaw szeregów czasowych dla liczby zdarzeń burzy dziennie. Kwerenda obejmuje okres trzech miesięcy dla każdego stanu, wypełniając brakujące pojemniki stałą 0:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Po utworzeniu zestawu serii (czasowej) można zastosować funkcje serii do wykrywania nietypowych kształtów, wzorców sezonowych i wielu innych.

Następująca kwerenda wyodrębnia trzy najważniejsze stany, które miały najwięcej zdarzeń w określonym dniu:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

Aby uzyskać więcej informacji, zapoznaj się z pełną listą [funkcji serii](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions).

## <a name="advanced-aggregations"></a>Agregacje zaawansowane

Omówiliśmy podstawowe agregacje, takie jak **liczba** i **podsumowanie,** wcześniej w tym artykule. W tej sekcji przedstawiono bardziej zaawansowane opcje.

### <a name="top-nested"></a>zagnieżdżone

[**zagnieżdżone na górze**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): daje hierarchiczne najlepsze wyniki, gdzie każdy poziom jest drążenie na podstawie wartości poprzedniego poziomu.

Ten operator jest przydatny w scenariuszach wizualizacji pulpitu nawigacyjnego lub gdy konieczne jest udzielenie odpowiedzi na pytanie w następujący sposób: "Znajdź wartości top-N K1 (przy użyciu niektórych agregacji); dla każdego z nich, znaleźć jakie są najwyższe wartości M K2 (przy użyciu innej agregacji); ..."

Następująca kwerenda zwraca hierarchiczną tabelę z `State` `Sources`najwyższym poziomem, a następnie .

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>wtyczka pivot()

[**wtyczka pivot():**](https://docs.microsoft.com/azure/kusto/query/pivotplugin)Obraca tabelę, zamieniając unikatowe wartości z jednej kolumny w tabeli wejściowej w wiele kolumn w tabeli wyjściowej. Operator wykonuje agregacje, gdzie są one wymagane dla wszystkich pozostałych wartości kolumn w danych wyjściowych końcowych.

Poniższa kwerenda stosuje filtr i przestawia wiersze na kolumny.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>dcount()

[**dcount()**](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): Zwraca szacunkową liczbę odrębnych wartości wyrażenia w grupie. Użyj [**count(),**](https://docs.microsoft.com/azure/kusto/query/countoperator) aby policzyć wszystkie wartości.

Następujące zapytania zlicza `State`się przez `Source` .

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d)**\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): Zwraca szacunkową liczbę odrębnych wartości wyrażenia dla wierszy, dla których predykat jest oceniany jako true.

Poniższa kwerenda zlicza `Source` `DamageProperty < 5000`różne wartości gdzie .

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d)**\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcount_hll"></a>dcount_hll()

[**dcount_hll()**](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): Oblicza **wynik dcount** z hyperloglogu (generowany przez [**hll**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) lub [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction).

Następująca kwerenda używa algorytmu HLL do generowania liczby.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA)**\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="arg_max"></a>arg_max()

[**arg_max()**](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): Znajduje wiersz w grupie, który maksymalizuje wyrażenie i zwraca wartość innego wyrażenia (lub * do zwrócenia całego wiersza).

Następująca kwerenda zwraca czas ostatniego raportu powodziowego w każdym stanie.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()**](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): Zwraca dynamiczną tablicę (JSON) zestawu odrębnych wartości, które wyrażenie przyjmuje w grupie.

Poniższa kwerenda zwraca wszystkie czasy, kiedy powódź została zgłoszona przez każdy stan i tworzy tablicę z zestawu różnych wartości.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-rozwiń

[**mv-rozwiń**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): Rozwija kolekcje o wielu wartościach z kolumny wpisanej dynamicznie, tak aby każda wartość w kolekcji pobierała osobny wiersz. Wszystkie inne kolumny w rozwiniętym wierszu są duplikowane. Jest to przeciwieństwo makelist.

Poniższa kwerenda generuje przykładowe dane, tworząc zestaw, a następnie używając go do wykazania możliwości **rozszerzenia mv.**

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA)**\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentyle()

[**percentyle()**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): Zwraca szacunkowy dla określonego [**percentyla najbliższej rangi**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) populacji zdefiniowanej przez wyrażenie. Dokładność zależy od gęstości zaludnienia w regionie percentyla. Może być używany tylko w kontekście agregacji wewnątrz [**podsumować**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator).

Poniższa kwerenda oblicza percentyle na czas trwania burzy.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Poniższa kwerenda oblicza percentyle dla czasu trwania burzy według stanu i`5m`normalizuje dane za pomocą pięciominutowych pojemników ( ).

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Zestaw danych krzyżowych

Ta sekcja zawiera elementy, które umożliwiają tworzenie bardziej złożonych zapytań, dołączanie danych między tabelami i zapytania między bazami danych i klastrami.

### <a name="let"></a>Niech

[**niech**](https://docs.microsoft.com/azure/kusto/query/letstatement): Poprawia modułowość i ponowne użycie. **Let** Instrukcja umożliwia przerwanie wyrażenia potencjalnie złożone na wiele części, z których każdy jest powiązany z nazwą, i skomponować te części razem. **Instrukcja let** może być również używana do tworzenia funkcji i widoków zdefiniowanych przez użytkownika (wyrażenia nad tabelami, których wyniki wyglądają jak nowa tabela). Wyrażenia powiązane **instrukcją let** mogą być typu skalarnego, typu tabelarycznego lub funkcji zdefiniowanej przez użytkownika (lambdas).

Poniższy przykład tworzy zmienną typu tabelaryczne i używa go w kolejnym wyrażeniu.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d)**\]**

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

[**sprzężenie**](https://docs.microsoft.com/azure/kusto/query/joinoperator): Scal wiersze dwóch tabel, tworząc nową tabelę, dopasowując wartości określonych kolumn z każdej tabeli. Kusto obsługuje pełną gamę typów sprzężenia: **fullouter**, **wewnętrzny**, **innerunique**, **leftanti**, **leftantisemi**, **leftouter , leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter**, **rightsemi**.

Poniższy przykład łączy dwie tabele z sprzężenia wewnętrznego.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==)**\]**

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
> Użyj **gdzie** i **operatorów projektu,** aby zmniejszyć liczbę wierszy i kolumn w tabelach wejściowych, przed sprzężenia. Jeśli jedna tabela jest zawsze mniejsza od drugiej, użyj jej jako lewej (potokowej) strony sprzężenia. Kolumny dopasowania sprzężenia muszą mieć taką samą nazwę. Użyj operatora **projektu,** jeśli to konieczne, aby zmienić nazwę kolumny w jednej z tabel.

### <a name="serialize"></a>Serializacji

[**serializacja**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): Serializuje zestaw wierszy, dzięki czemu można używać funkcji wymagających danych szeregowych, takich jak **row_number()**.

Następująca kwerenda powiedzie się, ponieważ dane są serializowane.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

Zestaw wierszy jest również uważany za serializowany, jeśli jest wynikiem: **sort,** **top**, lub **operatorów zakresu,** opcjonalnie po **projekcie,** **project-away**, **extend**, **gdzie**, **przeanalizować**, **mv-expand**, lub **wziąć** operatorów.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA)**\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Kwerendy między bazami danych i między klastrami

[Kwerendy między bazami danych i klastrami krzyżowymi:](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries)Można wysyłać `database("MyDatabase").MyTable`kwerendy do bazy danych w tym samym klastrze, odsyłając ją jako . Można zbadać bazę danych w klastrze zdalnym, odwołując się do niej jako `cluster("MyCluster").database("MyDatabase").MyTable`.

Następująca kwerenda jest wywoływana z `MyCluster` jednego klastra i wysyła zapytania do danych z klastra. Aby uruchomić tę kwerendę, użyj własnej nazwy klastra i nazwy bazy danych.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Analiza użytkowników

Ta sekcja zawiera elementy i kwerendy, które pokazują, jak łatwo jest przeprowadzić analizę zachowań użytkowników w Kusto.

### <a name="activity_counts_metrics-plugin"></a>wtyczka activity_counts_metrics

[**activity_counts_metrics wtyczka**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): Oblicza przydatne metryki aktywności (wartości całkowitej liczby, różne wartości liczby, odrębną liczbę nowych wartości i zagregowane liczby odrębnych). Metryki są obliczane dla każdego przedziału czasu, a następnie są porównywane i agregowane do i z wszystkich poprzednich okien czasu.

Poniższa kwerenda analizuje przyjęcie użytkownika przez obliczanie dziennej liczby działań.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d)**\]**

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

### <a name="activity_engagement-plugin"></a>Wtyczka activity_engagement

[**wtyczka activity_engagement:**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin)Oblicza współczynnik zaangażowania aktywności na podstawie kolumny Identyfikatora w oknie osi czasu przesuwnego. **activity_engagement wtyczka** może być używana do obliczania DAU, WAU i MAU (dziennych, tygodniowych i miesięcznych aktywnych użytkowników).

Poniższa kwerenda zwraca stosunek całkowitej różnych użytkowników przy użyciu aplikacji codziennie w porównaniu do całkowitej różnych użytkowników korzystających z aplikacji co tydzień, w ruchomym oknie siedem dni.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d)**\]**

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
> Podczas obliczania DAU/MAU zmień dane końcowe i ruchomy okres okna (OuterActivityWindow).

### <a name="activity_metrics-plugin"></a>wtyczka activity_metrics

[**activity_metrics wtyczka:**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin)Oblicza użyteczne metryki aktywności (różne wartości liczby, odrębną liczbę nowych wartości, wskaźnik retencji i współczynnik zmian) na podstawie bieżącego okna okresu w porównaniu z poprzednim oknem okresu.

Poniższa kwerenda oblicza współczynnik zmian i przechowywania dla danego zestawu danych.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d)**\]**

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

### <a name="new_activity_metrics-plugin"></a>wtyczka new_activity_metrics

[**wtyczka new_activity_metrics:**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin)Oblicza przydatne metryki aktywności (różne wartości liczby, odrębną liczbę nowych wartości, wskaźnik retencji i współczynnik zmian) dla kohorty nowych użytkowników. Koncepcja tej wtyczki jest podobna do [**wtyczki activity_metrics,**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin)ale skupia się na nowych użytkownikach.

Poniższa kwerenda oblicza wskaźnik retencji i zmian z okresem tydzień za tygodniem dla kohorty nowych użytkowników (użytkowników, którzy przybyli w pierwszym tygodniu).

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d)**\]**

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

### <a name="session_count-plugin"></a>Wtyczka session_count

[**session_count wtyczka**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): Oblicza liczbę sesji na podstawie kolumny identyfikatora na osi czasu.

Następująca kwerenda zwraca liczbę sesji. Sesja jest uważana za aktywną, jeśli identyfikator użytkownika pojawia się co najmniej raz w przedziale czasowym 100-czasowym, podczas gdy okno spojrzenia sesji to 41-czasowe szczeliny.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d)**\]**

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

### <a name="funnel_sequence-plugin"></a>Wtyczka funnel_sequence

[**funnel_sequence wtyczka**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): Oblicza odrębną liczbę użytkowników, którzy podjęli sekwencję stanów; pokazuje rozkład poprzednich i następnych stanów, które doprowadziły do lub były po sekwencji.

Poniższe zapytanie pokazuje, co dzieje się przed i po wszystkich zdarzeń Tornado w 2007 roku.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA)**\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnel_sequence_completion-plugin"></a>wtyczka funnel_sequence_completion

[**funnel_sequence_completion wtyczka**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): Oblicza lejek zakończonych kroków sekwencji w różnych okresach.

Następująca kwerenda sprawdza lejek `Hail -> Tornado -> Thunderstorm -> Wind` zakończenia sekwencji: w "ogólnej" godzinie jednej`[1h, 4h, 1d]`godziny, czterech godzinach i jeden dzień ( ).

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA)**\]**

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

W tej sekcji opisano [**funkcje:**](https://docs.microsoft.com/azure/kusto/query/functions)kwerendy wielokrotnegoużynowania, które są przechowywane na serwerze. Funkcje mogą być wywoływane przez kwerendy i inne funkcje (funkcje cykliczne nie są obsługiwane).

> [!NOTE]
> Nie można tworzyć funkcji w klastrze pomocy, który jest tylko do odczytu. Użyj własnego klastra testowego dla tej części.

Poniższy przykład tworzy funkcję, która`MyState`przyjmuje nazwę stanu ( ) jako argument.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

W poniższym przykładzie wywołuje funkcję, która pobiera dane dla stanu Teksas.

```Kusto
MyFunction ("Texas")
| summarize count()
```

Poniższy przykład usuwa funkcję, która została utworzona w pierwszym kroku.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>Następne kroki

[Odwołanie do języka zapytania Kusto](https://aka.ms/kustolangref)
