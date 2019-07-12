---
title: Wykrywanie anomalii w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób korzystania ze sobą usługi Azure Stream Analytics i Azure Machine Learning do wykrywania anomalii.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: e2fd226f1c605821f0fd595832b2cbe26d994fb4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612333"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Wykrywanie anomalii w usłudze Azure Stream Analytics

Dostępne w chmurze i usługi Azure IoT Edge, Azure Stream Analytics oferuje wbudowane usługi machine learning funkcje wykrywania anomalii zależności, które mogą służyć do monitorowania dwóch anomalie najczęściej występujące: tymczasowe i trwałe. Za pomocą **AnomalyDetection_SpikeAndDip** i **AnomalyDetection_ChangePoint** funkcje, możesz przeprowadzać wykrywanie anomalii bezpośrednio w ramach zadania usługi Stream Analytics.

Modele uczenia maszynowego założono szeregów czasowych równomiernie próbkowane. Jeśli Szeregi czasowe nie jest jednolite, może wstawić etap agregacji za pomocą okna wirowania przed wywołaniem wykrywania anomalii.

Operacje uczenia maszyny nie obsługują sezonowości trendów lub wielu variate korelacji w tej chwili.

## <a name="model-behavior"></a>Zachowania modelu

Ogólnie rzecz biorąc dokładności modelu zwiększa większej ilości danych w ramach przesuwającego się okna. Dane w określonym oknie przewijania jest traktowany jako część jej normalny zakres wartości dla tego okresu. Model analizuje tylko migracji historii zdarzeń przesuwającego się okna do Sprawdź, czy bieżące zdarzenie jest nieprawidłowe. Przemieszcza się w ramach przesuwającego się okna, stare wartości jest wykluczony z szkoleń modelowych.

Funkcje działają, ustanawiając niektórych zwykłym oparte na co ich zauważono pory. Elementy odstające są identyfikowane przez porównanie ustanowionych normalna, w ramach poziom ufności. Rozmiar okna powinna być oparta na zdarzeniach minimalne wymagane do nauczenia modelu do normalnego zachowania, tak aby w przypadku wystąpienia anomalii byłaby w stanie rozpoznać.

Model czas odpowiedzi zwiększa się z rozmiarem historii, ponieważ musi zostać porównane większa liczba przeszłych zdarzeń. Zalecane jest obejmujący tylko niezbędne liczbę zdarzeń w celu zapewnienia lepszej wydajności.

Luki w szeregu czasowym może być to wynikiem modelu nie odbiera zdarzenia w określonych punktach w czasie. Ta sytuacja jest obsługiwane przez usługę Stream Analytics przy użyciu przypisywania logiki. Rozmiar historii, a także czas trwania dla tego samego okna przewijania jest używane do obliczania średnia liczba zdarzeń powinny przybycie.

