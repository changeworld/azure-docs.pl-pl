---
title: Analizowanie danych szeregów czasowych przy użyciu usługi Azure Eksplorator danych
description: Dowiedz się, jak analizować dane szeregów czasowych w chmurze przy użyciu usługi Azure Eksplorator danych.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 3873b25394f91ce1c1601c348de2098198ba7fdd
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765487"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Analiza szeregów czasowych w usłudze Azure Eksplorator danych

Usługa Azure Eksplorator danych (ADX) wykonuje gromadzenie danych telemetrycznych z usług Cloud Services i IoT. Te dane można analizować pod kątem różnych szczegółowych informacji, takich jak monitorowanie kondycji usług, fizyczne procesy produkcyjne i trendy użycia. Analiza jest wykonywana w szeregach czasowych wybranych metryk, aby znaleźć odchylenia wzorca w porównaniu z jego typowym wzorcem linii bazowej.
ADX zawiera natywną obsługę tworzenia, manipulowania i analizy wielu szeregów czasowych. W tym temacie dowiesz się, jak ADX jest używany do tworzenia i analizowania **tysięcy szeregów czasowych w ciągu kilku sekund**, co pozwala na monitorowanie rozwiązań i przepływów pracy niemal w czasie rzeczywistym.

## <a name="time-series-creation"></a>Tworzenie szeregów czasowych

W tej sekcji utworzymy duży zestaw regularnych cyklów czasowych, po prostu i intuicyjnie przy użyciu operatora `make-series` i wypełnianie brakujących wartości zgodnie z wymaganiami.
Pierwszym krokiem w analizie szeregów czasowych jest partycjonowanie i przekształcenie oryginalnej tabeli telemetrii na zestaw szeregów czasowych. Tabela zwykle zawiera kolumnę sygnatur czasowych, wymiary kontekstowe i metryki opcjonalne. Wymiary są używane do partycjonowania danych. Celem jest utworzenie tysięcy szeregów czasowych na partycję w regularnych odstępach czasu.

Tabela wejściowa *demo_make_series1* zawiera rekordy 600K dowolnego ruchu usługi sieci Web. Użyj poniższego polecenia do przykładu 10 rekordów:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA) **\]**

```kusto
demo_make_series1 | take 10 
```

Poniższa tabela zawiera kolumnę sygnatur czasowych, trzy kolumny z wymiarami kontekstowymi i brak metryk:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Znacznik czasu | BrowserVer | OsVer | Kraj/region |
|   | 2016-08-25 09:35.4020000 | Chrome 51,0 | Windows 7 | Zjednoczone Królestwo |
|   | 2016-08-25 09:41.1120000 | Chrome 52,0 | Windows 10 |   |
|   | 2016-08-25 09:46.2300000 | Chrome 52,0 | Windows 7 | Zjednoczone Królestwo |
|   | 2016-08-25 09:46.5100000 | Chrome 52,0 | Windows 10 | Zjednoczone Królestwo |
|   | 2016-08-25 09:46.5570000 | Chrome 52,0 | Windows 10 | Republika Litwy |
|   | 2016-08-25 09:47.0470000 | Chrome 52,0 | Windows 8.1 | Indie |
|   | 2016-08-25 09:51.3600000 | Chrome 52,0 | Windows 10 | Zjednoczone Królestwo |
|   | 2016-08-25 09:51.6930000 | Chrome 52,0 | Windows 7 | Holandia |
|   | 2016-08-25 09:56.4240000 | Chrome 52,0 | Windows 10 | Zjednoczone Królestwo |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52,0 | Windows 10 | Indie |

Ze względu na to, że nie istnieją żadne metryki, można skompilować zestaw szeregów czasowych reprezentujący samą liczbę ruchu, partycjonowany przez system operacyjny przy użyciu następującej kwerendy:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Użyj operatora [`make-series`](/azure/kusto/query/make-seriesoperator) , aby utworzyć zestaw trzech szeregów czasowych, gdzie:
    - `num=count()`: szereg czasowy ruchu
    - `range(min_t, max_t, 1h)`: serie czasowe są tworzone w 1-godzinnym zasobniku w przedziale czasu (najstarsze i najnowsze sygnatury czasowe rekordów tabeli)
    - `default=0`: Określ metodę Fill dla brakujących pojemników, aby utworzyć regularne serie czasowe. Alternatywnie możesz użyć [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction), [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction), [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) i [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) do wprowadzania zmian
    - `byOsVer`: Podziel na partycje według systemu operacyjnego
