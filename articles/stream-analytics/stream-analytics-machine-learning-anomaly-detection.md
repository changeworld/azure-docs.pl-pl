---
title: Wykrywanie anomalii w usłudze Azure Stream Analytics
description: W tym artykule opisano, jak używać usługi Azure Stream Analytics i usługi Azure Machine Learning razem do wykrywania anomalii.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 51b9c827d453eef2e2e75e1aa5222204eaa38d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525536"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Wykrywanie anomalii w usłudze Azure Stream Analytics

Usługa Azure Stream Analytics, dostępna zarówno w chmurze, jak i w usłudze Azure IoT Edge, oferuje wbudowane funkcje wykrywania anomalii oparte na uczeniu maszynowym, które mogą służyć do monitorowania dwóch najczęściej występujących anomalii: tymczasowych i trwałych. Dzięki **funkcjom AnomalyDetection_SpikeAndDip** i **AnomalyDetection_ChangePoint** możesz wykrywać anomalie bezpośrednio w zadaniu usługi Stream Analytics.

Modele uczenia maszynowego zakładają jednolicie próbkowanym szeregom czasowym. Jeśli szeregi czasowe nie jest jednolite, można wstawić krok agregacji z oknem załamaniem przed wywołaniem wykrywania anomalii.

Operacje uczenia maszynowego nie obsługują trendów sezonowości ani korelacji z wieloma zmiennymi w tej chwili.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Wykrywanie anomalii przy użyciu uczenia maszynowego w usłudze Azure Stream Analytics

W poniższym klipie wideo pokazano, jak wykryć anomalię w czasie rzeczywistym przy użyciu funkcji uczenia maszynowego w usłudze Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Zachowanie modelu

Ogólnie rzecz biorąc dokładność modelu poprawia się z większą licznymi danymi w oknie przesuwnym. Dane w określonym oknie przesuwnym są traktowane jako część normalnego zakresu wartości dla tego przedziału czasowego. Model uwzględnia tylko historię zdarzeń w oknie przesuwnym, aby sprawdzić, czy bieżące zdarzenie jest nietypowe. W miarę przesuwania się okna przesuwnego stare wartości są eksmitowane ze szkolenia modelu.

Funkcje działają poprzez ustanowienie pewnego normalności w oparciu o to, co widzieli do tej pory. Wartości odstające są identyfikowane przez porównanie z ustaloną normalną, w ramach poziomu ufności. Rozmiar okna powinny być oparte na minimalnych zdarzeń wymaganych do szkolenia modelu dla normalnego zachowania, tak aby w przypadku wystąpienia anomalii, będzie w stanie go rozpoznać.

Czas odpowiedzi modelu zwiększa się wraz z rozmiarem historii, ponieważ musi być porównywany z większą liczbą przeszłych zdarzeń. Zaleca się uwzględnienie tylko liczby zdarzeń niezbędnych do uzyskania lepszej wydajności.

Luki w szeregach czasowych może być wynikiem modelu nie odbieranie zdarzeń w niektórych punktach w czasie. Ta sytuacja jest obsługiwana przez usługi Stream Analytics przy użyciu logiki imputacji. Rozmiar historii, a także czas trwania dla tego samego okna przesuwnego jest używany do obliczania średniej szybkości, z jaką zdarzenia mają nadejść.

