---
title: Zrozumienie zadanie monitorowania w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób monitorowania zadania w usłudze Azure Stream Analytics
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 200df7602f94f70f3fb9c62ad81a0710923184c7
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2018
ms.locfileid: "52291420"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Omówienie monitorowania zadań usługi Stream Analytics oraz monitorowanie zapytań

## <a name="introduction-the-monitor-page"></a>Wprowadzenie: Strona monitorowania
Azure portal, zarówno powierzchni kluczowe metryki wydajności, który może służyć do monitorowania i rozwiązywania problemów wydajność zapytań i zadania. Aby wyświetlić te metryki, przejdź do zadania usługi Stream Analytics, interesujące Cię metryki i wyświetlić **monitorowanie** sekcji na stronie Przegląd.  

![Monitorowanie łącza](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Zostanie wyświetlone okno, jak pokazano:

![Monitorowanie zadań pulpitu nawigacyjnego](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Metryki dostępne dla usługi Stream Analytics
| Metryka                 | Definicja                               |
| ---------------------- | ---------------------------------------- |
| Zaległe zdarzenia wejściowe       | Liczba zdarzeń wejściowych, które są "zaległe. |
| Błędy konwersji danych | Liczba zdarzeń wyjściowych, których nie można przekonwertować schematu oczekiwanych danych wyjściowych. |
| Wczesne zdarzenia wejściowe       | Liczba zdarzeń otrzymanych wcześnie. |
| Żądania funkcji zakończone niepowodzeniem | Liczba nieudanych wywołań funkcji usługi Azure Machine Learning (jeśli istnieje). |
| Zdarzenia funkcji        | Liczba zdarzeń wysyłanych do funkcji usługi Azure Machine Learning (jeśli istnieje). |
| Żądania funkcji      | Liczba wywołań funkcji usługi Azure Machine Learning (jeśli istnieje). |
| Błędy deserializacji danych wejściowych       | Liczba zdarzeń, których nie można wykonać deserializacji.  |
| Zdarzenia wejściowe (bajty)      | Ilość danych otrzymywanych przez zadanie usługi Stream Analytics, w bajtach. Może to służyć do sprawdzania, czy zdarzenia są wysyłane do źródła danych wejściowych. |
| Zdarzenia wejściowe           | Ilość danych otrzymywanych przez zadanie usługi Stream Analytics, w liczbie zdarzeń. Może to służyć do sprawdzania, czy zdarzenia są wysyłane do źródła danych wejściowych. |
| Odebrane źródła wejściowe       | Liczba zdarzeń przychodzących z źródła danych wejściowych. |
| Opóźnione zdarzenia wejściowe      | Liczba zdarzeń przybywających późne ze źródła, które albo zostały usunięte lub sygnatur czasowych została zmieniona na prawidłową, na podstawie konfiguracji zasad kolejność zdarzeń ustawienie Rozpoznanie późnego przybycia okno tolerancji. |
| Zdarzenia poza kolejnością    | Liczba zdarzeń otrzymanych poza kolejnością, które zostały porzucone lub podane skorygowany sygnatury czasowej, na podstawie zasad kolejność zdarzeń. Może to mieć wpływ konfigurację ustawienia okno tolerancji Out of zamówienia. |
| Zdarzenia wyjściowe          | Ilość danych wysłanych przez zadanie usługi Stream Analytics do obiektu docelowego dane wyjściowe w liczbie zdarzeń. |
| Błędy w czasie wykonywania         | Całkowita liczba błędów związanych z przetwarzania zapytań (z wyjątkiem błędów znalezionych podczas zbierać zdarzenia lub wyniki outputing) |
| % wykorzystania SU       | Użycie jednostek przesyłania strumieniowego jest przypisana do zadania na karcie Skala zadania. Jeżeli ten wskaźnik osiągnie 80% lub powyżej, istnieje wysokie prawdopodobieństwo, że przetwarzanie zdarzeń może być opóźniona lub postępu. |
| Opóźnienie znaku wodnego       | Opóźnienie maksymalnego limitu wszystkich partycji wszystkie dane wyjściowe zadania. |

## <a name="customizing-monitoring-in-the-azure-portal"></a>Dostosowywanie monitorowania w witrynie Azure portal
Można dostosować typ wykresu, metryki wyświetlane i zakres w ustawieniach Edytuj wykres czasu. Aby uzyskać więcej informacji, zobacz [jak dostosować monitorowanie](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Wykres czasu monitora zapytania](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


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

