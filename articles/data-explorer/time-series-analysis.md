---
title: Analizowanie danych szeregów czasowych przy użyciu Eksploratora danych platformy Azure
description: Dowiedz się, jak analizować dane szeregów czasowych w chmurze przy użyciu Eksploratora danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 3873b25394f91ce1c1601c348de2098198ba7fdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765487"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Analiza szeregów czasowych w Eksploratorze danych platformy Azure

Usługa Azure Data Explorer (ADX) wykonuje w toku zbieranie danych telemetrycznych z usług w chmurze lub urządzeń IoT. Te dane mogą być analizowane pod kątem różnych szczegółowych informacji, takich jak monitorowanie kondycji usługi, fizycznych procesów produkcyjnych i trendów użycia. Analiza jest wykonywana w szeregach czasowych wybranych metryk, aby znaleźć odchylenie we wzorcu w porównaniu do jego typowego wzorca linii bazowej.
ADX zawiera natywną obsługę tworzenia, manipulowania i analizy wielu szeregów czasowych. W tym temacie dowiedz się, jak adx jest używany do tworzenia i analizowania **tysięcy szeregów czasowych w ciągu kilku sekund,** umożliwiając niemal w czasie rzeczywistym rozwiązania monitorowania i przepływy pracy.

## <a name="time-series-creation"></a>Tworzenie szeregów czasowych

W tej sekcji utworzymy duży zestaw regularnych szeregów czasowych `make-series` w prosty i intuicyjnie za pomocą operatora i wypełnij brakujące wartości w razie potrzeby.
Pierwszym krokiem w analizie szeregów czasowych jest partycjonowanie i przekształcenie oryginalnej tabeli telemetrii na zestaw szeregów czasowych. Tabela zazwyczaj zawiera kolumnę sygnatury czasowej, wymiary kontekstowe i opcjonalne metryki. Wymiary są używane do partycjonowania danych. Celem jest utworzenie tysięcy szeregów czasowych na partycję w regularnych odstępach czasu.

Tabela wprowadzania *demo_make_series1* zawiera 600K rekordów dowolnego ruchu usługi sieci web. Użyj poniższego polecenia, aby wypróbować 10 rekordów:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA)**\]**

```kusto
demo_make_series1 | take 10 
```

Wynikowa tabela zawiera kolumnę sygnatury czasowej, trzy kolumny wymiarów kontekstowych i brak metryk:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Sygnatury czasowej | PrzeglądarkaVer | OsVer (osVer) | Kraj/region |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | Wielka Brytania |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | Wielka Brytania |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | Wielka Brytania |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | Republika Litewska |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | Indie |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | Wielka Brytania |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | Holandia |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | Wielka Brytania |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | Indie |

Ponieważ nie ma żadnych metryk, możemy utworzyć tylko zestaw szeregów czasowych reprezentujących samą liczbę ruchu, podzielony na partycje przez system operacyjny przy użyciu następującej kwerendy:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=)**\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Operator [`make-series`](/azure/kusto/query/make-seriesoperator) służy do tworzenia zestawu trzech szeregów czasowych, gdzie:
    - `num=count()`: szeregi czasowe ruchu
    - `range(min_t, max_t, 1h)`: szeregi czasowe są tworzone w 1-godzinnych pojemnikach w zakresie czasu (najstarsze i najnowsze znaczniki czasu rekordów tabeli)
    - `default=0`: określić metodę wypełniania brakujących pojemników, aby utworzyć regularne serie czasowe. Alternatywnie [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction)użyć [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction) [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) , [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) i dla zmian
    - `byOsVer`: partycja przez system operacyjny
- Rzeczywista struktura danych szeregów czasowych jest tablicą liczbową zagregowanych wartości dla każdego pojemnika czasu. Używamy `render timechart` do wizualizacji.

W powyższej tabeli mamy trzy partycje. Możemy utworzyć oddzielny szereg czasu: Windows 10 (czerwony), 7 (niebieski) i 8.1 (zielony) dla każdej wersji systemu operacyjnego, jak widać na wykresie:

![Partycja szeregów czasowych](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Funkcje analizy szeregów czasowych

W tej sekcji wykonamy typowe funkcje przetwarzania serii.
Po utworzeniu zestawu szeregów czasowych ADX obsługuje rosnącą listę funkcji do ich przetwarzania i analizowania, które można znaleźć w [dokumentacji szeregów czasowych.](/azure/kusto/query/machine-learning-and-tsa) Opiszemy kilka funkcji reprezentacyjnych do przetwarzania i analizowania szeregów czasowych.

### <a name="filtering"></a>Filtrowanie

Filtrowanie jest powszechną praktyką w przetwarzaniu sygnału i przydatne do zadań przetwarzania szeregów czasowych (na przykład wygładzić hałaśliwy sygnał, wykrywanie zmian).
- Istnieją dwie ogólne funkcje filtrowania:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): Zastosowanie filtra FIR. Służy do prostego obliczania średniej ruchomej i różnicowania szeregów czasowych do wykrywania zmian.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): Zastosowanie filtra IIR. Służy do wygładzania wykładniczego i sumy skumulowanej.
- `Extend`zestaw szeregów czasowych przez dodanie do kwerendy nowej średniej ruchomej serii pojemników o rozmiarze 5 (o nazwie *ma_num):*

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=)**\]**

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

