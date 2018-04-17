---
title: Testowanie zadania usługi analiza strumienia Azure z przykładowymi danymi | Dokumentacja firmy Microsoft
description: Jak przetestować zapytań w zadania usługi analiza strumienia.
keywords: zadania testowego, wprowadzić próbkowania, Przekaż przykładowe daty
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.workload: data-services
ms.date: 03/18/2018
ms.author: sngun
ms.openlocfilehash: c026a91fff5b8ef5774993b335f8d61877aa5d39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="test-your-stream-analytics-query-with-sample-data"></a>Testowanie kwerendy Stream Analytics z przykładowymi danymi

Za pomocą usługi Azure Stream Analytics, możesz przekazać przykładowe dane i testowania zapytania w portalu bez uruchamiania lub zatrzymywania zadania.

## <a name="upload-sample-data-and-test-the-query"></a>Przekaż przykładowe dane i przetestować zapytanie

1. Przejdź do jednej z istniejącego zadania usługi analiza strumienia > kliknij **zapytania** otwarte okno edytora zapytań. 

2. Aby przetestować zapytanie z przykładowych danych wejściowych, kliknij prawym przyciskiem myszy na żadnym z wejść, a następnie wybierz **przekazać dane przykładowe z pliku**. Obecnie można przekazać tylko dane w formacie JSON. Jeśli dane są w różnych formatach, takich jak CSV, należy ją przekonwertować na format JSON, przed przesłaniem. Można użyć dowolnego narzędzia konwersji opensource, takich jak [woluminu CSV do przetwarzania JSON](http://www.convertcsv.com/csv-to-json.htm) można przekonwertować danych na notację JSON.

    ![Edytor testów zapytania zapytań usługi Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

3. Po zakończeniu pobierania kliknij **Test** do przetestowania tego zapytania dotyczącego podano przykładowe dane.

    ![Edytor testów przykładowych danych zapytań usługi Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

4. Jeśli chcesz zapisać testu, dane wyjściowe w celu późniejszego użycia, danych wyjściowych kwerendy jest wyświetlana w przeglądarce z łączem do pobierania wyników. Można teraz łatwo i wielokrotnie powtarzane zmodyfikuj zapytanie i przetestować go wielokrotnie, aby zobaczyć, jak zmiany danych wyjściowych.

   ![Edytor przykładowe dane wyjściowe zapytań usługi Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Użycie wielu wyjść w zapytaniu, można wyświetlić wyniki każdego dane wyjściowe oddzielnie i łatwo przełączać się między nimi. Po zweryfikowaniu wyniki wyświetlane w przeglądarce, możesz zapisać zapytania, uruchomić zadanie i pozwól przetworzyć zdarzenia bez błędów.
Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