- Rzeczywista struktura danych szeregów czasowych to tablica liczbowa zagregowanej wartości dla każdego przedziału czasu. Używamy `render timechart` do wizualizacji.

W powyższej tabeli mamy trzy partycje. Możemy utworzyć oddzielną serię czasową: system Windows 10 (czerwony), 7 (niebieski) i 8,1 (zielony) dla każdej wersji systemu operacyjnego widziany na grafie:

![Partycja szeregów czasowych](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Funkcje analizy szeregów czasowych

W tej sekcji wykonamy typowe funkcje przetwarzania serii.
Po utworzeniu zestawu szeregów czasowych program ADX obsługuje rosnącą listę funkcji do przetworzenia i analizowania tych elementów, które można znaleźć w [dokumentacji szeregów czasowych](/azure/kusto/query/machine-learning-and-tsa). Opiszemy kilka funkcji reprezentatywnych dla przetwarzania i analizowania szeregów czasowych.

### <a name="filtering"></a>Filtrowanie

Filtrowanie jest powszechną metodą przetwarzania sygnałów i przydatne w przypadku zadań przetwarzania szeregów czasowych (na przykład wygładzanie sygnału szumu, wykrywania zmian).
- Istnieją dwie funkcje filtrowania ogólnego:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): stosowanie filtru FIR. Używane do prostej obliczeń średniej przeniesień i rozróżnienia szeregów czasowych na potrzeby wykrywania zmian.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): stosowanie filtru IIR. Używany do wygładzania wykładniczego i sumy skumulowanej.
- `Extend` zestawu czasowego, dodając nową średnią liczbę pojemników o rozmiarze 5 (o nazwie *ma_num*) do zapytania:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Filtrowanie szeregów czasowych](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Analiza regresji

ADX obsługuje wielosegmentową analizę regresji liniowej w celu oszacowania trendu szeregów czasowych.
- Użyj [series_fit_line ()](/azure/kusto/query/series-fit-linefunction) , aby dopasować najlepszą linię do szeregów czasowych na potrzeby ogólnego wykrywania trendów.
- Użyj [series_fit_2lines ()](/azure/kusto/query/series-fit-2linesfunction) w celu wykrycia zmian trendów względem linii bazowej, które są przydatne w scenariuszach monitorowania.

Przykład funkcji `series_fit_line()` i `series_fit_2lines()` w zapytaniu szeregów czasowych:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=) **\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Regresja szeregów czasowych](media/time-series-analysis/time-series-regression.png)

- Niebieska: Oryginalna seria czasowa
- Zielony: zamontowany wiersz
- Czerwony: dwa dopasowane linie

> [!NOTE]
> Funkcja dokładnie wykryła punkt skoku (zmiana poziomu).

### <a name="seasonality-detection"></a>Wykrywanie sezonowości

Wiele metryk obserwuje wzorce sezonowe (okresowe). Ruch użytkowników usług w chmurze zwykle zawiera codzienne i cotygodniowe wzorce, które są najwyższe w połowie dnia roboczego i najtańsze i w weekendy. Miara czujników IoT w regularnych odstępach czasu. Pomiary fizyczne, takie jak temperatura, ciśnienie lub wilgotność, mogą również przedstawiać zachowanie sezonowe.

W poniższym przykładzie zastosowano wykrywanie sezonowości w jednym miesiącu ruchu usługi sieci Web (w przypadku zasobników 2-godzinnych):

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA) **\]**

```kusto
demo_series3
| render timechart 
```

![Sezonowości szeregów czasowych](media/time-series-analysis/time-series-seasonality.png)

