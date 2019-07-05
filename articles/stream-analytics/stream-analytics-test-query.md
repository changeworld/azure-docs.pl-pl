---
title: Testowanie zadania usługi Azure Stream Analytics z przykładowymi danymi
description: W tym artykule opisano sposób użycia witryny Azure portal do testowania zadania usługi Azure Stream Analytics, przykładowe dane wejściowe i Przekaż dane przykładowe.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: 1a9caf83c6f4cd4ed15290afc872043c11234552
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508702"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testowanie zapytania usługi Stream Analytics z przykładowymi danymi

Za pomocą usługi Azure Stream Analytics, możesz przykładowe dane z danych wejściowych lub przekazywanie przykładowych danych do testowania zapytań w witrynie Azure portal, bez uruchamiania lub zatrzymywania zadania.

## <a name="upload-or-sample-data-from-a-live-source-to-test-the-query"></a>Przekazywanie lub przykładowe dane ze źródła na żywo, aby przetestować zapytanie

1. Zaloguj się do Portalu Azure. 

2. Znajdź istniejące zadanie usługi Stream Analytics i zaznacz je.

3. W usłudze Stream Analytics zadania stronie w obszarze **topologia zadań** nagłówka, wybierz **zapytania** otwarte okno edytora zapytań. 

4. Aby przetestować zapytanie można można następnie albo przykładowe dane z danych wejściowych na żywo lub przekazywania z pliku. Dane muszą być zserializowane w formacie JSON, CSV lub AVRO. Przykładowe dane wejściowe musi być zakodowany w formacie UTF-8 i nie skompresowany. Tylko przecinek (,) ogranicznik jest obsługiwana dla testowanie danych wejściowych CSV, w portalu.

    1. Przy użyciu danych wejściowych na żywo: kliknij prawym przyciskiem myszy na dowolnym dane wejściowe. Następnie wybierz pozycję **przykładowe dane z danych wejściowych**. Na następnym ekranie można ustawić czasu trwania próbki. Próbkowania zdarzeń od źródła na żywo pobierze maksymalnie 1000 zdarzeń lub 1 MB (osiągnięta jako pierwsza), dzięki czemu dane próbkowania mogą nie reprezentować wskazany interwał w pełnym wymiarze czasu.

    1. Przy użyciu plików: kliknij prawym przyciskiem myszy na dowolnym dane wejściowe. Następnie wybierz pozycję **Przekaż dane przykładowe z pliku**. 

    ![Zapytanie testowe edytora zapytań usługi Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Po zakończeniu pobierania próbek lub przekazywania, wybierz **Test** do testowania to zapytanie względem przykładowych danych zostały podane.

    ![Edytor testu przykładowych danych zapytań usługi Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Jeśli potrzebujesz danych wyjściowych testu w celu późniejszego użycia danych wyjściowych zapytania jest wyświetlany w przeglądarce z linkiem do pobierania wyników. 

7. Iteracyjne zmodyfikuj zapytanie i przetestować go ponownie, aby zobaczyć, jak zmienią się dane wyjściowe.

   ![Edytor przykładowe dane wyjściowe zapytań usługi Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Gdy używasz wiele wyjść w zapytaniu, wyniki są wyświetlane w osobnych kartach, a następnie można łatwo przełączać się między nimi.

8. Po upewnieniu się wyniki wyświetlane w przeglądarce, **Zapisz** zapytania. Następnie **Start** zadania i pozwól mu przetwarzanie zdarzeń przychodzących.

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
