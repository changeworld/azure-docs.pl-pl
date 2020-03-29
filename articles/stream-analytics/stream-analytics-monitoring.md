---
title: Opis monitorowania zadań w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób monitorowania zadań usługi Azure Stream Analytics w witrynie Azure portal.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 4e9f90035816269d2d41781be34d0d8080628b12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431653"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Opis monitorowania zadań usługi Stream Analytics i monitorowania zapytań

## <a name="introduction-the-monitor-page"></a>Wprowadzenie: Strona monitora
Portal Azure powierzchnie kluczowych metryk wydajności, które mogą służyć do monitorowania i rozwiązywania problemów z kwerendą i wydajnością zadania. Aby wyświetlić te dane, przejdź do zadania usługi Stream Analytics, które Chcesz zobaczyć metryki i wyświetlić sekcję **Monitorowanie** na stronie Przegląd.  

![Łącze monitorowania zadań usługi Usługi Stream Analytics](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Okno pojawi się w sposób pokazany:

![Pulpit nawigacyjny monitorowania zadań usługi Usługi Stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Dane dostępne dla usługi Stream Analytics
| Metryka                 | Definicja                               |
| ---------------------- | ---------------------------------------- |
| Zaległe zdarzenia wejściowe       | Liczba zdarzeń wejściowych, które są zaległe. Wartość niezerowa dla tej metryki oznacza, że zadanie nie jest w stanie nadążyć za liczbą zdarzeń przychodzących. Jeśli ta wartość jest powoli rośnie lub konsekwentnie niezerować, należy skalować w poziomie zadania. Możesz dowiedzieć się więcej, odwiedzając [zrozumieć i dostosować jednostki przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md). |
| Błędy konwersji danych | Liczba zdarzeń wyjściowych, których nie można przekonwertować na oczekiwany schemat wyjściowy. Zasady błędów można zmienić na "Upuść", aby upuścić zdarzenia, które napotykają ten scenariusz. |
| Zdarzenia wczesnego wprowadzania       | Zdarzenia, których sygnatura czasowa aplikacji jest wcześniejsza niż czas przybycia o więcej niż 5 minut. |
| Nieudane żądania funkcji | Liczba nieudanych wywołań funkcji usługi Azure Machine Learning (jeśli jest obecna). |
| Zdarzenia funkcji        | Liczba zdarzeń wysłanych do funkcji usługi Azure Machine Learning (jeśli jest obecna). |
| Żądania funkcji      | Liczba wywołań funkcji usługi Azure Machine Learning (jeśli jest obecna). |
| Błędy deserializacji danych wejściowych       | Liczba zdarzeń wejściowych, których nie można zdesenializować.  |
| Bajty zdarzenia wejściowego      | Ilość danych odebranych przez zadanie usługi Stream Analytics w bajtach. To może służyć do sprawdzania poprawności, że zdarzenia są wysyłane do źródła wejściowego. |
| Zdarzenia wejściowe           | Liczba rekordów zdań z zdarzeń wejściowych. Ta liczba nie obejmuje przychodzących zdarzeń, które powodują błędy deserializacji. |
| Odebrane źródła wejściowe       | Liczba wiadomości odebranych przez zadanie. W przypadku Centrum zdarzeń komunikat jest pojedynczym EventData. W przypadku obiektów blob wiadomość jest pojedynczym obiektem blob. Należy pamiętać, że źródła wejściowe są liczone przed deserializacji. Jeśli występują błędy deserializacji, źródła wejściowe mogą być większe niż zdarzenia wejściowe. W przeciwnym razie może być mniejsza lub równa zdarzeń wejściowych, ponieważ każda wiadomość może zawierać wiele zdarzeń. |
| Zdarzenia późnego wprowadzania      | Zdarzenia, które dotarły później niż skonfigurowane okno tolerancji późnego przybycia. Dowiedz się więcej o [zagadnieniach dotyczących zamówień zdarzeń usługi Azure Stream Analytics.](stream-analytics-out-of-order-and-late-events.md) |
| Zdarzenia poza kolejnością    | Liczba zdarzeń odebranych poza kolejnością, które zostały usunięte lub podane skorygowany sygnatura czasowa, na podstawie zasad zamawiania zdarzeń. Może mieć na to wpływ konfiguracja ustawienia okna tolerancja poza kolejnością. |
| Zdarzenia wyjściowe          | Ilość danych wysyłanych przez zadanie usługi Stream Analytics do obiektu docelowego danych wyjściowych w liczbie zdarzeń. |
| Błędy środowiska uruchomieniowego         | Całkowita liczba błędów związanych z przetwarzaniem zapytań (z wyłączeniem błędów znalezionych podczas pozyskiwania zdarzeń lub wyprowadzania wyników) |
| Wykorzystanie SU %       | Wykorzystanie jednostek przesyłania strumieniowego przypisanych do zadania na karcie Skalowanie zadania. Jeśli wskaźnik ten osiągnie 80% lub powyżej, istnieje duże prawdopodobieństwo, że przetwarzanie zdarzeń może być opóźnione lub zatrzymane. |
| Opóźnienie znaku wodnego       | Maksymalne opóźnienie znaku wodnego we wszystkich partycjach wszystkich wyjść w zadaniu. |

Za pomocą tych danych można [monitorować wydajność zadania usługi Stream Analytics.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Dostosowywanie monitorowania w witrynie Azure portal
Typ wykresu, wyświetlane dane i zakres czasu można dostosować w ustawieniach edytuj wykres. Aby uzyskać szczegółowe informacje, zobacz [Jak dostosować monitorowanie](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Wykres czasu monitorowania zapytań usługi Stream Analytics](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Najnowsze dane wyjściowe
Innym interesującym punktem danych do monitorowania zadania jest czas ostatniego wyjścia, pokazany na stronie Przegląd.
Tym razem jest to czas aplikacji (tj. czas przy użyciu sygnatury czasowej z danych zdarzeń) najnowszych danych wyjściowych zadania.

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, skorzystaj z naszego [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
