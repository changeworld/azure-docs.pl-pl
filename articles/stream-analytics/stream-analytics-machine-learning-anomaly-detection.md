---
title: Wykrywanie anomalii w usłudze Azure Stream Analytics (wersja zapoznawcza)
description: W tym artykule opisano sposób korzystania ze sobą usługi Azure Stream Analytics i Azure Machine Learning do wykrywania anomalii.
services: stream-analytics
author: dubansal
ms.author: dubansal
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 2f35f54c7ec5ad169673aebe08602294270f470a
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364459"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Wykrywanie anomalii w usłudze Azure Stream Analytics

> [!IMPORTANT]
> Ta funkcja jest w trakcie wycofywane, ale zostanie zastąpiony nowych funkcji. Aby uzyskać więcej informacji, odwiedź stronę [osiem nowych funkcji w usłudze Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/) wpis w blogu.

**AnomalyDetection** operator jest używany do wykrywania różne rodzaje anomalii w strumieniach zdarzeń. Na przykład powolne spadek wolnej pamięci przez dłuższy czas może wskazywać przeciek pamięci lub liczbę żądań usług sieci web, które są trwałe w zakresie może znacznie zwiększyć lub zmniejszyć.  

AnomalyDetection operator wykrywa trzy rodzaje anomalii: 

* **Zmień poziom dwukierunkowego**: stałego zwiększyć lub zmniejszyć poziom wartości, zarówno w górę, jak i w dół. Ta wartość jest inna niż wzrostów i DIP, które są zmianami natychmiastowego lub krótkotrwałe.  

* **Wolne trendów pozytywnych**: powolne wzrost trend wraz z upływem czasu.  

* **Wolne ujemna Trend**: powolne spadek trend wraz z upływem czasu.  

Za pomocą operatora AnomalyDetection, należy określić **Limit Duration** klauzuli. Ta klauzula Określa przedział czasu (jak daleko w historii z bieżącego zdarzenia) należy uwzględnić podczas wykrywania anomalii. Opcjonalnie Ten operator może być ograniczone wyłącznie te zdarzenia, które odpowiadają określoną właściwość lub warunek, za pomocą  **podczas**  klauzuli.   Ten operator może również opcjonalnie przetwarzać grup zdarzeń oddzielnie w zależności od klucza określonego w **partycji przez** klauzuli. Uczenia i przewidywania występować niezależnie dla każdej partycji. 

## <a name="syntax-for-anomalydetection-operator"></a>Składnia AnomalyDetection — operator

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**Przykład użycia**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>Argumenty

* **wyrażenie_skalarne** -wyrażenie skalarne, nad którym odbywa się wykrywanie anomalii. Dozwolone wartości dla tego parametru obejmują Float lub typy danych Bigint oznacza zwracany pojedynczy () wartość skalarną. Wyrażenia z symbolami wieloznacznymi **\*** jest niedozwolone. Wyrażenie skalarne, które nie mogą zawierać inne funkcje analityczne lub funkcji zewnętrznych. 

* **partition_by_clause** — `PARTITION BY <partition key>` klauzuli dzieli edukacyjnych i szkoleniowych w oddzielnych partycjach. Innymi słowy, osobnymi plikami modelu będzie używana dla wartości `<partition key>` i tylko zdarzenia przy użyciu tej wartości będą używane na potrzeby edukacyjnych i szkoleniowych w tym modelu. Na przykład następujące zapytanie pociągów i przeznaczona do oceniania odczytu względem innych odczyty tylko tych samych czujnik:

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **Klauzula limit_duration** `DURATION(<unit>, <length>)` -Określa przedział czasu (jak daleko w historii z bieżącego zdarzenia) należy uwzględnić podczas wykrywania anomalii. Zobacz [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) szczegółowy opis obsługiwanych jednostek i ich skrótami. 

* **when_clause** -określa warunek logiczny dla zdarzeń uwzględnione podczas obliczania wykrywania anomalii.

### <a name="return-types"></a>Typy zwracane

