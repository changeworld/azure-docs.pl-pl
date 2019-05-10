---
title: Usługi Machine learning możliwości w Eksploratorze danych platformy Azure
description: Korzystanie z uczenia maszynowego, klastrowania analizą głównych przyczyn problemów w Eksploratorze danych platformy Azure.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: bc72cc21ab525ec82d9ce4b24e80ce82d92a5d21
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233495"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Usługi Machine learning możliwości w Eksploratorze danych platformy Azure

Eksplorator usługi Azure Data, platformy do analizy danych big Data jest używana do monitorowania kondycji usługi, QoS lub nieprawidłowo działający urządzeń nietypowe zachowanie przy użyciu wbudowanych [wykrywanie anomalii i przygotowywać trafniejsze prognozy](/azure/data-explorer/anomaly-detection) funkcji. Po wykryciu nietypowego wzorca analizy przyczyny głównej (RCA) jest przeprowadzana w celu złagodzenia lub rozwiązać anomalii.

Proces diagnostyki jest złożona i długich i gotowe przez ekspertów z konkretnych dziedzin. Proces obejmuje pobieranie i dołączenie dodatkowych danych dla tego samego horyzontu czasowego, wyszukiwanie zmiany w rozkładu wartości w wielu wymiarach, tworzenie wykresów dodatkowe zmienne z różnych źródeł i innych technik oparty na wiedzy specjalistycznej i intuition. Ponieważ te scenariusze diagnostyki są wspólne w Eksploratorze danych platformy Azure, machine learning wtyczki są dostępne dla ułatwienia fazy diagnostyki i skrócić czas trwania — analiza głównej przyczyny.

Eksplorator usługi Azure Data ma trzy wtyczki usługi Machine Learning: [ `autocluster` ](/azure/kusto/query/autoclusterplugin), [ `basket` ](/azure/kusto/query/basketplugin), i [ `diffpatterns` ](/azure/kusto/query/diffpatternsplugin). Wszystkie dodatki plug-in zaimplementować algorytmami klastrowego. `autocluster` i `basket` wtyczek klastra jeden zestaw rekordów i `diffpatterns` wtyczki klastrów różnice między dwoma zestawami rekordów.

## <a name="clustering-a-single-record-set"></a>Klastrowanie zestaw pojedynczy rekord

Typowy scenariusz obejmuje zestaw danych, wybranych przez określone kryteria, takich jak przedział czasu, która wykazuje nietypowego zachowania dotyczącego, temperatura urządzenie odczyty, czas trwania długiej poleceń i górnej wydatków użytkowników. Prosimy o poświęcenie prosty i szybki sposób znaleźć typowe wzorce (segmenty) w danych. Wzorce stanowią podzestaw zestawu danych, w której rekordy współużytkują te same wartości w wielu wymiarach (podzielone na kategorie kolumny). Następujące zapytanie, kompilacji i spowodują dłużej niż przez tydzień pojemniki 10 minutowy szeregów czasowych wyjątków usługi:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Wykres czasu wyjątków usługi](media/machine-learning-clustering/service-exceptions-timechart.png)

Liczba wyjątków usługi mają związek z ogólnych ruch usługi. Wyraźnie widać wzorzec dzienny dla dni roboczych, z okresu od poniedziałku do piątku, z wzrost usługi wyjątek jest liczona środku dnia i mocno spadł w liczby w nocy. Płaskie niskiej liczby są widoczne w ciągu weekendu. Wyjątek wartości graniczne mogą zostać wykryte za pomocą [czasu wykrywania anomalii serii](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) w Eksploratorze danych platformy Azure.

Druga Kolekcja danych odbywa się na wtorek po południu użycie. Następujące zapytanie służy w dalszym diagnozowaniu tej kolekcji. Użyj zapytania, aby odświeżyć wykresu wokół wzrost wyższa rozdzielczość (8 godzin w pojemnikach co minutę), aby sprawdzić, czy jest ono gwałtowny wzrost i wyświetl jego obramowania.

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Skup się na wykres czasu kolekcji](media/machine-learning-clustering/focus-spike-timechart.png)

Widzimy wąskie kolekcji dwóch minut od 15:00 do 15:02. W następującym zapytaniu liczba wyjątków w tym oknie dwie minuty:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Count |
|---------|
|972    |

W następującym zapytaniu przykładowy 20 wyjątki poza 972:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Obszar | ScaleUnit | DeploymentId                     | Punkt śledzenia | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Autocluster() na użytek pojedynczy rekord, ustaw klastrowania

Mimo że ma mniej niż tysiąc wyjątków, jest nadal trudno znaleźć wspólnego segmenty, jak wiele wartości w każdej kolumnie. Możesz użyć [ `autocluster()` ](/azure/kusto/query/autoclusterplugin) dodatek plug-in, aby natychmiast wyodrębnić krótką listę typowych segmenty i znajdowanie interesujących klastrów w ciągu dwóch minut w kolekcji, jak pokazano w następującym zapytaniu:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Count | Wartość procentowa | Obszar | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | UZE | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Powyższe wyniki można zobaczyć, najbardziej dominujący segment zawiera 65.74% rekordów łączna liczba wyjątków i udostępnia cztery wymiary. Następny segment jest znacznie mniej typowe, zawiera tylko 9.67% rekordów i udostępnia trzy wymiary. Inne segmenty są nawet rzadziej. 

