---
title: Analizowanie danych szeregów czasowych za pomocą Eksploratora danych platformy Azure
description: Dowiedz się, jak i analizowanie danych szeregów czasowych w chmurze za pomocą Eksploratora danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 496c033e3df096cdada2b3facba3e73092ffd755
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051499"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Analiza serii czasu w Eksploratorze danych platformy Azure

Usługa Azure Data Explorer (ADX) wykonuje kolekcję bieżące dane telemetryczne z usług w chmurze lub urządzeń IoT. Te dane mogą być analizowane różnych szczegółowych danych, takich jak monitorowania kondycji usługi, procesy produkcji fizycznej i trendów użycia. Analiza odbywa się w szeregu czasowym wybrane metryki, aby znaleźć odchylenie we wzorcu w porównaniu do jego wzór typowej linii bazowej.
ADX zawiera natywną obsługę tworzenia, manipulowanie i analizę wielu szeregach czasowych. W tym temacie, Dowiedz się, jak ADX służy do tworzenia i analizowania **tysiące szeregów czasowych w ciągu kilku sekund**, dzięki czemu prawie w czasie rzeczywistym rozwiązań do monitorowania i przepływów pracy.

## <a name="time-series-creation"></a>Tworzenie serii czasu

W tej sekcji utworzymy szerokiej gamy serii czasu prosty i intuicyjny sposób za pomocą `make-series` operator i wypełnianie brakujące wartości zgodnie z potrzebami.
Pierwszym krokiem podczas analizy serii czasowych jest na partycje i Przekształć oryginalnej tabeli danych telemetrycznych z zestawem szeregów czasowych. Tabela zwykle zawiera kolumnę sygnatur czasowych, kontekstowe wymiarów i opcjonalnie metryki. Wymiary są używane w celu podzielenia danych. Celem jest tworzyć tysiące szeregów czasowych dla każdej partycji w regularnych odstępach czasu.

Tabela wejściowa *demo_make_series1* zawiera 600 tys. rekordów ruchu usług sieci web dowolnego. Użyj poniższego polecenia, aby pobrać próbkę 10 rekordów:

```kusto
demo_make_series1 | take 10 
```

Tabela wynikowa zawiera kolumnę sygnatur czasowych, trzy kolumny kontekstowych wymiarów i braku metryk:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Sygnatura czasowa | BrowserVer | OsVer | Kraj |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | Zjednoczone Królestwo |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | Zjednoczone Królestwo |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | Zjednoczone Królestwo |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | Republika Litwa |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | Indie |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | Zjednoczone Królestwo |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | Holandia |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | Zjednoczone Królestwo |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | Indie |

Ponieważ nie istnieją żadne metryki, możemy ją tworzyć tylko zestaw szeregów czasowych, reprezentujący liczbę ruchu, podzielone na partycje według systemu operacyjnego przy użyciu następującej kwerendy:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Użyj [ `make-series` ](/azure/kusto/query/make-seriesoperator) operatora, aby utworzyć zestaw trzech szeregów czasowych, gdzie:
    - `num=count()`: czas serii ruchu
    - `range(min_t, max_t, 1h)`: szeregu czasowego jest tworzony w pojemnikach 1 godziny w zakresie czasu (najstarszej i najnowszej znacznikami czasu rekordy tabeli)
    - `default=0`: Określ metody fill brak pojemników, utworzyć serii czasu. Można również użyć [ `series_fill_const()` ](/azure/kusto/query/series-fill-constfunction), [ `series_fill_forward()` ](/azure/kusto/query/series-fill-forwardfunction), [ `series_fill_backward()` ](/azure/kusto/query/series-fill-backwardfunction) i [ `series_fill_linear()` ](/azure/kusto/query/series-fill-linearfunction) zmian
    - `byOsVer`: partycję według systemu operacyjnego
- Struktura danych szeregu czasowego rzeczywisty jest tablicy liczbowej zagregowane wartości dla każdego kwantu czasu. Używamy `render timechart` wizualizacji.

W powyższej tabeli mamy trzy partycje. Możemy utworzyć oddzielne szeregów: Windows 10 (czerwony), 7 (niebieski) i 8.1 (zielony) dla każdej wersji systemu operacyjnego, jak pokazano na wykresie:

![Czas serii partycji](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Funkcje analizy serii czasu

W tej sekcji możemy wykonać serię typowych przetwarzania funkcji.
Po utworzeniu zestawu szeregów czasowych ADX obsługuje coraz większej liczby funkcji do przetwarzania i analizowania ich, które znajdują się w [dokumentacja macierzy magazynowych serii czasu](/azure/kusto/query/machine-learning-and-tsa). Opiszemy kilka funkcji reprezentatywny dla przetwarzanie i analizowanie danych szeregów czasowych.

### <a name="filtering"></a>Filtrowanie

Filtrowanie jest powszechną praktyką w sygnał przetwarzania i użyteczna dla szeregów czasowych, zadania przetwarzania (na przykład smooth sygnał generujące dużo alertów, zmienianie wykrycia).
- Istnieją dwa ogólne funkcje filtrowania:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): Stosowanie filtru jako część. Używany do obliczania proste przenoszenie średnią i różnice między szeregu czasowym na potrzeby wykrywania zmian.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): Stosowanie filtru IIR. Używane dla Wygładzanie wykładnicze i zbiorcze sum.
- `Extend` Szeregi czasowe, dodając nową ruchoma średnia serię rozmiar pojemników 5 (o nazwie *ma_num*) do kwerendy:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Filtrowanie serii czasu](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Analizę regresji

Obsługuje ADX segmentowanych analizę regresji liniowej do oszacowania trend szeregów czasowych.
- Użyj [series_fit_line()](/azure/kusto/query/series-fit-linefunction) aby dopasować najlepsze linii szeregów czasowych do wykrywania ogólny trend.
- Użyj [series_fit_2lines()](/azure/kusto/query/series-fit-2linesfunction) do wykrywania zmian trendów, względem punktu odniesienia, które są przydatne w scenariuszach monitorowania.

Przykład `series_fit_line()` i `series_fit_2lines()` funkcji w zapytaniu serii czasu:

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Regresja serii czasu](media/time-series-analysis/time-series-regression.png)

