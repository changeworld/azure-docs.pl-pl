---
title: Usługa Azure Stream Analytics opartych na danych debugowanie przy użyciu diagramu zadania | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z zadania Stream Analytics przy użyciu diagramu zadania i metryki.
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 05/01/2017
ms.author: jeanb
ms.openlocfilehash: 65eeeee7daa22b94074f55defdfd1219049774c9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Oparte na danych debugowanie przy użyciu diagramu zadania

Diagram zadania na **monitorowanie** bloku w portalu Azure ułatwiają wizualizowanie planowaną zadania. Przedstawia on wejść, wyjść i kroki zapytań. Diagram zadania służy do sprawdzenia metryki dla każdego kroku, aby szybciej wyizolować źródła problemu podczas rozwiązywania problemów.

## <a name="using-the-job-diagram"></a>Przy użyciu diagramu zadania

W portalu Azure podczas w zadaniu Stream Analytics, w obszarze **pomocy technicznej i rozwiązywania problemów**, wybierz pozycję **diagram zadania**:

![Diagram zadania z metryki - lokalizacji](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Wybierz każdego kroku zapytania, aby zobaczyć do odpowiedniej sekcji w zapytaniu edycji okienka. Wykres metryki dla kroku jest wyświetlany w dolnym okienku na tej stronie.

![Diagram zadania z metryki — podstawowe zadania](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Aby wyświetlić partycji dla danych wejściowych Azure Event Hubs, wybierz **...** Zostanie wyświetlone menu kontekstowego. Można również sprawdzić połączenie wejściowego.

![Diagram zadania z metryki - rozwiń partycji](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Aby wyświetlić na wykresie metryki dla tylko jednej partycji, wybierz węzeł partycji. Metryki są wyświetlane w dolnej części strony.

![Diagram zadania z metryki - więcej metryk](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Aby wyświetlić na wykresie metryki dotyczące łączenia, wybierz węzeł połączenia. W poniższej tabeli przedstawiono, że żadne zdarzenia nie zostały porzucone lub dostosowana.

![Diagram zadania z metryki - siatki](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Aby wyświetlić szczegóły wartość metryki i czasu, wskaż polecenie wykresu.

![Diagram z metryki zadania — wskaźnik myszy](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Rozwiązywanie problemów przy użyciu metryk

**QueryLastProcessedTime** Metryka wskazuje podczas odbierania danych w określonym kroku. Analizując topologii, można pracować wstecz od procesora danych wyjściowych, aby zobaczyć, który krok nie odbiera danych. Jeśli krok nie jest pobieranie danych, przejdź do kroku zapytania bezpośrednio przed nią. Sprawdź, czy poprzedni krok zapytania ma przedział czasu, a jeśli upłynęło dostatecznie dużo czasu na jego dane wyjściowe. (Uwaga tego czasu systemu windows są przyciągane do godziny.)
 
Jeśli poprzedni krok zapytania procesora wprowadzania, użyj metryk wejściowych przesyłający następujące pytania docelowych. One może pomóc w określeniu, czy zadanie jest pobieranie danych z jego źródeł danych wejściowych. Jeśli zapytanie jest podzielone na partycje, sprawdź każdą partycję.
 
### <a name="how-much-data-is-being-read"></a>Trwa odczytywanie ilość danych?

*   **InputEventsSourcesTotal** jest liczba jednostek danych do odczytu. Na przykład liczbę obiektów blob.
*   **InputEventsTotal** jest liczba zdarzeń do odczytu. Ta metryka jest dostępna dla każdej partycji.
*   **InputEventsInBytesTotal** jest to liczba bajtów odczytanych.
*   **InputEventsLastArrivalTime** został zaktualizowany o czasie umieszczonych w kolejce co otrzymane zdarzenie.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Czas jest przenoszona do przodu? Jeśli rzeczywiste zdarzenia są odczytywane, znaki interpunkcyjne nie może zostać wygenerowany.

*   Metryka **InputEventsLastPunctuationTime** wskazuje, kiedy wyróżnienie zostało wystawione, aby czas płynął do przodu. Jeśli nie jest wystawiany znaki interpunkcyjne, mogą zablokowane przepływu danych.
 
### <a name="are-there-any-errors-in-the-input"></a>Istnieją błędy w danych wejściowych?

*   **InputEventsEventDataNullTotal** to liczba zdarzeń, które zawierają dane wartości null.
*   **InputEventsSerializerErrorsTotal** to liczba zdarzeń, które może nie być prawidłowo zdeserializowana.
*   **InputEventsDegradedTotal** to liczba zdarzeń, które wystąpiły problemy innych niż z deserializacji.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Są zdarzenia porzucone lub dostosować?

*   **InputEventsEarlyTotal** jest liczba zdarzeń, które mają sygnatura czasowa aplikacji przed górnego limitu.
*   **InputEventsLateTotal** jest liczba zdarzeń, które mają sygnatura czasowa aplikacji po górnego limitu.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** jest numer zdarzenia usunięta, zanim godzinę rozpoczęcia zadania.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Czy możemy objętych odczytywanie danych?

*   **InputEventsSourcesBackloggedTotal** informuje, ile więcej wiadomości muszą być odczytane dane wejściowe centra zdarzeń i Centrum IoT Azure.


## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi analiza strumienia](stream-analytics-introduction.md)
* [Wprowadzenie do usługi analiza strumienia](stream-analytics-real-time-fraud-detection.md)
* [Zadania usługi analiza strumienia skali](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytania usługi analiza strumienia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Strumienia Analytics management REST API reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
