---
title: Wykrywanie anomalii w usłudze Azure Stream Analytics (wersja zapoznawcza)
description: W tym artykule opisano, jak używać usługi Azure Stream Analytics i usługi Azure Machine Learning razem do wykrywania anomalii.
services: stream-analytics
author: dubansal
ms.author: dubansal
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: e7274e4507d901a209ed5832e98ca630feefda4f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31420099"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Wykrywanie anomalii w Azure Stream Analytics

> [!IMPORTANT]
> Ta funkcja jest dostępna w wersji zapoznawczej, nie jest zaleca się korzystanie z obciążeń produkcyjnych.

**AnomalyDetection** operator jest używana do wykrywania różnych typów anomalii w strumieni zdarzeń. Na przykład powolne spadek wolnej pamięci przez dłuższy czas może wskazywać przeciek pamięci lub liczbę żądań usług sieci web, które są trwałe w zakresie może znacznie zwiększyć lub zmniejszyć.  

AnomalyDetection operator wykrywa trzy typy anomalii: 

* **Zmień poziom dwukierunkowego**: utrzymujących zwiększyć lub zmniejszyć poziom wartości, zarówno w górę, jak i w dół. Ta wartość jest inna niż impulsy i DIP, które zmian natychmiastowego lub w krótkim okresie.  

* **Powolna dodatnią Trend**: powolne wzrost trend w czasie.  

* **Powolna ujemna Trend**: powolne spadek trend w czasie.  

Przy użyciu operatora AnomalyDetection, należy określić **Limit Duration** klauzuli. Klauzulę Określa przedział czasu (jak daleko w historii z bieżącego zdarzenia) należy uwzględnić podczas wykrywania anomalii. Ten operator może być opcjonalnie maksymalnie tylko zdarzenia, które odpowiada za pomocą niektórych właściwości lub warunek **podczas** klauzuli. Ten operator może przetwarzać również opcjonalnie grup zdarzeń klucz określony w oparciu o oddzielnie **partycji przez** klauzuli. Szkolenie i prognozowania są wykonywane niezależnie dla każdej partycji. 

## <a name="syntax-for-anomalydetection-operator"></a>Składnia AnomalyDetection — operator

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**Przykład użycia**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>Argumenty

* **scalar_expression** — wyrażenie skalarne, w którym odbywa się wykrywania anomalii. Dozwolone wartości dla tego parametru to Float lub typy danych Bigint to zwracany pojedynczą wartość (skalarną). Wyrażenia z symbolami wieloznacznymi **\*** jest niedozwolone. Wyrażenie skalarne nie może zawierać inne funkcje analityczne lub funkcji zewnętrznych. 

* **partition_by_clause** — `PARTITION BY <partition key>` klauzuli dzieli uczenie i szkolenia w osobnych partycji. Innymi słowy, oddzielne modelu będzie służyć za wartości `<partition key>` i tylko zdarzenia z daną wartością mają być używane do nauki i szkolenia w tym modelu. Na przykład następujące pociągu zapytania i wyniki a odczytu względem innych odczyty tylko tej samej czujnika:

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **Klauzula limit_duration** `DURATION(<unit>, <length>)` — Określa przedział czasu (jak daleko w historii z bieżącego zdarzenia) należy uwzględnić podczas wykrywania anomalii. Zobacz [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) szczegółowy opis obsługiwanych jednostek i ich skrótów. 

* **when_clause** — określa warunek typu boolean dla zdarzeń w obliczenia wykrywania anomalii.

### <a name="return-types"></a>Zwracane typy

AnomalyDetection operator zwraca rekord zawierający wszystkie trzy wyniki jako dane wyjściowe. Właściwości skojarzone z różnymi typami detektory anomalii są:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Aby wyodrębnić poszczególne wartości poza rekordu, użyj **funkcji GetRecordPropertyValue** funkcji. Na przykład:

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