Generator anomalii dostępny [w tym miejscu](https://aka.ms/asaanomalygenerator) może służyć do podawania centrum Iot z danymi z różnymi wzorcami anomalii. Zadanie ASA można skonfigurować za pomocą tych funkcji wykrywania anomalii do odczytu z tego centrum Iot i wykrywania anomalii.

## <a name="spike-and-dip"></a>Kolec i dip

Tymczasowe anomalie w strumieniu zdarzeń szeregów czasowych są nazywane skokami i spadkami. Skoki i spadki mogą być monitorowane za pomocą operatora opartego na uczeniu [maszynowym, AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Przykład anomalii skoków i dipów](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

W tym samym oknie przesuwnym, jeśli drugi skok jest mniejszy niż pierwszy, obliczony wynik dla mniejszego skoku prawdopodobnie nie jest wystarczająco znaczący w porównaniu do wyniku dla pierwszego skoku w ramach określonego poziomu ufności. Można spróbować zmniejszyć poziom ufności modelu, aby wykryć takie anomalie. Jeśli jednak zaczniesz otrzymywać zbyt wiele alertów, możesz użyć wyższego przedziału ufności.

Poniższa przykładowa kwerenda zakłada jednolitą szybkość wprowadzania jednego zdarzenia na sekundę w 2-minutowym oknie przesuwnym z historią 120 zdarzeń. Końcowa instrukcja SELECT wyodrębnia i wyprowadza wynik i stan anomalii z poziomem ufności 95%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Zmień punkt

Trwałe anomalie w strumieniu zdarzeń szeregów czasowych są zmiany w dystrybucji wartości w strumieniu zdarzeń, takich jak zmiany poziomu i trendy. W usłudze Stream Analytics takie anomalie są wykrywane przy użyciu operatora opartego na uczeniu maszynowym [AnomalyDetection_ChangePoint.](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics)

Trwałe zmiany trwają znacznie dłużej niż skoki i spadki i mogą wskazywać na katastrofalne zdarzenia. Trwałe zmiany zwykle nie są widoczne gołym okiem, ale można je wykryć za pomocą **operatora AnomalyDetection_ChangePoint.**

Poniższy obraz jest przykładem zmiany poziomu:

![Przykład anomalii zmiany poziomu](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

Poniższy obraz jest przykładem zmiany trendu:

![Przykład anomalii zmiany trendu](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

Poniższa przykładowa kwerenda zakłada jednolitą szybkość wprowadzania jednego zdarzenia na sekundę w 20-minutowym oknie przesuwnym o rozmiarze historii 1200 zdarzeń. Końcowa instrukcja SELECT wyodrębnia i wyprowadza wynik i stan anomalii z poziomem ufności 80%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>Charakterystyka wydajności

Wydajność tych modeli zależy od rozmiaru historii, czasu trwania okna, obciążenia zdarzeń i czy partycjonowanie poziomu funkcji jest używane. W tej sekcji omówiono te konfiguracje i przedstawiono przykłady dotyczące sposobu obsługi szybkości pozyskiwania zdarzeń 1K, 5K i 10K na sekundę.

* **Rozmiar historii** — te modele działają liniowo z **rozmiarem historii**. Im dłuższy rozmiar historii, tym dłużej modele zdobywają nowe wydarzenie. Jest tak, ponieważ modele porównać nowe zdarzenie z każdym z ostatnich zdarzeń w buforze historii.
* **Czas trwania** **okna** — czas trwania okna powinien odzwierciedlać czas potrzebny do odbierania tyle zdarzeń, ile określono w rozmiarze historii. Bez tego wiele zdarzeń w oknie usługi Azure Stream Analytics będzie przypisywać brakujące wartości. W związku z tym zużycie procesora JEST funkcją rozmiaru historii.
* **Obciążenie zdarzeń** — im większe **obciążenie zdarzenia,** tym więcej pracy jest wykonywane przez modele, co wpływa na zużycie procesora CPU. Zadanie można skalować w poziomie, czyniąc go żenująco równolegle, zakładając, że ma sens dla logiki biznesowej, aby użyć więcej partycji wejściowych.
* **Partycjonowanie** - poziomu funkcji**Partycjonowanie** ```PARTITION BY``` poziomu funkcji odbywa się za pomocą wywołania funkcji wykrywania anomalii. Ten typ partycjonowania dodaje obciążenie, ponieważ stan musi być utrzymywany dla wielu modeli w tym samym czasie. Partycjonowanie na poziomie funkcji jest używane w scenariuszach, takich jak partycjonowanie na poziomie urządzenia.

### <a name="relationship"></a>Relacja
Rozmiar historii, czas trwania okna i całkowite obciążenie zdarzeń są powiązane w następujący sposób:

windowDuration (w ms) = 1000 * historySize / (Całkowita liczba zdarzeń wejściowych na sekundę / Liczba partycji wejściowych)

Podczas partycjonowania funkcji według identyfikatora device, dodaj "PARTITION BY deviceId" do wywołania funkcji wykrywania anomalii.

### <a name="observations"></a>Obserwacje
Poniższa tabela zawiera obserwacje przepływności dla pojedynczego węzła (6 SU) dla przypadku nieobeprzejmowanego:

| Rozmiar historii (wydarzenia) | Czas trwania okna (ms) | Całkowita liczba zdarzeń wejściowych na sekundę |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2200 |
| 600 | 728 | 1,650 |
| 6000 | 10,910 | 1100 |

Poniższa tabela zawiera obserwacje przepływności dla pojedynczego węzła (6 SU) dla przypadku podzielonego na partycje:

| Rozmiar historii (wydarzenia) | Czas trwania okna (ms) | Całkowita liczba zdarzeń wejściowych na sekundę | Liczba urządzeń |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1100 | 10 |
| 600 | 10,910 | 1100 | 10 |
| 6000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6000 | 2,181,819 | <550 | 100 |

Przykładowy kod do uruchamiania konfiguracji nie podzielonych na partycje powyżej znajduje się w [repozytorium przesyłania strumieniowego w skali](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) przykładów platformy Azure. Kod tworzy zadanie analizy strumienia bez partycjonowania na poziomie funkcji, który używa Centrum zdarzeń jako dane wejściowe i wyjściowe. Obciążenie wejściowe jest generowane przy użyciu klientów testowych. Każde zdarzenie wejściowe jest dokumentem json 1KB. Zdarzenia symulują urządzenie IoT wysyłające dane JSON (dla maksymalnie 1K urządzeń). Rozmiar historii, czas trwania okna i całkowite obciążenie zdarzeń są zróżnicowane na 2 partycje wejściowe.

> [!Note]
> Aby uzyskać dokładniejsze oszacowanie, dostosuj przykłady, aby pasowały do scenariusza.

### <a name="identifying-bottlenecks"></a>Identyfikowanie wąskich gardeł
Użyj okienka Metryki w zadaniu usługi Azure Stream Analytics, aby zidentyfikować wąskie gardła w potoku. Przejrzyj **zdarzenia wejściowe/wyjściowe** dla przepływności i ["Opóźnienie znaku wodnego"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) lub **zdarzenia zaległe,** aby sprawdzić, czy zadanie nadąża za szybkością wprowadzania. W przypadku metryk Usługi zdarzeń poszukaj **żądań ograniczonego** i odpowiednio dostosuj jednostki progowe. W przypadku metryk usługi Cosmos DB, przejrzyj **maksymalną ilość zużytych ru/s na zakres klucza partycji** w obszarze Przepływność, aby upewnić się, że zakresy kluczy partycji są jednolicie używane. W przypadku usługi Azure SQL DB należy monitorować **we/wy** dziennika i **procesora .**

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

