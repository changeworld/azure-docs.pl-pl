---
title: Zrozumienie zadanie monitorowania w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób monitorowania zadań usługi Azure Stream Analytics w witrynie Azure portal.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: ce6fc0a90ad093a6bba5a4720777e409202c73ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479370"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Omówienie monitorowania zadań usługi Stream Analytics oraz monitorowanie zapytań

## <a name="introduction-the-monitor-page"></a>Wprowadzenie: Na stronie monitora
Azure portal, zarówno powierzchni kluczowe metryki wydajności, który może służyć do monitorowania i rozwiązywania problemów wydajność zapytań i zadania. Aby wyświetlić te metryki, przejdź do zadania usługi Stream Analytics, interesujące Cię metryki i wyświetlić **monitorowanie** sekcji na stronie Przegląd.  

![Link monitorowania zadania Stream Analytics](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Zostanie wyświetlone okno, jak pokazano:

![Pulpit nawigacyjny monitorowania zadania usługi Stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metryki dostępne dla usługi Stream Analytics
| Metryka                 | Definicja                               |
| ---------------------- | ---------------------------------------- |
| Zaległe zdarzenia wejściowe       | Liczba zdarzeń wejściowych, które są zaległe. Wartość różna od zera dla tej metryki oznacza, że zadania nie jest w stanie na bieżąco z liczbą zdarzeń przychodzących. Jeśli ta wartość jest powoli zwiększenie lub spójnie różna od zera, należy skalować zadania usługi. Dowiedz się więcej, odwiedzając stronę [opis i dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md). |
| Błędy konwersji danych | Liczba zdarzeń wyjściowych, których nie można przekonwertować schematu oczekiwanych danych wyjściowych. Zasady dotyczące błędów można zmienić celu upuszczania, można usunąć zdarzenia, które występują w tym scenariuszu. |
| Wczesne zdarzenia wejściowe       | Zdarzenia, w której sygnatura czasowa aplikacji jest wcześniejszy niż czas ich przyjęcia przez więcej niż 5 minut. |
| Żądania funkcji zakończone niepowodzeniem | Liczba nieudanych wywołań funkcji usługi Azure Machine Learning (jeśli istnieje). |
| Zdarzenia funkcji        | Liczba zdarzeń wysyłanych do funkcji usługi Azure Machine Learning (jeśli istnieje). |
| Żądania funkcji      | Liczba wywołań funkcji usługi Azure Machine Learning (jeśli istnieje). |
| Błędy deserializacji danych wejściowych       | Liczba zdarzeń wejściowych, których nie można wykonać deserializacji.  |
| Zdarzenia wejściowe (bajty)      | Ilość danych otrzymywanych przez zadanie usługi Stream Analytics, w bajtach. Może to służyć do sprawdzania, czy zdarzenia są wysyłane do źródła danych wejściowych. |
| Zdarzenia wejściowe           | Liczba rekordów deserializacji zdarzeń wejściowych. |
| Odebrane źródła wejściowe       | Liczba zdarzeń otrzymanych przez zadanie. Może to służyć do sprawdzania, czy zdarzenia są wysyłane do źródła danych wejściowych. |
| Opóźnione zdarzenia wejściowe      | Zdarzenia, które już korzystać z nowszej niż skonfigurowane okno tolerancji spóźnionego przybycia. Dowiedz się więcej o [zagadnienia dotyczące kolejności zdarzeń usługi Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md) . |
| Zdarzenia poza kolejnością    | Liczba zdarzeń otrzymanych poza kolejnością, które zostały porzucone lub podane skorygowany sygnatury czasowej, na podstawie zasad kolejność zdarzeń. Może to mieć wpływ konfigurację ustawienia okno tolerancji Out of zamówienia. |
| Zdarzenia wyjściowe          | Ilość danych wysłanych przez zadanie usługi Stream Analytics do obiektu docelowego dane wyjściowe w liczbie zdarzeń. |
| Błędy w czasie wykonywania         | Całkowita liczba błędów związanych z przetwarzania zapytań (z wyjątkiem błędów znalezionych podczas zbierać zdarzenia lub wyprowadzania wyników) |
| % wykorzystania SU       | Użycie jednostek przesyłania strumieniowego jest przypisana do zadania na karcie Skala zadania. Jeżeli ten wskaźnik osiągnie 80% lub powyżej, istnieje wysokie prawdopodobieństwo, że przetwarzanie zdarzeń może być opóźniona lub postępu. |
| Opóźnienie znaku wodnego       | Opóźnienie maksymalnego limitu wszystkich partycji wszystkie dane wyjściowe zadania. |

Możesz użyć tych metryk, aby [monitorowania wydajności zadania usługi Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Dostosowywanie monitorowania w witrynie Azure portal
Można dostosować typ wykresu, metryki wyświetlane i zakres w ustawieniach Edytuj wykres czasu. Aby uzyskać więcej informacji, zobacz [jak dostosować monitorowanie](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Wykres czasu monitora zapytań usługi Stream Analytics](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Najnowsze dane wyjściowe
Inny interesujący punkt danych, aby monitorować zadania jest czas ostatniego wyświetlone dane wyjściowe, na stronie Przegląd.
Ten czas to czas aplikacji (czyli czasu przy użyciu sygnaturę czasową od danych zdarzeń) najnowszych danych wyjściowych zadania.

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, skorzystaj z naszego [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