Anomalii typu została wykryta, gdy jeden z wyników anomalii przekracza próg. Próg, może być dowolna liczba zmiennoprzecinkowa > = 0. Próg wynosi zależności między czułość i zaufania. Na przykład niższego progu wprowadzić bardziej podatna na zmiany wykrywania i generowanie więcej alertów wyższa wartość progową można wprowadzić wykrywania mniej poufne i większa pewność, ale zamaskować niektórych anomalii. Wartość progowa dokładnie do użycia zależy od scenariusza. Nie ma żadnego limitu górnego, ale zalecane zakres jest 3,25 5. 

Wartość 3,25 pokazano w przykładzie jest po prostu sugerowane punkt początkowy. Dostosowywanie wartość uruchamiając operacje na zestawie danych i sprawdź wartość wyjściowa, aż do dopuszczalnych wartości progowej.

## <a name="anomaly-detection-algorithm"></a>Algorytm wykrywania anomalii

* AnomalyDetection operator używa **uczenie nienadzorowane** podejście, w którym nie przyjmuje żadnego typu dystrybucji w zdarzeniach. Ogólnie rzecz biorąc dwa modele są obsługiwane jednocześnie w dowolnym momencie, gdy jeden z nich służy do oceniania i innych jest uczony w tle. Modele wykrywania anomalii są uczone przy użyciu danych z bieżącego strumienia, a nie przy użyciu mechanizmu poza pasmem. Dane używane na potrzeby szkolenia zależy od d rozmiar okna, określony przez użytkownika w ramach parametr limitu czasu trwania. Każdy model kończy się pobieranie uczony w zależności od d 2d zdarzeń z długiego okresu. Zalecane jest ma co najmniej 50 zdarzenia każdego okna, aby uzyskać najlepsze wyniki. 

* AnomalyDetection operator używa **przedłużanie semantyki okna** do uczenia modeli i zdarzenia wynik. Co oznacza, że każdego zdarzenia jest sprawdzany pod kątem anomalii i wynik jest zwracany. Wynik jest wskaźnik poziomu zaufania z tym anomalii. 

* Zawiera AnomalyDetection operator **gwarancji powtarzalności** takie same dane wejściowe zawsze daje wynik sam, niezależnie od tego, dane wyjściowe zadania godzina rozpoczęcia. Czas rozpoczęcia dane wyjściowe zadania reprezentuje czasu, jaką pierwsze zdarzenie wyjściowe jest generowany przez zadanie. Jest ustawiony przez użytkownika w celu bieżący czas, wartość niestandardową lub czas ostatniego dane wyjściowe (Jeśli zadanie ma wcześniej utworzone dane wyjściowe). 

### <a name="training-the-models"></a>Uczenie modeli 

W miarę postępów czasu modele są uczone z wykorzystaniem różnych danych. Rozsądnie wynik, pomaga zrozumieć podstawowy mechanizm, za pomocą którego modele są uczone. W tym miejscu **t<sub>0</sub>**  jest **dane wyjściowe zadania godzina rozpoczęcia** i **d** jest **rozmiar okna** od długości Limit parametr. Załóżmy, że czas jest podzielony na **przeskoków d rozmiar**, rozpoczynając od `01/01/0001 00:00:00`. Następujące kroki służą do nauczenia modelu, a wynik zdarzenia:

* Podczas uruchamiania zadania odczytuje dane, zaczynając od czasu t<sub>0</sub> — 2W.  
* Gdy czas osiągnie następnego przeskoku, nowy model M1 jest tworzony i uruchamia pobieranie uczony.  
* Gdy czas przechodzi przez inny przeskoku, nowy model M2 jest tworzony i uruchamia pobieranie uczony.  
* Gdy czas osiągnie t<sub>0</sub>, M1 jest aktywowane i rozpoczyna jej wynik pobierania wyjściowych.  
* W następnym przeskoku trzy elementy odbywa się na tym samym czasie:  

  * M1 nie jest już potrzebne, i jest on odrzucony.  
  * M2 ma wystarczająco uczenia, dlatego służy do oceniania.  
  * Nowy model M3 jest tworzony i uruchamia pobieranie uczony w tle.  

