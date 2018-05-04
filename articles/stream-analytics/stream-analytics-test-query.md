---
title: Zadania usługi analiza strumienia Azure z przykładowymi danymi testowego
description: Jak przetestować zapytań w zadania usługi analiza strumienia.
keywords: W tym artykule opisano sposób użycia portalu Azure do testowania zadania usługi analiza strumienia Azure, przykładowe dane wejściowe i przekaż przykładowe dane.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 3dc9091934f3db8ededc13f74d2f302eccace4d6
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testowanie kwerendy Stream Analytics z przykładowymi danymi

Za pomocą usługi Azure Stream Analytics, możesz przekazać przykładowe dane i testowania zapytania w portalu Azure, bez uruchamiania lub zatrzymywania zadania.

## <a name="upload-sample-data-and-test-the-query"></a>Przekaż przykładowe dane i przetestować zapytanie

1. Zaloguj się do Portalu Azure. 

2. Znajdź istniejące zadania usługi analiza strumienia i zaznacz je.

3. Na usługi analiza strumienia zadania w obszarze strony, **topologii zadania** nagłówek, wybierz **zapytania** otwarte okno edytora zapytań. 

4. Aby przetestować zapytanie z przykładowych danych wejściowych, kliknij prawym przyciskiem myszy dowolne dane wejściowe.  Następnie wybierz **przekazać dane przykładowe z pliku**.

   Dane muszą być tylko dane w formacie JSON. Jeśli dane są w różnych formatach, takich jak CSV, należy ją przekonwertować na format JSON, przed przesłaniem. Można użyć dowolnego narzędzia konwersji opensource, takich jak [woluminu CSV do przetwarzania JSON](http://www.convertcsv.com/csv-to-json.htm) można przekonwertować danych na notację JSON.

    ![Edytor testów zapytania zapytań usługi Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Po zakończeniu przekazywania wybierz **Test** do przetestowania tego zapytania dotyczącego podano przykładowe dane.

    ![Edytor testów przykładowych danych zapytań usługi Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Jeśli potrzebujesz danych wyjściowych testu do późniejszego użycia, danych wyjściowych kwerendy jest wyświetlana w przeglądarce z łączem do pobierania wyników. 

7. Wielokrotnie powtarzane zmodyfikuj zapytanie i przetestować go ponownie, aby zobaczyć, jak zmiany danych wyjściowych.

   ![Edytor przykładowe dane wyjściowe zapytań usługi Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Użycie wielu wyjść w zapytaniu, wyniki są wyświetlane na osobnych kartach i możliwość łatwego przełączania między nimi.

8. Po zweryfikowaniu wyniki wyświetlane w przeglądarce, **zapisać** zapytania. Następnie **Start** zadania i pozwól mu przetwarzania zdarzenia przychodzącego.

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
