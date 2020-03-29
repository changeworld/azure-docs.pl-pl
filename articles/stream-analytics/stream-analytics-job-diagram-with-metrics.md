---
title: Debugowanie oparte na danych w usłudze Azure Stream Analytics
description: W tym artykule opisano, jak rozwiązywać problemy z zadaniem usługi Azure Stream Analytics przy użyciu diagramu zadań i metryk w witrynie Azure portal.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 3c0c29e1793e56efae8d13cb01d57faf257d8805
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426071"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Data-driven debugging by using the job diagram (Debugowanie oparte na danych za pomocą diagramu zadań)

Diagram zadań w bloku **monitorowania** w witrynie Azure portal może pomóc wizualizować potok zadań. Pokazuje wejścia, wyjścia i kroki zapytania. Za pomocą diagramu zadań można sprawdzać metryki dla poszczególnych kroków, aby szybciej ustalić źródło problemu podczas rozwiązywania problemów.

## <a name="using-the-job-diagram"></a>Korzystanie z diagramu zadań

W witrynie Azure portal, podczas gdy w zadaniu usługi Stream Analytics, w obszarze **OBSŁUGA + ROZWIĄZYWANIE PROBLEMÓW**, wybierz diagram **zadań:**

![Diagram zadań z metrykami - lokalizacja](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Zaznacz każdy krok kwerendy, aby wyświetlić odpowiednią sekcję w okienku edycji kwerendy. Wykres metryczny dla tego kroku jest wyświetlany w dolnym okienku na stronie.

![Diagram zadań z metrykami - zadanie podstawowe](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Aby wyświetlić partycje danych wejściowych usługi Azure Event Hubs, wybierz **opcję . . .** Zostanie wyświetlene menu kontekstowe. Można również zobaczyć połączenie wejściowe.

![Diagram zadań z metrykami — rozwiń partycję](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Aby wyświetlić wykres metryki tylko dla jednej partycji, wybierz węzeł partycji. Dane są wyświetlane u dołu strony.

![Diagram zadań z metrykami - więcej metryk](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Aby wyświetlić wykres metryki dla połączenia, wybierz węzeł fuzji. Na poniższym wykresie przedstawiono, że żadne zdarzenia nie zostały usunięte lub skorygowane.

![Diagram zadań z metrykami - siatka](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Aby wyświetlić szczegóły wartości i czasu metryki, wskaż wykres.

![Diagram zadań z metrykami - hover](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Rozwiązywanie problemów przy użyciu danych

**Metryka QueryLastProcessedTime** wskazuje, kiedy określony krok odebrał dane. Patrząc na topologii, można pracować wstecz z procesora wyjściowego, aby zobaczyć, który krok nie odbiera danych. Jeśli krok nie jest uzyskanie danych, przejdź do kroku kwerendy tuż przed nim. Sprawdź, czy poprzedni krok kwerendy ma przedział czasu i czy minęło wystarczająco dużo czasu, aby uzyskać dane wyjściowe. (Należy zauważyć, że okna czasowe są przyciągane do godziny).
 
Jeśli poprzednim krokiem kwerendy jest procesor wejściowy, użyj metryki wejściowej, aby odpowiedzieć na następujące pytania docelowe. Mogą one pomóc w określeniu, czy zadanie jest pozyskiwanie danych z jego źródeł wejściowych. Jeśli zapytanie jest podzielone na partycje, sprawdź każdą partycję.
 
### <a name="how-much-data-is-being-read"></a>Ile danych jest odczytywanych?

*   **InputEventsSourcesTotal** jest liczbą odczytanych jednostek danych. Na przykład liczba obiektów blob.
*   **InputEventsTotal** jest liczba zdarzeń odczytanych. Ta metryka jest dostępna dla każdej partycji.
*   **InputEventsInBytesTotal** jest liczbą odczytanych bajtów.
*   **InputEventsLastArrivalTime** jest aktualizowany z każdym odebranym zdarzeniem w kolejce.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Czy czas posuwa się naprzód? Jeśli są odczytywane rzeczywiste zdarzenia, wyróżnienie może nie być wystawiane.

*   Metryka **InputEventsLastPunctuationTime** wskazuje, kiedy wyróżnienie zostało wystawione, aby czas płynął do przodu. Jeśli znaki interpunkcyjne nie są wystawiane, przepływ danych może zostać zablokowany.
 
### <a name="are-there-any-errors-in-the-input"></a>Czy są jakieś błędy w danych wejściowych?

*   **InputEventsEventDataNullTotal** jest liczbą zdarzeń, które mają dane null.
*   **InputEventsSerializerErrorsTotal** jest liczbą zdarzeń, których nie można poprawnie zdesialisować.
*   **InputEventsDegradedTotal** jest liczbą zdarzeń, które miały problem inny niż z deserializacji.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Czy zdarzenia są usuwane lub korygowane?

*   **InputEventsEarlyTotal** jest liczbą zdarzeń, które mają sygnaturę czasową aplikacji przed wysokim znakiem wodnym.
*   **InputEventsLateTotal** to liczba zdarzeń, które mają sygnaturę czasową aplikacji po wysokim znaku wodnym.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** to liczba zdarzeń porzuconych przed godziną rozpoczęcia zadania.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Czy jesteśmy w tyle w czytaniu danych?

*   **Zdarzenia wejściowe zaległe (Total)** informuje, ile więcej wiadomości należy odczytać dla centrów zdarzeń i danych wejściowych usługi Azure IoT Hub. Gdy ta liczba jest większa niż 0, oznacza to, że zadanie nie może przetwarzać danych tak szybko, jak nadchodzi. W takim przypadku może być konieczne zwiększenie liczby jednostek przesyłania strumieniowego i/lub upewnienie się, że zadanie może być równoległe. Więcej informacji na ten temat można znaleźć na [stronie równoległości zapytania](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). 


## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, wypróbuj nasze [forum usługi Azure Stream Analytics.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) 

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do analizy strumienia](stream-analytics-introduction.md)
* [Wprowadzenie do usługi Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalowanie zadań usługi Stream Analytics](stream-analytics-scale-jobs.md)
* [Odwołanie do języka zapytań usługi Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Odwołanie do interfejsu API REST dla zarządzania usługą Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