AnomalyDetection operator zwraca rekord zawierający wszystkie trzy wyniki jako dane wyjściowe. Właściwości skojarzone z różnymi typami anomalii detektorów są:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Aby wyodrębnić poszczególne wartości z rekordu, użyj **funkcji GetRecordPropertyValue** funkcji. Na przykład:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

Anomalii typu została wykryta, gdy jeden z wyników anomalii przekracza próg. Próg, może być dowolną liczbą zmiennoprzecinkową > = 0. Próg wynosi zależność między poufności i zaufania. Na przykład niższego progu ułatwić wykrywanie bardziej wrażliwy na zmiany i generować więcej alertów wyższej wartości progowej może ułatwić wykrywanie mniej poufnych i większa pewność, ale maski niektóre anomalii. Wartość progowa dokładnie do użycia zależy od scenariusza. Nie ma żadnego limitu górnego zakresu zalecane jest jednak 3,25 5. 

Wartość 3,25 pokazano w przykładzie jest po prostu sugerowane punkt początkowy. Dostosuj wartość, uruchamiając operacje na zestawie danych i sprawdź, czy wartość wyjściowa aż dopuszczalna wartość progową.

## <a name="anomaly-detection-algorithm"></a>Algorytm wykrywania anomalii

* AnomalyDetection operator używa **uczenie nienadzorowane** podejścia, w której nie zakłada dowolnego typu dystrybucji w zdarzeniach. Ogólnie rzecz biorąc dwa modele są obsługiwane w sposób równoległy w dowolnym momencie, gdzie jeden z nich służy do oceniania, a drugi jest uczony w tle. Modele wykrywania anomalii są uczone przy użyciu danych z bieżącego strumienia, a nie przy użyciu mechanizmu poza pasmem. Dane używane na potrzeby szkolenia zależy od d rozmiar okna, które są określone przez użytkownika w ramach parametru Limit Duration. Każdy model kończy się wprowadzenie skonfigurowanych pod kątem zależnie od d-2d zdarzeń z długiego okresu. Zaleca się mieć co najmniej 50 zdarzenia każdego okna w celu uzyskania najlepszych wyników. 

* AnomalyDetection operator używa **przesuwanego okna semantyki** do uczenia modeli i wynik zdarzenia. Co oznacza, że każde zdarzenie sprawdzania pod kątem anomalii, a wynik jest zwracany. Wynik jest wskazanie poziom zaufania tym anomalii. 

* Zawiera AnomalyDetection operator **gwarancji powtarzalności** takie same dane wejściowe zawsze daje ten sam wynik, niezależnie od tego, dane wyjściowe zadania czas rozpoczęcia. Godzina rozpoczęcia zadania danych wyjściowych reprezentuje czas, w którym pierwsze zdarzenie w danych wyjściowych jest generowany przez zadanie. Jest on ustawiony przez użytkownika do bieżącego czasu, wartość niestandardową lub czas ostatniego wyjścia (jeśli jest to zadanie było wcześniej utworzone dane wyjściowe). 

### <a name="training-the-models"></a>Szkolenie modeli 

W miarę postępów czasu modele są uczone z różnymi danymi. Aby poznać wyniki, pomaga to zrozumieć podstawowy mechanizm, za pomocą którego są uczone modele. W tym miejscu **t<sub>0</sub>**  jest **czas rozpoczęcia dane wyjściowe zadania** i **d** jest **rozmiar okna** z Limit Duration parametr. Przyjęto założenie, że czas jest podzielony na **przeskoków d rozmiar**, poczynając od `01/01/0001 00:00:00`. Poniższe kroki są używane do nauczenia modelu a wynik zdarzenia:

* Podczas uruchamiania zadania odczytuje dane, zaczynając od czasu t<sub>0</sub> — 2d.  
* Gdy czas osiągnie następny przeskok, nowy model M1 zostanie utworzony i rozpoczyna się wprowadzenie skonfigurowanych pod kątem.  
* Czas jest przesuwany o przeskoku innym, nowy model M2 jest tworzony a rozpoczyna się wprowadzenie skonfigurowanych pod kątem.  
* Gdy czas osiągnie t<sub>0</sub>M1 zostanie aktywowane i jego wynik, który rozpoczyna się wprowadzenie zwrócone.  
* Na następny przeskok trzy rzeczy odbywa się na tym samym czasie:  

  * M1 nie są już potrzebne, i jest pomijany.  
  * M2 ma wystarczająco uczony, dzięki czemu jest używana do oceniania.  
  * Nowy model M3 zostanie utworzony i rozpoczyna się wprowadzenie nauczone w tle.  

