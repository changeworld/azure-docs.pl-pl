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
ms.openlocfilehash: 88c0aea851bcf70206b5f68d7865c487441905f6
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329896"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Wykrywanie anomalii w usłudze Azure Stream Analytics

Dostępne w chmurze i usługi Azure IoT Edge, Azure Stream Analytics oferuje wbudowane usługi machine learning funkcje wykrywania anomalii zależności, które mogą służyć do monitorowania dwóch anomalie najczęściej występujące: tymczasowe i trwałe. Za pomocą **AnomalyDetection_SpikeAndDip** i **AnomalyDetection_ChangePoint** funkcje, możesz przeprowadzać wykrywanie anomalii bezpośrednio w ramach zadania usługi Stream Analytics.

Modele uczenia maszynowego założono szeregów czasowych równomiernie próbkowane. Jeśli Szeregi czasowe nie jest jednolite, może wstawić etap agregacji za pomocą okna wirowania przed wywołaniem wykrywania anomalii.

Operacje uczenia maszyny nie obsługują sezonowości trendów lub wielu variate korelacji w tej chwili.

## <a name="model-accuracy-and-performance"></a>Dokładność modelu i wydajności

Ogólnie rzecz biorąc dokładności modelu zwiększa większej ilości danych w ramach przesuwającego się okna. Dane w określonym oknie przewijania jest traktowany jako część jej normalny zakres wartości dla tego okresu. Model analizuje tylko migracji historii zdarzeń przesuwającego się okna do Sprawdź, czy bieżące zdarzenie jest nieprawidłowe. Przemieszcza się w ramach przesuwającego się okna, stare wartości jest wykluczony z szkoleń modelowych.

Funkcje działają, ustanawiając niektórych zwykłym oparte na co ich zauważono pory. Elementy odstające są identyfikowane przez porównanie ustanowionych normalna, w ramach poziom ufności. Rozmiar okna powinna być oparta na zdarzeniach minimalne wymagane do nauczenia modelu do normalnego zachowania, tak aby w przypadku wystąpienia anomalii byłaby w stanie rozpoznać.

Model czas odpowiedzi zwiększa się z rozmiarem historii, ponieważ musi zostać porównane większa liczba przeszłych zdarzeń. Zalecane jest obejmujący tylko niezbędne liczbę zdarzeń w celu zapewnienia lepszej wydajności.

Luki w szeregu czasowym może być to wynikiem modelu nie odbiera zdarzenia w określonych punktach w czasie. Ta sytuacja jest obsługiwane przez usługę Stream Analytics przy użyciu przypisywania logiki. Rozmiar historii, a także czas trwania dla tego samego okna przewijania jest używane do obliczania średnia liczba zdarzeń powinny przybycie.

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

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Wykrywanie anomalii w usłudze Azure Stream Analytics przy użyciu usługi machine learning

Poniższy klip wideo pokazuje, jak i wykrywania anomalii w czasie rzeczywistym za pomocą funkcji uczenia maszyny w usłudze Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

