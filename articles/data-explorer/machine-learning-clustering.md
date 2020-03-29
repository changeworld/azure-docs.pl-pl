---
title: Funkcja uczenia maszynowego w Eksploratorze danych platformy Azure
description: Użyj klastrowania uczenia maszynowego do analizy głównej przyczyny w Eksploratorze danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: fe72031ef9ade7473dc4d5de7e090e92ef2a6843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769935"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Funkcja uczenia maszynowego w Eksploratorze danych platformy Azure

Usługa Azure Data Explorer, platforma analityczna Big Data, służy do monitorowania kondycji usługi, QoS lub nieprawidłowych działań urządzeń pod kątem nietypowych zachowań przy użyciu wbudowanych funkcji [wykrywania anomalii i prognozowania.](/azure/data-explorer/anomaly-detection) Po wykryciu nietypowego wzorca, analiza przyczyn źródłowych (RCA) jest wykonywana w celu złagodzenia lub rozwiązania anomalii.

Proces diagnozowania jest złożony i długotrwały i wykonywany przez ekspertów w dziedzinie. Proces obejmuje pobieranie i dołączanie dodatkowych danych z różnych źródeł w tym samym przedziale czasowym, poszukiwanie zmian w dystrybucji wartości w wielu wymiarach, tworzenie wykresów dodatkowych zmiennych i innych technik opartych na wiedzy o domenie i Intuicji. Ponieważ te scenariusze diagnozy są powszechne w Eksploratorze danych platformy Azure, wtyczki uczenia maszynowego są dostępne, aby ułatwić fazę diagnostyki i skrócić czas trwania RCA.

Usługa Azure Data Explorer ma [`autocluster`](/azure/kusto/query/autoclusterplugin)trzy [`basket`](/azure/kusto/query/basketplugin)wtyczki usługi Machine Learning: , i [`diffpatterns`](/azure/kusto/query/diffpatternsplugin). Wszystkie wtyczki implementują algorytmy klastrowania. I `autocluster` `basket` wtyczki klastra jeden zestaw `diffpatterns` rekordów i klastrów wtyczki różnice między dwoma zestawami rekordów.

## <a name="clustering-a-single-record-set"></a>Grupowanie pojedynczego zestawu rekordów

Typowy scenariusz zawiera zestaw danych wybrany według określonych kryteriów, takich jak przedział czasu, który wykazuje nietypowe zachowanie, odczyty urządzeń w wysokiej temperaturze, polecenia długi czas trwania i najlepszych użytkowników wydatków. Chcielibyśmy łatwy i szybki sposób, aby znaleźć wspólne wzorce (segmenty) w danych. Wzorce są podzbiorem zestawu danych, którego rekordy mają te same wartości w wielu wymiarach (kolumnach kategorii). Następujące kwerendy tworzy i pokazuje szeregi czasowe wyjątków usługi w ciągu tygodnia w pojemnikach dziesięć minut:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Timechart wyjątków usługi](media/machine-learning-clustering/service-exceptions-timechart.png)

Liczba wyjątków usługi koreluje z ogólnym ruchem usługi. Wyraźnie widać dzienny wzór, w dni robocze od poniedziałku do piątku, ze wzrostem liczby wyjątków usług w połowie dnia i spadki liczby w nocy. Płaskie niskie liczby są widoczne w weekend. Skoki wyjątków można wykryć za pomocą [wykrywania anomalii szeregów czasowych](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) w Eksploratorze danych platformy Azure.

Drugi skok w danych występuje we wtorek po południu. Poniższa kwerenda jest używana do dalszego diagnozowania tego skoku. Użyj kwerendy, aby ponownie rysować wykres wokół skoku w wyższej rozdzielczości (osiem godzin w jednominutowych pojemnikach), aby sprawdzić, czy jest to gwałtowny skok, i wyświetlić jego obramowania.

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=)**\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Skoncentruj się na wykresie czasowym skoków](media/machine-learning-clustering/focus-spike-timechart.png)

Widzimy wąski dwuminutowy skok od 15:00 do 15:02. W następującej kwerendzie zlicz wyjątki w tym dwuminutowym oknie:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Liczba |
|---------|
|972    |