* Ten cykl powtarza się dla każdego przeskoku w przypadku, gdy aktywnego modelu jest pomijany, przełącz się do równoległych modelu i Rozpocznij szkolenie trzecim modelu w tle. 

Schematycznie kroki wyglądać w następujący sposób: 

![Szkolenie modeli](media/stream-analytics-machine-learning-anomaly-detection/training_model.png)

|**Model** | **Czas rozpoczęcia szkolenia** | **Czas, aby rozpocząć korzystanie z jej wynik** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* M1 modelu uruchamia szkolenia o 11:20, czyli następny przeskok po odczyt 11:13 am rozpoczęcia zadania. Pierwszy danych wyjściowych jest generowany z M1 o 11:33 po szkolenie przy użyciu 13 minut danych. 

* Nowy model M2 rozpoczyna się szkolenia o 11:30, ale nie jej wynik Pobierz używane w aż do 11:40:00, czyli po po zapoznaniu z 10 minut danych. 

* M3 jest zgodna z tym samym wzorcem, jako M2. 

### <a name="scoring-with-the-models"></a>Ocenianie z modeli 

Na poziomie usługi Machine Learning algorytm wykrywania anomalii oblicza wartość strangeness dla każdego zdarzenia przychodzące, porównując ją za pomocą zdarzeń w oknie historii. Obliczenie strangeness różni się dla każdego typu anomalii.  

Omówmy teraz obliczeń strangeness szczegółowo (przyjmowane jest, istnieje zestaw historycznych systemu Windows ze zdarzeniami): 

1. **Zmień poziom dwukierunkowego:** oparte na oknie historii, normalnego zakresu działania jest obliczana jako [10 percentyl, 90. percentyla] oznacza to, firma 10th wartość percentylu z wartością dolną granicę i 90. percentyl jako górną granicę. Wartość strangeness dla bieżącego zdarzenia jest obliczana jako:  

   - 0, jeśli event_value normalnego zakresu działania  
   - Jeśli event_value/90th_percentile event_value > 90th_percentile  
   - 10th_percentile/event_value, jeśli jest event_value < 10th_percentile  

2. **Powolne trendów pozytywnych:** linii trendu za pośrednictwem wartości zdarzeń w oknie historii jest obliczana, a operacja szuka dodatnią trend w wierszu. Wartość strangeness jest obliczana jako:  

   - Krzywa, jeśli nachylenie jest dodatnia  
   - 0, w przeciwnym razie 

3. **Powolne trend ujemna:** linii trendu za pośrednictwem wartości zdarzeń w oknie historii jest obliczana, a operacja szuka trendów ujemne w wierszu. Wartość strangeness jest obliczana jako: 

   - Krzywa, jeśli nachylenie jest ujemna  
   - 0, w przeciwnym razie  

Gdy jest obliczana wartość strangeness przychodzącym zdarzeniu, wartość martingale jest obliczana na podstawie wartości strangeness (zobacz [blogu usługi Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) Aby uzyskać szczegółowe informacje, w jaki sposób jest obliczana wartość martingale). Ta wartość martingale jest zwracany jako wyniku anomalii. Wartość martingale zwiększa się wolno w odpowiedzi na dziwne wartości, która umożliwia wykrywanie pozostaje niezawodne sporadyczne zmian i zmniejsza fałszywych alertów. Ma on również przydatne właściwości: 

Prawdopodobieństwo [istnieje t takich tego M<sub>t</sub> > λ] < 1/λ, gdzie M<sub>t</sub> -wartość martingale błyskawiczne t i λ jest rzeczywistą wartość. Na przykład, jeśli alert po M<sub>t</sub>> 100, a następnie prawdopodobieństwa wyników fałszywie dodatnich jest mniejsza niż 1/100.  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>Wskazówki dotyczące używania poziom dwukierunkowego zmienić wykrywanie 