* Ten cykl powtarza się dla każdego przeskoku w przypadku, gdy zostaną odrzucone aktywnego modelu, przełącz się do równoległych modelu i rozpocząć szkolenie trzecim modelu w tle. 

Schematycznie kroki wyglądać w następujący sposób: 

![Modele szkolenia](media/stream-analytics-machine-learning-anomaly-detection/training_model.png)

|**Model** | **Czas rozpoczęcia szkolenia** | **Czas, aby rozpocząć korzystanie z jego wynik** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* M1 modelu uruchamia szkolenie na 11:20:00, czyli następnego przeskoku po zadania rozpoczyna odczyt 11:13:00. Pierwsze dane wyjściowe jest tworzony z M1 na 11:33:00 po szkolenia 13 minut danych. 

* Nowy model M2 rozpoczyna się szkolenie na 11:30:00, ale jego wynik nie pobrać używane do 11:40:00, czyli po ma przeprowadzono uczenia z danych 10 minut. 

* M3 jest zgodny ze wzorcem samej jako M2. 

### <a name="scoring-with-the-models"></a>Ocenianie przy użyciu modeli 

Na poziomie usługi Machine Learning algorytm wykrywania anomalii oblicza wartość strangeness dla każdego zdarzenia przychodzące, porównując go z zdarzeń w oknie historii. Obliczenia strangeness jest różny dla każdego typu anomalii.  

Umożliwia przeglądanie obliczeń strangeness szczegółowo (przyjmowane jest, istnieje zestaw historycznych systemu Windows ze zdarzeniami): 

1. **Zmiany poziomu dwukierunkowe:** oparte na okno Historia, normalnego zakresu działania jest obliczana jako [10 percentyl, 90-procentowy] to znaczy 10 wartość percentylu jako wartość dolnej granicy i 90th jako górna granica. Wartość strangeness dla bieżącego zdarzenia jest obliczana jako:  

   - 0, jeśli event_value normalnego zakresu działania  
   - Jeśli event_value/90th_percentile event_value > 90th_percentile  
   - 10th_percentile/event_value, jeśli jest event_value < 10th_percentile  

2. **Powolne trend dodatnią:** linię trendu wartości zdarzeń w oknie historii jest obliczana i operacji sprawdza pozytywne trendu w wierszu. Wartość strangeness jest obliczana jako:  

   - Kąt nachylenia, jeśli nachylenie jest dodatnia  
   - 0, w przeciwnym razie 

3. **Powolne trend ujemna:** linię trendu wartości zdarzeń w oknie historii jest obliczana i operacji szuka ujemna trendu w wierszu. Wartość strangeness jest obliczana jako: 

   - Kąt nachylenia, jeśli nachylenie jest ujemna.  
   - 0, w przeciwnym razie  

Po jest obliczana wartość strangeness zdarzenia przychodzącego, wartość martingale jest obliczana na podstawie wartości strangeness (zobacz [blogu uczenia maszynowego](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) szczegółowe informacje na temat sposobu jest obliczana wartość martingale). Ta wartość martingale jest zwracany jako wynik anomalii. Wartość martingale zwiększa się powoli w odpowiedzi na wartości dziwne, co umożliwia detektora pozostaje niezawodne sporadyczne zmiany i zmniejsza fałszywe alerty. Ma ona również przydatne właściwości: 

Prawdopodobieństwo [istnieje t takie tego M<sub>t</sub> > λ] < 1/λ, gdzie M<sub>t</sub> -wartość martingale t błyskawiczne i λ jest rzeczywistą wartość. Na przykład, jeśli istnieje alert po M<sub>t</sub>> 100, a następnie prawdopodobieństwo fałszywych alarmów jest mniejsza niż 1-100.  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>Wskazówki dotyczące korzystania z poziomu dwukierunkowe zmienić detektora 