Generatora anomalii [tutaj](https://aka.ms/asaanomalygenerator) może służyć do kanału informacyjnego usługi Iot Hub przy użyciu danych za pomocą różnych anomalii wzorców. Zadania usługi ASA można skonfigurować za pomocą tych funkcji wykrywania anomalii do odczytu z tej usługi Iot Hub i wykrywania anomalii.

## <a name="spike-and-dip"></a>Kolekcji i dip

Tymczasowe anomalie w strumieniu zdarzeń serie czasu są znane jako gwałtowne wzrosty i spadki. Wzrostów i spadków można monitorować za pomocą operatora usługi Machine Learning na podstawie [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Przykład kolekcji i dip anomalii](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

W tym samym metodzie przesuwanego okna, jeśli drugi kolekcji jest mniejszy niż pierwszy z nich, obliczanych wyników dla mniejszych kolekcji prawdopodobnie nie ma znaczenia wystarczająco dużo, w porównaniu do wyników dla pierwszej kolekcji w ramach poziom ufności określony. Możesz wypróbować, zmniejszyć poziom ufności modelu do wykrywania anomalii takie. Jednak w przypadku uruchomienia uzyskać zbyt wiele alertów, można użyć nowszej interwał ufności.

Poniższe przykładowe zapytanie zakłada jednolitą stawkę wejściowych jedno zdarzenie na sekundę w 2-minutowy przesuwającego się okna z historią 120 zdarzeń. Końcowe instrukcji SELECT wyodrębnia i wyświetla wynik i anomalii stanu z poziomu ufności 95%.

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

Trwałe anomalie w strumieniu zdarzeń serii czasu są zmiany w rozkładu wartości w strumieniu zdarzeń, takich jak zmiany poziomu i trendów. W usłudze Stream Analytics, takie anomalie są wykrywane przy użyciu uczenia maszynowego na podstawie [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) operatora.

Trwałe zmiany trwać znacznie dłużej, niż gwałtowne wzrosty i spadki i wskazywać, że zdarzenia krytycznego. Trwałe zmiany nie są zwykle widoczne gołym okiem, ale może zostać wykryte za pomocą **AnomalyDetection_ChangePoint** operatora.

Poniższy rysunek jest przykładem zmiany poziomu:

![Przykład zmiany poziomu anomalii](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

Poniższej ilustracji przedstawiono przykładowy zmiany trendów:

![Przykład trend zmiany anomalii](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

Poniższe przykładowe zapytanie zakłada jednolitą stawkę wejściowych jedno zdarzenie na sekundę w przesuwającego się okna 20 minut z rozmiarem historii zdarzeń 1200. Końcowe instrukcji SELECT wyodrębnia i wyświetla wynik i anomalii stanu z poziomu ufności 80%.

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

## <a name="performance-characteristics"></a>Charakterystyki wydajności

Wydajność tych modeli zależy od rozmiaru historii, czas trwania okna, obciążenie zdarzeniami i tego, czy funkcja poziomu Partycjonowanie jest używany. W tej sekcji omówiono te konfiguracje i przykłady dotyczące umożliwienia stawek pozyskiwania 1K i 5K 10 tys. zdarzeń na sekundę.

* **Rozmiar historii** -liniowo wykonywanie tych modeli **rozmiar historii**. Im dłużej rozmiar historii, tym dłużej modele wykonać, aby oceniać nowe zdarzenie. Jest to spowodowane modele Porównaj nowe zdarzenie z każdą z przeszłych zdarzeń w buforze historii.
* **Czas trwania okna** — **czas trwania okna** powinny odzwierciedlać, jak długo trwa tyle zdarzenia są rejestrowane jako określony przez rozmiar historii. Bez wybranej liczby zdarzeń w oknie Azure Stream Analytics spowoduje naliczenie brakujące wartości. W związku z tym użycie procesora CPU jest funkcją wielkości historii.
* **Obciążenie zdarzeniami** — większa **obciążenie zdarzeniami**, tym więcej pracy, jest wykonywane przez modele, które ma wpływ na użycie procesora CPU. Zadanie może być skalowana, dzięki czemu zaskakująco równoległymi, przy założeniu, że największy sens dla logikę biznesową w celu używania więcej partycji danych wejściowych.
* **Funkcja poziomu partycjonowanie** - **partycjonowania na poziomie funkcji** odbywa się przy użyciu ```PARTITION BY``` w wywołaniu funkcji wykrywania anomalii. Tego rodzaju partycjonowanie dodaje obciążenie, ponieważ stan musi być zachowana dla wielu modeli, w tym samym czasie. Partycjonowania na poziomie funkcji jest używany w scenariuszach, takich jak partycjonowania na poziomie urządzenia.

### <a name="relationship"></a>Relacja
Rozmiar historii, czas trwania okna i łączna liczba zdarzeń obciążenia są powiązane w następujący sposób:

windowDuration (w ms) = 1000 * historySize / (całkowita liczba danych wejściowych zdarzeń na s / opinia nabrała partycji)

Podczas partycjonowania funkcji przez deviceId należy dodać "PARTYCJI przez deviceId", aby wywołanie funkcji wykrywania anomalii.

### <a name="observations"></a>Uwagi
Poniższa tabela zawiera uwagi przepływności dla pojedynczego węzła (6 SU) w przypadku niepartycjonowana:

| Rozmiar historii (zdarzenia) | Czas trwania okna (ms) | Łączna liczba zdarzeń wejściowych na sekundę |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6,000 | 10,910 | 1,100 |

Poniższa tabela zawiera uwagi przepływności dla pojedynczego węzła (6 SU) w przypadku partycjonowanego:

| Rozmiar historii (zdarzenia) | Czas trwania okna (ms) | Łączna liczba zdarzeń wejściowych na sekundę | Liczba urządzeń |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6,000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6,000 | 2,181,819 | <550 | 100 |

Przykładowy kod służący do uruchomionych konfiguracjach niepartycjonowana powyżej znajduje się w [repozytorium przesyłania strumieniowego w skali](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) z przykładów dla platformy Azure. Ten kod tworzy zadanie usługi stream analytics za pomocą nie funkcji poziomu partycjonowania, który korzysta z Centrum zdarzeń jako dane wejściowe i wyjściowe. Ładowanie danych wejściowych jest generowana z użyciem klientów testowych. Każde zdarzenie w danych wejściowych to dokument json 1KB. Zdarzenia symulowanie urządzenia IoT wysyłają dane JSON (dla urządzeń z maksymalnie 1 K). Rozmiar historii, czas trwania okna i łączna liczba zdarzeń obciążenia są zróżnicowane na 2 partycjach danych wejściowych.

> [!Note]
> Bardziej precyzyjne Kwota szacunkowa można dostosować w przykłady odpowiednio do scenariusza.

### <a name="identifying-bottlenecks"></a>Identyfikowania wąskich gardeł
Okienko metryki w ramach zadania usługi Azure Stream Analytics służy do identyfikowania wąskich gardeł w potoku. Przegląd **zdarzenia wejścia/wyjścia** przepływności i ["Opóźnienie znaku wodnego"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) lub **zaległe zdarzenia** aby zobaczyć, jeśli zadanie jest Nadążanie za szybkość danych wejściowych. Centrum zdarzeń miar, poszukaj **ograniczenia żądań** i odpowiednio dostosować próg jednostki. Metryki usługi Cosmos DB można znaleźć **maksymalna liczba użytych jednostek RU/s na zakres kluczy partycji** równomiernie są używane w ramach przepływności, aby zapewnić zakresów kluczy partycji. W przypadku bazy danych SQL Azure, monitorować **we/wy dziennika** i **Procesora**.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Wykrywanie anomalii w usłudze Azure Stream Analytics przy użyciu usługi machine learning

Poniższy klip wideo pokazuje, jak i wykrywania anomalii w czasie rzeczywistym za pomocą funkcji uczenia maszyny w usłudze Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