Wykrywanie zmiany poziomu dwukierunkowej może służyć w scenariuszach, takich jak power awarii i odzyskiwania lub godzinę łazienkowych ruchu itp. Jednak działa w taki sposób, że gdy model jest uczony przy użyciu określonych danych, inna zmiana poziomu nietypowe tylko wtedy, gdy nowa wartość jest wyższa niż poprzednie górny limit (podwyższeniu poziomu Zmień wielkość liter) lub mniejsza niż poprzednie niższy limit (w dół poziom Zmień wielkość liter). W związku z tym model nie powinien wartości danych w zakresie nowy poziom (w górę lub w dół) w jego oknie szkolenia dla nich zostały uznane za nieprawidłowe. 

Korzystając z tego wykrywanie, należy rozważyć następujące kwestie: 

1. Gdy szeregów czasowych nagle widzi wzrost lub drop w wartości, operatora AnomalyDetection wykrywa go. Ale wykrywanie powrotu do normalnego wymaga więcej planowania. Jeśli w stanie stabilności przed anomalii, w którym można osiągnąć przez ustawienie AnomalyDetection operator wykrywania okna co najwyżej połowa długości anomalii szeregów czasowych. W tym scenariuszu przyjęto założenie, że wcześniej, można oszacować minimalny czas trwania anomalii i istnieją wystarczającej liczby zdarzeń w tym czasie do nauczenia modelu wystarczająco (co najmniej 50 zdarzeń). 

   Jest to pokazane na rysunku 1 i 2 poniżej z użyciem zmian górny limit (ta sama logika dotyczy niższe zmiany limitu). W obu wartości liczbowych kształty fali są nietypowe zmiany poziomu. Pionowe linie pomarańczowy oznaczają granice przeskoku i rozmiar przeskoku jest taka sama jak określono w operatorze AnomalyDetection okna wykrywania. Zielony linie wskazują rozmiar okna szkolenia. Na rysunku 1. rozmiar przeskoku jest taka sama jak czasu, dla których jest dostępna anomalii. Na rysunku 2 rozmiar przeskoku jest połowę czasu, dla których trwa anomalii. We wszystkich przypadkach zostanie wykryta zmiana palcem, ponieważ model wykorzystywany do oceniania został skonfigurowanych pod kątem na normalne danych. Ale oparte na jak działa wykrywanie zmiany poziomu dwukierunkowej, go wykluczyć normalnych wartości z okna szkolenia użyty dla modelu, który ocenia powrotu do normalnego. Na rysunku 1 szkoleń modelowych oceniania obejmuje niektóre zdarzenia normalne, więc nie można wykryć powrót do normalnego. Jednak na rysunku 2, szkolenie obejmuje tylko nietypowe strony, który zapewnia, że wykryto powrotu do normalnego. Mniejsze niż połowy współpracuje również z tego samego powodu należy nic większe znajdą włącznie bitowe normalne zdarzeń. 

   ![Usługi AD o długości anomalii w taki sam rozmiar okna](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_anomaly_length.png)

   ![AD przy użyciu rozmiaru okna jest równa połowie długość anomalii](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_half_anomaly_length.png)

2. W przypadkach, w których nie można przewidzieć długość anomalii to wykrywanie działa w najlepszym nakładu pracy. Jednak wybór mniejszą niż przedział czasu ogranicza dane szkoleniowe, które będzie zwiększyć prawdopodobieństwo wykrycia powrotu do normalnego. 

3. W poniższym scenariuszu dłużej anomalii nie wykryte jako okno szkolenia zawiera już anomalii o tej samej wysokiej wartości. 

   ![Anomalie z takim samym rozmiarze](media/stream-analytics-machine-learning-anomaly-detection/anomalies_with_same_length.png)

## <a name="example-query-to-detect-anomalies"></a>Przykładowe zapytanie do wykrywania anomalii 