W poniższej kwerendzie przykładowe 20 wyjątków z 972:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| Precyzyjnystatowy            | Region | Jednostka skalowa | DeploymentIdId (ida rozmieszcz                     | Punkt śledzenia | Servicehost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus (scus)   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus (scus)   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus (scus)   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus (scus)   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus (scus)   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus (scus)   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aee40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus (ncus)   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus (scus)   | Su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus (scus)   | Su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus (scus)   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus (scus)   | Su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus (scus)   | Su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus (ncus)   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus (scus)   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus (scus)   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus (scus)   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aee40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus (scus)   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus (scus)   | Su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Użyj autoklastr() do klastrowania pojedynczego zestawu rekordów

Mimo że istnieje mniej niż tysiąc wyjątków, nadal trudno jest znaleźć typowe segmenty, ponieważ w każdej kolumnie znajduje się wiele wartości. Za pomocą [`autocluster()`](/azure/kusto/query/autoclusterplugin) wtyczki można natychmiast wyodrębnić małą listę typowych segmentów i znaleźć interesujące klastry w ciągu dwóch minut spike'a, jak widać w następującej kwerendzie:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| Identyfikator segmentu | Liczba | Wartość procentowa | Region | Jednostka skalowa | DeploymentIdId (ida rozmieszcz | Servicehost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus (scus) | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus (ncus) | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus (scus) | Su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | Uze | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Z powyższych wyników wynika, że najbardziej dominujący segment zawiera 65,74% wszystkich rekordów wyjątków i dzieli cztery wymiary. Następny segment jest znacznie mniej powszechny, zawiera tylko 9,67% rekordów i dzieli trzy wymiary. Pozostałe segmenty są jeszcze mniej powszechne. 

Autocluster używa zastrzeżonego algorytmu do wydobywania wielu wymiarów i wyodrębniania interesujących segmentów. "Ciekawe" oznacza, że każdy segment ma znaczny zasięg zarówno zestawu rekordów, jak i ustawionych funkcji. Segmenty są również rozbieżne, co oznacza, że każdy z nich znacznie różni się od innych. Jeden lub więcej z tych segmentów może być istotnych dla procesu RCA. Aby zminimalizować przegląd i ocenę segmentu, autoklastr wyodrębnia tylko małą listę segmentów.

### <a name="use-basket-for-single-record-set-clustering"></a>Użyj koszyka() dla klastrowania pojedynczego zestawu rekordów

Można również użyć [`basket()`](/azure/kusto/query/basketplugin) wtyczki, jak widać w następującej kwerendzie:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| Identyfikator segmentu | Liczba | Wartość procentowa | Region | Jednostka skalowa | DeploymentIdId (ida rozmieszcz | Punkt śledzenia | Servicehost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus (scus) | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus (ncus) | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus (scus) | Su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus (scus) |  |  |  |  |
| 9 | 55 | 5.65843621399177 | Uze | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Koszyk implementuje algorytm Apriori dla górnictwa zestawu towarów i wyodrębnia wszystkie segmenty, których pokrycie zestawu rekordów jest powyżej progu (domyślnie 5%). Widać, że więcej segmentów zostało wyodrębnionych z podobnymi (na przykład segmenty 0,1 lub 2,3).

Obie wtyczki są wydajne i łatwe w użyciu, ale ich znaczącym ograniczeniem jest to, że grupują pojedynczy rekord ustawiony w sposób nienadzorowany (bez etykiet). W związku z tym nie jest jasne, czy wyodrębnione wzorce charakteryzują wybrany zestaw rekordów (rekordy nietypowe) lub zestaw rekordów globalnych.

## <a name="clustering-the-difference-between-two-records-sets"></a>Klastrowanie różnicy między dwoma zestawami rekordów

Plugin [`diffpatterns()`](/azure/kusto/query/diffpatternsplugin) pokonuje ograniczenie `autocluster` `basket`i . `Diffpatterns`pobiera dwa zestawy rekordów i wyodrębnia główne segmenty, które różnią się między nimi. Jeden zestaw zwykle zawiera nietypowy zestaw rekordów, `autocluster` `basket`który jest badany (jeden analizowany przez i ). Drugi zestaw zawiera zestaw rekordów odniesienia (linia bazowa). 

W poniższej kwerendzie `diffpatterns` używamy do znalezienia interesujących klastrów w ciągu dwóch minut spike'a, które różnią się od klastrów w linii bazowej. Definiujemy okno linii bazowej jako osiem minut przed 15:00 (po rozpoczęciu skoku). Musimy również rozszerzyć o kolumnę binarną (AB), określając, czy określony rekord należy do linii bazowej, czy do zestawu nietypowego. `Diffpatterns`implementuje nadzorowany algorytm uczenia się, gdzie dwie etykiety klas zostały wygenerowane przez nietypowe w porównaniu do flagi linii bazowej (AB).

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| Identyfikator segmentu | CountA | LiczbaB | Percenta (percenta) | PercentB (wartość procentowa) | PercentDiffAB (ProcentDiffAB) | Region | Jednostka skalowa | DeploymentIdId (ida rozmieszcz | Punkt śledzenia |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus (scus) |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus (ncus) | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | Uze | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

Najbardziej dominującym segmentem jest ten sam `autocluster`segment, który został wyodrębniony przez , jego zasięg w dwuminutowym nienormanym oknie wynosi również 65,74%. Ale jego zasięg w ośmiominutowym oknie bazowym wynosi tylko 1,7%. Różnica wynosi 64,04%. Różnica ta wydaje się być związane z nietypowe skok. Można to zweryfikować, dzieląc oryginalny wykres na rekordy należące do tego problematycznego segmentu w porównaniu z innymi segmentami, jak widać w poniższej kwerendzie:

**\[**[**Kliknij, aby uruchomić kwerendę**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Sprawdzanie poprawności schematu czasowego segmentu "diffpattern"](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Ten wykres pozwala nam zobaczyć, że skok we wtorek po południu był z `diffpatterns` powodu wyjątków z tego konkretnego segmentu, odkryte za pomocą wtyczki.

## <a name="summary"></a>Podsumowanie

Wtyczki usługi Azure Data Explorer machine learning są przydatne w wielu scenariuszach. I `autocluster` `basket` wdrożyć algorytm uczenia się bez nadzoru i są łatwe w użyciu. `Diffpatterns`implementuje nadzorowany algorytm uczenia się i choć bardziej złożony, jest bardziej wydajny w wyodrębnianiu segmentów różnicowania dla RCA.

Wtyczki te są używane interaktywnie w scenariuszach ad-hoc i w automatycznych usługach monitorowania w czasie zbliżonym do rzeczywistego. W Eksploratorze danych platformy Azure wykrywanie anomalii szeregów czasowych następuje proces diagnozowania, który jest wysoce zoptymalizowany, aby spełnić niezbędne standardy wydajności.