Autocluster korzysta z własnościowego algorytmu do wyszukiwania wiele wymiarów i wyodrębniania interesujące segmentów. "Interesujących" oznacza, że każdy z segmentów ma znaczące pokrycia zarówno zestaw rekordów, jak i zestaw funkcji. Segmenty są również zmieniały się różnice, co oznacza, że każdy z nich jest znacznie różnią się od innych. Co najmniej jeden z tych segmentach mogą być istotne dla procesu — analiza głównej przyczyny. Aby zminimalizować segmentu Przegląd i ocena, autocluster wyodrębnia listę niewielkich segmentów.

### <a name="use-basket-for-single-record-set-clustering"></a>Basket() na użytek pojedynczy rekord, ustaw klastrowania

Można również użyć [ `basket()` ](/azure/kusto/query/basketplugin) wtyczki, jak pokazano w następującym zapytaniu:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Count | Wartość procentowa | Obszar | ScaleUnit | DeploymentId | Punkt śledzenia | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | UZE | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Koszyk implementuje algorytm Apriori dla elementu zestawu wyszukiwania i wyodrębnia wszystkie segmenty, w których pokrycie zestawu rekordów jest powyżej wartości progowej (wartość domyślna to % 5). Aby zobaczyć, że więcej segmentów zostały wyodrębnione z podobnych (na przykład, segmentów 0,1 lub 2,3).

Wtyczki są wydajne i łatwy w użyciu, ale ich znaczne ograniczenie to z faktu, że ich klastra pojedynczy zestaw rekordów w sposób nienadzorowanych (z nie etykiety). Dlatego jest niejasny czy wyodrębnione wzorców scharakteryzowania wybranego zestawu rekordów (nietypowe rekordów) lub globalnego zestawu rekordów.

## <a name="clustering-the-difference-between-two-records-sets"></a>Klastrowanie różnica między zestawami dwa rekordy

[ `diffpatterns()` ](/azure/kusto/query/diffpatternsplugin) Wtyczka pozwala pokonać ograniczenia `autocluster` i `basket`. `Diffpatterns` przyjmuje dwa zestawy rekordów i wyodrębnia segmentów główne, które różnią się między nimi. Jeden zestaw zawiera zazwyczaj nietypowe rekordu, ustaw badamy (jeden analizowane przez `autocluster` i `basket`). Drugi zestaw zawiera odwołanie do zestawu rekordów (linia bazowa). 

W poniższym zapytaniu używamy `diffpatterns` Aby znaleźć interesujące klastrów w ciągu dwóch minut kolekcji, które różnią się od klastrów w ramach planu bazowego. Definiujemy okna bazowego ośmiu minut przed 15:00 (podczas uruchamiania kolekcji). Musimy również rozszerzyć, kolumna typu binary (AB) określający, czy określony rekord należy do linii bazowej lub nietypowych zestawu. `Diffpatterns` implementuje Algorytm uczenia nadzorowanego, w którym etykiety dwuklasowego zostały wygenerowane przez nietypowe i Flaga linii bazowej (AB).

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

| SegmentId | CountA | CountB | PercentA | percentB | PercentDiffAB | Obszar | ScaleUnit | DeploymentId | Punkt śledzenia |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | UZE | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

Segment najbardziej dominujący jest tym samym segmencie, który został wyodrębniony przez `autocluster`, jej zakresu w oknie nietypowe dwie minuty, jest również 65.74%. Ale jej zakresu w oknie minutę 8 odniesienia jest tylko % 1.7. Różnica polega na 64.04%. Różnica ta wydaje się być one związane z nietypowych kolekcji. Możesz sprawdzić to założenie, dzieląc oryginalny wykres na rekordy należące do tego odcinka problematyczne i innych klas, jak pokazano w poniższym zapytaniu:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![Sprawdzanie poprawności wykres czasu segmentu "diffpattern"](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Ten wykres pozwala nam sprawdzenia, czy kolekcji na wtorek po południu użycie było ze względu na wyjątki z tego konkretnego segmentu odnalezione za pomocą `diffpatterns` wtyczki.

## <a name="summary"></a>Podsumowanie

Wtyczki usługi Azure Data Explorer Machine Learning są przydatne w wielu scenariuszach. `autocluster` i `basket` implementować Algorytm uczenia nienadzorowanych i są łatwe w użyciu. `Diffpatterns` implementuje w trybie nadzorowanym algorytmu uczenia i chociaż bardziej skomplikowane, jest bardziej wydajne podczas wyodrębniania rozróżnienia odcinki dla — analiza głównej przyczyny.

Te dodatki plug-in są używać hasła interaktywnie w scenariuszach ad hoc i automatyczne niemal w czasie rzeczywistym usługi monitorowania. W Eksploratorze danych platformy Azure wykrywanie anomalii w czasie serii następuje proces diagnostyki, wysoce zoptymalizowane pod kątem spełniać normy wydajności na potrzeby.