- Niebieski: oryginalna szeregów czasowych
- Zielony: zainstalowane wiersza
- Red: dwa wiersze dopasowanego

> [!NOTE]
> Funkcja dokładnie wykrył punkt skoku (zmiany poziomu).

### <a name="seasonality-detection"></a>Sezonowość wykrywania

Wiele metryk wykonaj sezonowych wzorców (okresowo). Ruch użytkowników usług w chmurze zawiera zazwyczaj dzienne i tygodniowe wzorców, które są najwyższy wokół środkowej dnia roboczego i najniższego w środku nocy i w ciągu weekendu. Pomiar czujniki IoT w regularnych odstępach czasu. Pomiary fizycznych, takich jak temperatury, wykorzystanie lub wilgotność również mogą być wyświetlane sezonowych zachowanie.

Następujący przykład dotyczy sezonowości wykrywania na jeden miesiąc ruch usługi sieci web (pliki binarne 2 godziny):

```kusto
demo_series3
| render timechart 
```

![Sezonowość serii czasu](media/time-series-analysis/time-series-seasonality.png)

- Użyj [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction) próbę automatycznego wykrycia okresy w szeregu czasowym. 
- Użyj [series_periods_validate()](/azure/kusto/query/series-periods-validatefunction) gdy wiemy, czy metryka powinna mieć określone okresy odrębne i chcemy sprawdzić, czy istnieją one.

> [!NOTE]
> Jeśli nie istnieją określone okresy distinct jest anomalii

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mvexpand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | okresy | Wyniki | dni |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

Funkcja wykrywa dzienne i tygodniowe sezonowość. Codziennie ocenia mniejsza niż co tydzień, ponieważ dni weekendowych różnią się od dni tygodnia.

### <a name="element-wise-functions"></a>Funkcje element-Wise

Operacji arytmetycznych i logicznych może odbywać się na szeregu czasowego. Za pomocą [series_subtract()](/azure/kusto/query/series-subtractfunction) możemy obliczania serii pozostałego czasu, który jest, różnica między oryginalnego pierwotne metryk i wygładzone jednego i poszukaj anomalii w pozostałych sygnał:

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

![Czas serii operacji](media/time-series-analysis/time-series-operations.png)

- Niebieski: oryginalna szeregów czasowych
- Czerwony: wygładzone szeregów czasowych
- Zielony: Szeregi czasowe resztkowe

## <a name="time-series-workflow-at-scale"></a>Przepływ pracy z serii czasu w odpowiedniej skali

W poniższym przykładzie pokazano, jak te funkcje można uruchomić na dużą skalę na tysiącach szeregów czasowych w ciągu kilku sekund do wykrywania anomalii. Aby wyświetlić kilka przykładowych danych telemetrycznych rekordów metryki liczba odczytu usługa bazy danych za pośrednictwem cztery dni uruchom następujące zapytanie:

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | SYGNATURA CZASOWA | Lokalizacja | anonOp | DB | Odczyt danych |
|   | 2016-09-11 21:00:00.0000000 | Lokalizacja 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Lokalizacja 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Lokalizacja 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Lokalizacja 9 | 371921734563783410 | 255 | 0 |

I proste statystyki:

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | Liczba | min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Tworzenie szeregów czasowych w pojemnikach 1-godzinnego odczytu metryki (łącznie cztery dni * 24 godziny = 96 punktów), wynikiem fluktuacje wzorca normalnego:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Szeregi czasowe na dużą skalę](media/time-series-analysis/time-series-at-scale.png)

Zachowanie powyżej jest mylący, serii jeden raz normalne są agregowane z tysięcy różnymi wystąpieniami, które mogą mieć nietypowe wzorce. W związku z tym możemy utworzyć szeregów czasowych dla każdego wystąpienia. Wystąpienie jest definiowany przez lokalizacja (lokalizacja), anonOp (operacji) i DB (określonej maszyny).

Ile szeregów czasowych możemy utworzyć?

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Licznik |
|   | 18339 |

Teraz zamierzamy utworzyć zestaw 18339 szeregów czasowych metryki liczba odczytu. Dodamy `by` klauzula instrukcji serii upewnij zastosować regresji liniowej i wybierz pierwszych dwóch szeregów czasowych, których najbardziej znaczące zmniejszenie trendów:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Szeregi czasowe pierwszych dwóch](media/time-series-analysis/time-series-top-2.png)

Wyświetl wystąpienia:

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
|   | Lokalizacja | OP | DB | Krzywa |
|   | Lokalizacja 15 | 37 | 1151 | -102743.910227889 |
|   | Lokalizacja 13 | 37 | 1249 | -86303.2334644601 |

W niespełna dwie minuty ADX analizowane blisko 20 000 szeregów czasowych i wykryto dwa szeregów czasowych nietypowe, w których nagle porzucony odczytu liczby.

Te zaawansowane możliwości w połączeniu z wysoką wydajność ADX Podaj unikatowe i zaawansowane rozwiązanie do analizy serii czasowych.
