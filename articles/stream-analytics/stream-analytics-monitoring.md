---
title: Zrozumienie zadanie monitorowania w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób monitorowania zadań usługi Azure Stream Analytics w witrynie Azure portal.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 4e9f90035816269d2d41781be34d0d8080628b12
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431653"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Omówienie monitorowania zadań usługi Stream Analytics oraz monitorowanie zapytań

## <a name="introduction-the-monitor-page"></a>Wprowadzenie: Strona monitorowania
Azure Portal prezentuje kluczowe metryki wydajności, których można użyć do monitorowania i rozwiązywania problemów z zapytaniami i wydajnością zadań. Aby wyświetlić te metryki, przejdź do zadania usługi Stream Analytics, interesujące Cię metryki i wyświetlić **monitorowanie** sekcji na stronie Przegląd.  

![Link monitorowania zadania Stream Analytics](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Zostanie wyświetlone okno, jak pokazano:

![Pulpit nawigacyjny monitorowania zadania usługi Stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metryki dostępne dla usługi Stream Analytics
| Metryka                 | Definicja                               |
| ---------------------- | ---------------------------------------- |
| Zaległe zdarzenia wejściowe       | Liczba zdarzeń wejściowych, które są zaległe. Wartość różna od zera dla tej metryki oznacza, że zadanie nie jest w stanie zachować liczby zdarzeń przychodzących. Jeśli ta wartość jest powoli zwiększana lub konsekwentnie różna od zera, należy przeskalować zadanie. Więcej informacji można znaleźć w [opisie i dostosowaniu jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md). |
| Błędy konwersji danych | Liczba zdarzeń wyjściowych, których nie można przekonwertować schematu oczekiwanych danych wyjściowych. Zasady błędów można zmienić na "Drop", aby porzucić zdarzenia, które napotykają ten scenariusz. |
| Wczesne zdarzenia wejściowe       | Zdarzenia, których sygnatura czasowa aplikacji jest wcześniejsza niż ich czas przybycia o więcej niż 5 minut. |
| Żądania funkcji zakończone niepowodzeniem | Liczba nieudanych wywołań funkcji usługi Azure Machine Learning (jeśli istnieje). |
| Zdarzenia funkcji        | Liczba zdarzeń wysyłanych do funkcji usługi Azure Machine Learning (jeśli istnieje). |
| Żądania funkcji      | Liczba wywołań funkcji usługi Azure Machine Learning (jeśli istnieje). |
| Błędy deserializacji danych wejściowych       | Liczba zdarzeń wejściowych, których nie można zdeserializować.  |
| Zdarzenia wejściowe (bajty)      | Ilość danych otrzymywanych przez zadanie usługi Stream Analytics, w bajtach. Może to służyć do sprawdzania, czy zdarzenia są wysyłane do źródła danych wejściowych. |
| Zdarzenia wejściowe           | Liczba rekordów deserializowanych ze zdarzeń wejściowych. Ta liczba nie obejmuje zdarzeń przychodzących, które powodują błędy deserializacji. |
| Odebrane źródła wejściowe       | Liczba komunikatów odebranych przez zadanie. W przypadku usługi Event Hub komunikat to pojedynczy EventData. W przypadku obiektu BLOB komunikat jest pojedynczym obiektem BLOB. Należy pamiętać, że źródła danych wejściowych są zliczane przed deserializacji. Jeśli występują błędy deserializacji, źródła danych wejściowych mogą być większe niż zdarzenia wejściowe. W przeciwnym razie może być mniejsze niż lub równe zdarzenia wejściowe, ponieważ każdy komunikat może zawierać wiele zdarzeń. |
| Opóźnione zdarzenia wejściowe      | Zdarzenia, które dotarły później niż skonfigurowane okno tolerancji opóźnionego przybycia. Dowiedz się więcej na temat [zagadnień dotyczących kolejności zdarzeń Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md) . |
| Zdarzenia poza kolejnością    | Liczba zdarzeń otrzymanych poza kolejnością, które zostały porzucone lub podane skorygowany sygnatury czasowej, na podstawie zasad kolejność zdarzeń. Może to mieć wpływ konfigurację ustawienia okno tolerancji Out of zamówienia. |
| Zdarzenia wyjściowe          | Ilość danych wysłanych przez zadanie usługi Stream Analytics do obiektu docelowego dane wyjściowe w liczbie zdarzeń. |
| Błędy w czasie wykonywania         | Łączna liczba błędów związanych z przetwarzaniem zapytań (z wyjątkiem błędów znalezionych podczas pozyskiwania zdarzeń lub wyprowadzania wyników) |
| % wykorzystania SU       | Użycie jednostek przesyłania strumieniowego jest przypisana do zadania na karcie Skala zadania. Jeżeli ten wskaźnik osiągnie 80% lub powyżej, istnieje wysokie prawdopodobieństwo, że przetwarzanie zdarzeń może być opóźniona lub postępu. |
| Opóźnienie znaku wodnego       | Opóźnienie maksymalnego limitu wszystkich partycji wszystkie dane wyjściowe zadania. |

Możesz użyć tych metryk do [monitorowania wydajności zadania Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Dostosowywanie monitorowania w witrynie Azure portal
Można dostosować typ wykresu, metryki wyświetlane i zakres w ustawieniach Edytuj wykres czasu. Aby uzyskać więcej informacji, zobacz [jak dostosować monitorowanie](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Wykres czasu monitora zapytań usługi Stream Analytics](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Najnowsze dane wyjściowe
Inny interesujący punkt danych, aby monitorować zadania jest czas ostatniego wyświetlone dane wyjściowe, na stronie Przegląd.
Ten czas to czas aplikacji (czyli czasu przy użyciu sygnaturę czasową od danych zdarzeń) najnowszych danych wyjściowych zadania.

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, skorzystaj z naszego [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