Wykrywacz zmiany poziomu dwukierunkowe służy w scenariuszach, takich jak power awarii i odzyskiwania lub godzina łazienkowych ruchu itp. Jednak działa w taki sposób, że po modelu są uczone z niektórych danych, inna zmiana poziomu nietypowych tylko wtedy, gdy nowa wartość jest wyższa niż poprzednie górny limit (podwyższeniu poziomu zmiana wielkości liter) lub niższa od poprzedniego dolny limit (w dół poziom Zmień wielkość liter). W związku z tym modelem nie powinien być widoczny wartości danych w zakresie nowy poziom (w górę lub w dół) w jego oknie szkoleń dla nich wziąć pod uwagę nietypowych. 

Korzystając z tego detektora, należy rozważyć następujące punkty: 

1. Gdy szeregów czasowych nagle widzi zwiększenia lub porzucić wartości, AnomalyDetection operator wykryje. Ale wykrywanie powrotu do normalnego wymaga więcej planowania. Jeśli szeregów czasowych była w stanie stabilności przed anomalii, które uzyskuje się przez ustawienie okna wykrywania AnomalyDetection operator co najwyżej połowa długości anomalii. W tym scenariuszu założono, że wcześniejsze można oszacować minimalny czas trwania anomalii i ma za mało zdarzeń w tym czasie do nauczenia modelu wystarczająco (co najmniej 50 zdarzeń). 

   Przedstawiono na rysunku 1 i 2 poniżej przy użyciu zmian górny limit (ta sama logika dotyczy niższy limit zmiany). W obu wartości kształty fali są nietypowe zmiany poziomu. Pionowych linii pomarańczowy oznaczenia granice przeskoku i rozmiar przeskoku jest taki sam, jak określono w operatorze AnomalyDetection okna wykrywania. Zielone linie wskazuje rozmiar okna szkolenia. Na rysunku 1 rozmiar przeskoku jest taka sama jak czasu, dla których okresu anomalii. Na rysunku 2 rozmiar przeskoku jest połowy czasu, dla których trwa anomalii. We wszystkich przypadkach wykryciu górę zmiany, ponieważ z modelem użytym do oceniania uczenia został na normalne danych. Ale w oparciu o sposób działania detektora zmiany poziomu dwukierunkowe, go wykluczyć wartości normalnych z okna szkolenia użyty dla modelu, który wyników na powrót do normalnego. Na rysunku 1 uczenia modelu oceniania obejmuje niektóre zdarzenia normalne, więc nie można wykryć powrotu do normalnego. Jednak na rysunku 2, szkolenia zawiera tylko nietypowych strony, która pozwala na wykrycie powrotu do normalnego. Mniejszego niż połowy działa także dla tego samego powodu, podczas gdy większe niczego zakończą się łącznie z bitowego normalne zdarzeń. 

   ![Usługi AD o długości anomalii taki sam rozmiar okna](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_anomaly_length.png)

   ![Usługi Active Directory z rozmiar okna jest równa połowie długości anomalii](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_half_anomaly_length.png)

2. W przypadkach, gdy nie można przewidzieć długość anomalii to detektora działa w najlepszym wysiłku. Jednak wybór mniejszą niż okno czasu ogranicza dane szkoleniowe, której może zwiększyć prawdopodobieństwo wykrycia powrotu do normalnego. 

3. W poniższym scenariuszu dłużej anomalii nie jest wykryte jako okno szkolenia zawiera już anomalii tego samego wysokiej wartości. 

   ![Anomalie o tym samym rozmiarze](media/stream-analytics-machine-learning-anomaly-detection/anomalies_with_same_length.png)

## <a name="example-query-to-detect-anomalies"></a>Przykładowe zapytanie do wykrywania anomalii 