ADX obsługuje segmentową analizę regresji liniowej w celu oszacowania trendu szeregów czasowych.
- Użyj [series_fit_line(),](/azure/kusto/query/series-fit-linefunction) aby dopasować najlepszą linię do szeregów czasowych do ogólnego wykrywania trendów.
- Użyj [series_fit_2lines(),](/azure/kusto/query/series-fit-2linesfunction) aby wykryć zmiany trendu w stosunku do linii bazowej, które są przydatne w scenariuszach monitorowania.

Przykład `series_fit_line()` i `series_fit_2lines()` funkcje w kwerendzie szeregów czasowych:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=)**\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Regresja szeregów czasowych](media/time-series-analysis/time-series-regression.png)

- Niebieski: oryginalna seria czasowa
- Zielony: linia fitt
- Czerwony: dwie linie wyposażone

> [!NOTE]
> Funkcja dokładnie wykryła punkt skoku (zmiana poziomu).

### <a name="seasonality-detection"></a>Wykrywanie sezonowości

Wiele wskaźników jest zgodnych z sezonowymi (okresowymi) wzorcami. Ruch użytkowników usług w chmurze zwykle zawiera dzienne i tygodniowe wzorce, które są najwyższe w środku dnia roboczego i najniższe w nocy i w weekend. Czujniki IoT mierzą się w odstępach czasu. Pomiary fizyczne, takie jak temperatura, ciśnienie lub wilgotność, mogą również wykazywać zachowanie sezonowe.

Poniższy przykład stosuje wykrywanie sezonowości w ruchu miesięcznym usługi sieci web (pojemniki 2-godzinne):

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA)**\]**

```kusto
demo_series3
| render timechart 
```

![Sezonowość szeregów czasowych](media/time-series-analysis/time-series-seasonality.png)

- Użyj [series_periods_detect(),](/azure/kusto/query/series-periods-detectfunction) aby automatycznie wykryć okresy w szeregach czasowych. 
- Użyj [series_periods_validate(),](/azure/kusto/query/series-periods-validatefunction) jeśli wiemy, że dane powinny mieć określone różne okresy i chcemy sprawdzić, czy istnieją.

> [!NOTE]
> Jest to anomalia, jeśli nie istnieją określone odrębne okresy

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA)**\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | Okresy | Wyniki | Dni |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

Funkcja wykrywa sezonowość dzienną i tygodniową. Dzienne wyniki są mniejsze niż w tygodniu, ponieważ dni weekendowe różnią się od dni powszednich.

### <a name="element-wise-functions"></a>Funkcje oparte na elementach

Operacje arytmetyczne i logiczne można wykonać w szeregach czasowych. Za pomocą [series_subtract()](/azure/kusto/query/series-subtractfunction) możemy obliczyć resztkowe szeregi czasowe, czyli różnicę między oryginalną surową metryką a wygładzone, i szukać anomalii w sygnale resztkowym:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=)**\]**

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

- Niebieski: oryginalna seria czasowa
- Czerwony: wygładzone szeregi czasowe
- Zielony: resztkowe szeregi czasowe

## <a name="time-series-workflow-at-scale"></a>Przepływ pracy szeregów czasowych na dużą skalę

Poniższy przykład pokazuje, jak te funkcje mogą działać na dużą skalę na tysiące szeregów czasowych w sekundach do wykrywania anomalii. Aby wyświetlić kilka przykładowych rekordów telemetrycznych metryki liczby odczytów usługi DB w ciągu czterech dni, uruchom następującą kwerendę:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==)**\]**

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | Sygnatury czasowej | Loc | anonOp ( anonOp ) | DB | Dataread |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 (łat. | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 (łat. | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 (łat. | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 (łat. | 371921734563783410 | 255 | 0 |

I proste statystyki:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA)**\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | num | min\_t | maks.\_ |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Tworzenie szeregów czasowych w 1-godzinnych pojemnikach metryki odczytu (łącznie cztery dni * 24 godziny = 96 punktów), powoduje normalne wahania wzoru:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Szeregi czasowe na dużą skalę](media/time-series-analysis/time-series-at-scale.png)

Powyższe zachowanie jest mylące, ponieważ pojedyncze normalne serie czasowe są agregowane z tysięcy różnych wystąpień, które mogą mieć nieprawidłowe wzorce. W związku z tym tworzymy szeregi czasowe na wystąpienie. Wystąpienie jest definiowane przez Loc (lokalizacja), anonOp (operacja) i DB (określony komputer).

Ile szeregów czasowych możemy utworzyć?

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=)**\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Liczba |
|   | 18339 |

Teraz utworzymy zestaw 18339 szeregów czasowych metryki liczby odczytów. Dodamy `by` klauzulę do instrukcji make-series, stosujemy regresję liniową i wybieramy dwie pierwsze serie czasowe, które miały najbardziej znaczący trend spadkowy:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Seria czasowa dwie najlepsze](media/time-series-analysis/time-series-top-2.png)

Wyświetl wystąpienia:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA)**\]**

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
|   | Loc | Operator | DB | Nachylenie |
|   | Loc 15 (łot 15) | 37 | 1151 | -102743.910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

W ciągu niecałych dwóch minut adx przeanalizował blisko 20 000 szeregów czasowych i wykrył dwa nietypowe szeregi czasowe, w których liczba odczytów nagle spadła.

Te zaawansowane możliwości w połączeniu z wysoką wydajnością ADX stanowią unikalne i wydajne rozwiązanie do analizy szeregów czasowych.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [wykrywaniu anomalii szeregów czasowych i prognozowaniu](/azure/data-explorer/anomaly-detection) w Eksploratorze danych platformy Azure.
* Dowiedz się więcej o [funkcjach uczenia maszynowego](/azure/data-explorer/machine-learning-clustering) w Eksploratorze danych platformy Azure.