- Użyj [series_periods_detect ()](/azure/kusto/query/series-periods-detectfunction) , aby automatycznie wykrywać okresy w szeregach czasowych. 
- Użyj [series_periods_validate ()](/azure/kusto/query/series-periods-validatefunction) , jeśli wiemy, że Metryka powinna mieć określone odrębne okresy i chcemy sprawdzić, czy istnieją.

> [!NOTE]
> Jest to anomalia w przypadku nieistnienia określonych odrębnych okresów

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA) **\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | okresu | punkt | dni |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

Funkcja wykrywa codzienne i cotygodniowe sezonowości. Dzienne wyniki są mniejsze niż cotygodniowe, ponieważ dni weekendowe różnią się od dnia tygodnia.

### <a name="element-wise-functions"></a>Funkcje związane z elementami

Operacje arytmetyczne i logiczne można wykonywać w szeregach czasowych. Korzystając z [series_subtract ()](/azure/kusto/query/series-subtractfunction) , możemy obliczyć resztę szeregów czasowych, czyli różnice między pierwotną metryką nieprzetworzoną i gładką i wyszukać anomalie w sygnale resztkowym:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![Operacje szeregów czasowych](media/time-series-analysis/time-series-operations.png)

- Niebieska: Oryginalna seria czasowa
- Czerwona: wygładzone serie czasowe
- Zielony: pozostałe serie czasowe

## <a name="time-series-workflow-at-scale"></a>Przepływ pracy szeregów czasowych w skali

W poniższym przykładzie pokazano, jak te funkcje mogą być uruchamiane na dużą skalę w tysiącach czasowych w ciągu kilku sekund na potrzeby wykrywania anomalii. Aby wyświetlić kilka przykładowych rekordów telemetrii metryki liczby odczytów usługi bazy danych w ciągu czterech dni, uruchom następujące zapytanie:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==) **\]**

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | ZNACZNIK czasu | Loc | anonOp | BAZĄ | Odczyt DataReady |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

I proste statystyki:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA) **\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | numerowan | min\_t | maks\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Tworzenie szeregów czasowych w 1-godzinnym zasobniku metryki odczytu (łącznie cztery dni * 24 godziny = 96 punktów), wyniki w normalnych wahaniach wzorców:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Szeregi czasowe na dużą skalę](media/time-series-analysis/time-series-at-scale.png)

Powyższe zachowanie jest mylące, ponieważ pojedyncze normalne serie czasowe są agregowane z tysięcy różnych wystąpień, które mogą mieć nietypowe wzorce. W związku z tym tworzymy serie czasowe dla każdego wystąpienia. Wystąpienie jest zdefiniowane przez Loc (Location), anonOp (Operation) i DB (określony komputer).

Ile szeregów czasowych można utworzyć?

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=) **\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Liczba |
|   | 18339 |

Teraz utworzymy zestaw 18339 szeregów czasowych metryki liczby odczytów. Dodajemy klauzulę `by` do instrukcji CREATE-Series, Zastosuj regresję liniową i wybierz dwie górne serie czasu, które miały najbardziej znaczący trend spadkowy:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Seria czasowa — pierwsze dwa](media/time-series-analysis/time-series-top-2.png)

Wyświetl wystąpienia:

**\[** [**kliknij, aby uruchomić zapytanie**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Loc | Op | BAZĄ | widm |
|   | Loc 15 | 37 | 1151 | -102743,910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

W czasie krótszym niż dwie minuty ADX przeanalizowane blisko do 20 000 szeregów czasowych i wykryto dwie nietypowe serie czasowe, w których liczba odczytanych nagle została porzucona.

Te zaawansowane możliwości połączone z ADX Fast Performance zapewniają unikatowe i zaawansowane rozwiązanie do analizy szeregów czasowych.

## <a name="next-steps"></a>Następne kroki

* Informacje o [wykrywaniu anomalii szeregów czasowych i prognozowania](/azure/data-explorer/anomaly-detection) na platformie Azure Eksplorator danych.
* Dowiedz się więcej o [możliwościach uczenia maszynowego](/azure/data-explorer/machine-learning-clustering) w usłudze Azure Eksplorator danych.