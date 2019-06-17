---
title: Oparte na danych debugowania w usłudze Azure Stream Analytics
description: W tym artykule opisano, jak rozwiązywać problemy z zadania usługi Azure Stream Analytics za pomocą diagramu zadania i metryki w witrynie Azure portal.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 4a6d359b27b9a2e52d71ed5f8547041645147605
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61479970"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Oparte na danych debugowanie za pomocą diagramu zadania

Diagram zadań na **monitorowanie** bloku w witrynie Azure portal może pomóc w wizualizacji potok zadania. Pokazuje wejścia, wyjścia i kroki zapytania. Za pomocą diagramu zadań można sprawdzać metryki dla poszczególnych kroków, aby szybciej ustalić źródło problemu podczas rozwiązywania problemów.

## <a name="using-the-job-diagram"></a>Za pomocą diagramu zadania

W witrynie Azure portal podczas gdy w ramach zadania usługi Stream Analytics w obszarze **pomoc techniczna i rozwiązywanie problemów**, wybierz opcję **diagram zadania**:

![Diagram zadań za pomocą metryk - lokalizacji](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Zaznacz każdy krok zapytania, aby zobaczyć, do odpowiedniej sekcji w zapytaniu do edycji okienko. Wykres metryk dla kroku jest wyświetlany w dolnym okienku na stronie.

![Diagram zadań za pomocą metryk - podstawowe zadania](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Aby wyświetlić partycji danych wejściowych usługi Azure Event Hubs, wybierz **...** Zostanie wyświetlone menu kontekstowe. Widać również połączenia danych wejściowych.

![Diagram zadań za pomocą metryk - rozwiń węzeł partycji](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Aby wyświetlić wykresu metryki dla jednej partycji, wybierz węzeł partycji. Metryki są wyświetlane w dolnej części strony.

![Diagram zadań z metrykami — więcej metryk](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Aby wyświetlić wykres metryk, aby połączenie, wybierz węzeł połączenia. Na poniższym wykresie przedstawiono, że zdarzenia nie zostały porzucone lub dopasowane.

![Diagram zadań za pomocą metryk - siatki](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Aby wyświetlić szczegóły wartość metryki i czasu, wskaż polecenie wykresu.

![Zadania diagramu z metrykami — Zatrzymaj wskaźnik myszy](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Rozwiązywanie problemów przy użyciu metryk

**QueryLastProcessedTime** Metryka wskazuje, kiedy określonego kroku Odebrano dane. Patrząc topologii, możesz podjąć wstecz z przetwarzania danych wyjściowych, aby zobaczyć, który krok nie odbiera danych. Jeśli krok nie uzyskuje dane, przejdź do kroku zapytania tuż przed. Sprawdź, czy poprzedni krok zapytania ma przedział czasu, a jeśli minęło wystarczająco dużo czasu na jego dane wyjściowe. (Uwaga czasu systemu windows są wyrównywane do godziny.)
 
Jeśli w poprzednim kroku zapytania danych wejściowych, użyj metryk wejściowych, mogą ułatwić uzyskanie odpowiedzi następujące pytania. One może pomóc w określeniu, czy zadanie jest pobierających dane ze swoich źródeł wejściowych. Jeśli zapytanie jest podzielone na partycje, sprawdź każdą partycję.
 
### <a name="how-much-data-is-being-read"></a>Jak dużo danych jest odczytywanych?

*   **InputEventsSourcesTotal** jest to liczba jednostek danych odczytu. Na przykład liczbę obiektów blob.
*   **InputEventsTotal** jest liczbę odczytanych zdarzeń. Ta metryka jest dostępna dla każdej partycji.
*   **InputEventsInBytesTotal** jest liczba odczytanych bajtów.
*   **InputEventsLastArrivalTime** jest aktualizowana co odebranego zdarzenia.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Czy czas płynie do przodu? Jeśli są odczytywane rzeczywiste zdarzenia, wyróżnienie może nie być wystawiane.

*   Metryka **InputEventsLastPunctuationTime** wskazuje, kiedy wyróżnienie zostało wystawione, aby czas płynął do przodu. Jeśli wyróżnienie nie zostało wystawione, przepływ danych może zostać zablokowany.
 
### <a name="are-there-any-errors-in-the-input"></a>Czy istnieją błędy w danych wejściowych?

*   **InputEventsEventDataNullTotal** jest liczbę zdarzeń, które zawierają dane o wartości null.
*   **InputEventsSerializerErrorsTotal** jest liczba zdarzeń, które mogły nie zostać poprawnie zdeserializowane.
*   **InputEventsDegradedTotal** jest liczba zdarzeń, których problem innych niż z deserializacji.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Czy zdarzenia są porzucone lub dopasowane?

*   **InputEventsEarlyTotal** jest liczbę zdarzeń, które mają sygnaturą czasową aplikacji wcześniejszą górnego limitu.
*   **InputEventsLateTotal** jest liczba zdarzeń, które mają sygnaturą czasową aplikacji po górnego limitu.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** jest liczbę zdarzeń porzuconych przed czas rozpoczęcia zadania.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Firma Microsoft malejących podczas odczytywania danych?

*   **Dane wejściowe zdarzenia zaległe (łącznie)** informujący o tym, ile komunikatów więcej muszą zostać odczytana dla danych wejściowych usługi Event Hubs i Azure IoT Hub. Jeśli ta liczba jest większa niż 0, oznacza to, że zadania nie może przetworzyć dane bezpośrednio po ich otrzymaniu. W takim przypadku może być konieczne zwiększenie liczby jednostek przesyłania strumieniowego i/lub upewnij się, że zadanie może być przetwarzane równolegle. Więcej informacji na ten może zobaczyć na [strona przetwarzania równoległego zapytań](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). 


## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Stream Analytics](stream-analytics-introduction.md)
* [Rozpoczynanie pracy z usługą Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalowanie zadań usługi Stream Analytics](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytań usługi Analytics Stream](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics management dokumentacja interfejsu API REST](https://msdn.microsoft.com/library/azure/dn835031.aspx)