Następujące zapytanie, może służyć do wypełniania wyjściowego alert w przypadku wykrycia anomalii.
Gdy strumień wejściowy nie jest jednolite, krok agregacji może pomóc przekształcić ją w serii jednolita postać czasu. W przykładzie użyto AVG, ale konkretny typ agregacji jest zależny od scenariusza użytkownika. Ponadto gdy szeregów czasowych ma większe niż okno agregacji luki, nie ma żadnych zdarzeń w szeregu czasowym, wykrywanie anomalii wyzwalacza (zgodnie z przesuwanego okna semantyki). W rezultacie przy założeniu jednolitość został przerwany, po nadejściu następne zdarzenie. W takich sytuacjach należy wprowadzić luki w szeregu czasowym. Jedno z możliwych podejść jest wzięcie ostatnie zdarzenie w każdego okna przeskoku, jak pokazano poniżej.

```sql
    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) >= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) >=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) >=
       3.25
```

## <a name="performance-guidance"></a>Wskazówki dotyczące wydajności

* W przypadku zadań, należy użyć sześciu jednostek przesyłania strumieniowego. 
* Wysyłanie zdarzeń co najmniej jednej sekundy od siebie.
* Niepartycjonowana zapytania, który jest używany AnomalyDetection operator może wygenerować wyniki przeciętnie z opóźnieniem obliczeń około 25 ms.
* Opóźnienie odczuwalnych podzielonym na partycje zapytanie różni się nieco z liczbą partycji, ponieważ liczba obliczeń jest większa. Jednak opóźnienie jest prawie taki sam jak w przypadku niepartycjonowana porównywalne łączna liczba zdarzeń wszystkich partycji.
* Podczas odczytywania danych się w czasie rzeczywistym, szybko są pozyskiwane dużej ilości danych. Przetwarzanie tych danych jest obecnie wolniejsze. Stwierdzono, że opóźnienie w takich scenariuszach rośnie liniowo z liczba punktów danych w oknie, a nie interwał rozmiaru lub zdarzeń okna. Aby zmniejszyć opóźnienia w przypadku się w czasie rzeczywistym, należy wziąć pod uwagę przy użyciu mniejszego rozmiaru okna. Można również rozważyć uruchomienie zadania od bieżącego czasu. Kilka przykładów opóźnienia w zapytaniu niepartycjonowana: 
    - 60 punktów danych w oknie wykrywania może spowodować opóźnienia w 10 sekund przy przepływności równej 3 MB/s. 
    - 600 punktów danych opóźnienie docierać do około 80 sekund przy przepływności 0,4 MB/s.

## <a name="limitations-of-the-anomalydetection-operator"></a>Ograniczenia operatora AnomalyDetection 

* Ten operator nie obsługuje obecnie wykrywania kolekcji i dip. Wzrostów i spadków modyfikacje spontanicznej lub krótkotrwałe w szeregu czasowym. 

* Ten operator aktualnie nie obsługuje wzorce sezonowość. Sezonowość wzorce są wzorce powtórzeń w danych, na przykład zachowanie ruchu innej witryny sieci web podczas weekendów lub różnych zakupów trendów w czarny piątek, nie będących anomalie, ale oczekiwanym wzorcem w zachowaniu. 

* Ten operator oczekuje serii czas wejściowy jest jednolita. Strumień zdarzeń można ujednolicone przez agregowanie za pośrednictwem wirowania lub przeskokiem okna. W scenariuszach, gdzie przerwy między zdarzeniami zawsze jest mniejszy niż okno agregacji okno wirowania jest wystarczające, aby wprowadzić jednolitego szeregów czasowych. Podczas przerwy, może być większy, można podać luki, powtarzając ostatnie wartości za pomocą okna przeskoku. 

## <a name="references"></a>Dokumentacja

* [Wykrywanie anomalii — używanie uczenia maszynowego w celu wykrycia nieprawidłowości w danych szeregów czasowych](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Interfejs API wykrywania anomalii uczenia maszynowego](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Wykrywanie anomalii w czasie serii](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