Następujące zapytanie może służyć do wyjściowego alert w przypadku wykrycia anomalii.
Jeśli strumień wejściowy nie jest jednolite, krok agregacji może pomóc przekształcania serii jednolita postać czasu. W przykładzie użyto AVG, ale określony typ agregacji jest zależny od scenariusza użytkownika. Ponadto podczas szeregów czasowych ma luki większe niż okno agregacji, nie ma żadnych zdarzeń w szeregu czasowym z wykrywaniem anomalii wyzwalacza (zgodnie z harmonogramem przesuwanego okna semantyki). W związku z tym założeniu jednolitość jest przerywane po nadejściu następne zdarzenie. W takich sytuacjach luk w szeregu czasowym powinno być wypełnione. Jednym z podejść możliwe ma mieć ostatnie zdarzenie co okno przeskoku, jak pokazano poniżej.

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

## <a name="performance-guidance"></a>Wytyczne dotyczące wydajności

* Za pomocą sześciu jednostek przesyłania strumieniowego dla zadania. 
* Wysyłanie zdarzeń co najmniej jednej sekundy od siebie.
* Niepartycjonowany zapytanie, które używa operatora AnomalyDetection może wygenerować wyniki przeciętnie z opóźnieniem obliczeń około 25 ms.
* Opóźnienie doświadczają partycjonowanej zapytania nieco zależy od liczby partycji, jak liczba obliczenia jest większa. Jednak opóźnienie nastąpi taka sama jak w przypadku niepartycjonowany porównywalne łączna liczba zdarzeń wszystkich partycji.
* Podczas odczytywania danych z systemem innym niż czasu rzeczywistego, szybko jest pozyskanych dużej ilości danych. Przetwarzanie tych danych jest obecnie wolniej. Czas oczekiwania w takich scenariuszach znaleziono liniowo zwiększyć liczbę punktów danych w oknie zamiast interwał okna rozmiaru lub zdarzeń. Aby skrócić czas oczekiwania w przypadku się w czasie rzeczywistym, należy wziąć pod uwagę przy użyciu mniejszego rozmiaru okna. Alternatywnie należy rozważyć uruchomienie zadania od bieżącego czasu. Kilka przykładów opóźnienia w zapytaniu niepartycjonowany: 
    - 60 punktów danych w oknie wykrywania może spowodować opóźnienia 10 sekund o przepustowości 3 MB/s. 
    - 600 punktów danych opóźnienie może nawiązać połączenie o 80 sekund o przepustowości 0,4 MB/s.

## <a name="limitations-of-the-anomalydetection-operator"></a>Ograniczenia operatora AnomalyDetection 

* Ten operator nie obsługuje obecnie kolekcji i dip wykrywania. Wartości szczytowe i DIP są spontanicznej lub tej zmiany w szeregu czasowym. 

* Ten operator nie obsługuje obecnie wzorce sezonowości. Wzorce sezonowości są powtarzane wzorce w danych, na przykład zachowanie ruchu innej witryny sieci web podczas weekendów lub różnych trendów zakupów podczas czarne piątek, które nie są anomalii, ale oczekiwano wzorca w zachowaniu. 

* Ten operator oczekuje serii godzina jest jednolita. Strumień zdarzeń można ujednolicone przez agregowanie za pośrednictwem wirowania lub Skaczące okno. W scenariuszach, w którym odstęp między zdarzeniami zawsze jest mniejszy niż okno agregacji okno wirowania jest wystarczające, aby wprowadzić uniform szeregów czasowych. Gdy przerwa może być większy, luk może zostać wypełniony powtarzając ostatnią wartość przy użyciu okna przeskoku. 

## <a name="references"></a>Dokumentacja

* [Wykrywanie anomalii — Using machine learning w celu wykrycia nieprawidłowości w czasie serii danych](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [Wykrywanie anomalii interfejsu API uczenia maszynowego](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Wykrywanie anomalii serii czasu](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

