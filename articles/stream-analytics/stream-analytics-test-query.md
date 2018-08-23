---
title: Testowanie zadania usługi Azure Stream Analytics z przykładowymi danymi
description: Jak jest przetestowanie kwerend w zadania usługi Stream Analytics.
keywords: W tym artykule opisano sposób użycia witryny Azure portal do testowania zadania usługi Azure Stream Analytics, przykładowe dane wejściowe i Przekaż dane przykładowe.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: d699d69362b2d28c205aab14a4bfb26570a68a4e
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2018
ms.locfileid: "42060230"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testowanie zapytania usługi Stream Analytics z przykładowymi danymi

Za pomocą usługi Azure Stream Analytics, możesz przekazać przykładowe dane i testowanie zapytań w witrynie Azure portal, bez uruchamiania lub zatrzymywania zadania.

## <a name="upload-sample-data-and-test-the-query"></a>Przekazywanie przykładowych danych i przetestować zapytanie

1. Zaloguj się do Portalu Azure. 

2. Znajdź istniejące zadanie usługi Stream Analytics i zaznacz je.

3. W usłudze Stream Analytics zadania stronie w obszarze **topologia zadań** nagłówka, wybierz **zapytania** otwarte okno edytora zapytań. 

4. Aby przetestować zapytanie za pomocą przykładowych danych wejściowych, kliknij prawym przyciskiem myszy dowolne dane wejściowe.  Następnie wybierz pozycję **Przekaż dane przykładowe z pliku**. Dane muszą być zserializowane w formacie JSON, CSV lub AVRO.

    ![Zapytanie testowe edytora zapytań usługi Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Po zakończeniu pobierania wybierz **Test** do testowania to zapytanie względem przykładowych danych zostały podane.

    ![Edytor testu przykładowych danych zapytań usługi Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Jeśli potrzebujesz danych wyjściowych testu w celu późniejszego użycia danych wyjściowych zapytania jest wyświetlany w przeglądarce z linkiem do pobierania wyników. 

7. Iteracyjne zmodyfikuj zapytanie i przetestować go ponownie, aby zobaczyć, jak zmienią się dane wyjściowe.

   ![Edytor przykładowe dane wyjściowe zapytań usługi Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Gdy używasz wiele wyjść w zapytaniu, wyniki są wyświetlane w osobnych kartach, a następnie można łatwo przełączać się między nimi.

8. Po upewnieniu się wyniki wyświetlane w przeglądarce, **Zapisz** zapytania. Następnie **Start** zadania i pozwól mu przetwarzanie zdarzeń przychodzących.

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
