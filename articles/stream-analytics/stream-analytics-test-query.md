---
title: Testowanie zadania Azure Stream Analytics przy użyciu przykładowych danych
description: W tym artykule opisano sposób użycia Azure Portal do testowania Azure Stream Analytics zadania, przykładowe dane wejściowe i przekazywanie przykładowych danych.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: d68c1ee084efc146d69d8404aadb8acbb4cc33d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891754"
---
# <a name="test-an-azure-stream-analytics-job-with-sample-data"></a>Testowanie zadania Azure Stream Analytics przy użyciu przykładowych danych

W Azure Stream Analytics można testować zapytanie bez uruchamiania lub zatrzymywania zadania. Można testować zapytania dotyczące danych przychodzących z Twojego ujścia danych wejściowych lub przekazać przykładowe dane z pliku lokalnego w witrynie Azure Portal. Możesz również testować zapytania lokalnie z lokalnych danych przykładowych lub danych na żywo w programie [Visual Studio](stream-analytics-live-data-local-testing.md) i [Visual Studio Code](vscode-local-run.md). 

## <a name="sample-incoming-data-from-input"></a>Przykładowe przychodzące dane z danych wejściowych

Azure Stream Analytics automatycznie pobiera zdarzenia z danych wejściowych przesyłania strumieniowego. Można uruchamiać zapytania na domyślnym przykładzie lub ustawić określony przedział czasu dla przykładu.

1. Zaloguj się do Portalu Azure.

2. Znajdź i wybierz istniejące zadanie Stream Analytics.

3. Na stronie Stream Analytics zadania w obszarze nagłówek **topologii zadania** wybierz pozycję **zapytanie** , aby otworzyć okno edytora zapytań. 

4. Aby wyświetlić przykładową listę zdarzeń przychodzących, wybierz pozycję dane wejściowe z ikoną pliku, a Przykładowe zdarzenia będą automatycznie wyświetlane w **podglądzie danych wejściowych**. 

   a. Typ serializacji dla danych jest wykrywany automatycznie, jeśli jego kod JSON lub CSV. Możesz również ręcznie zmienić ten plik w formacie JSON, CSV, AVRO, zmieniając opcję z menu rozwijanego.
    
   b. Użyj selektora, aby wyświetlić dane w formacie **tabeli** lub **nieprzetworzonym** .
    
   d. Jeśli wyświetlane dane nie są aktualne, wybierz pozycję **Odśwież** , aby wyświetlić najnowsze zdarzenia.

   W poniższej tabeli przedstawiono przykład danych w **formacie tabeli**:

   ![Azure Stream Analytics przykładowe dane wejściowe w formacie tabeli](./media/stream-analytics-test-query/asa-sample-table.png)

   W poniższej tabeli przedstawiono przykład danych w **formacie nieprzetworzonym**:

   ![Azure Stream Analytics przykładowe dane wejściowe w formacie nieprzetworzonym](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Aby przetestować zapytanie z danymi przychodzącymi, wybierz opcję **zapytanie testowe**. Wyniki są wyświetlane na karcie **wyniki testu** . Możesz również wybrać pozycję **Pobierz wyniki** , aby pobrać wyniki.

   ![Azure Stream Analytics przykładowe wyniki zapytania testu](./media/stream-analytics-test-query/asa-test-query.png)

6. Aby przetestować zapytanie względem określonego zakresu czasu zdarzeń przychodzących, wybierz **pozycję Wybierz zakres czasu**.
   
   ![Azure Stream Analytics zakres czasu dla przychodzących zdarzeń przykładowych](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Ustaw zakres czasu zdarzeń, których chcesz użyć do testowania zapytania, a następnie wybierz pozycję **przykład**. W tym przedziale czasowym można pobrać maksymalnie 1000 zdarzeń lub 1 MB, zależnie od tego, co nastąpi wcześniej.

   ![Azure Stream Analytics Ustaw zakres czasu dla przychodzących zdarzeń przykładowych](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Po pobraniu zdarzeń dla wybranego zakresu czasu są one wyświetlane na karcie **Podgląd danych wejściowych** .

   ![Wyświetl wyniki testów Azure Stream Analytics](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Wybierz pozycję **Zresetuj** , aby wyświetlić przykładową listę zdarzeń przychodzących. W przypadku wybrania opcji **Zresetuj**wybór zakresu czasu zostanie utracony. Wybierz **zapytanie testowe** , aby przetestować zapytanie i przejrzeć wyniki na karcie **wyniki testów** .

10. Po wprowadzeniu zmian w zapytaniu wybierz pozycję **Zapisz zapytanie** , aby przetestować nową logikę zapytań. Pozwala to na iteracyjne modyfikowanie zapytania i przetestowanie go w celu sprawdzenia, jak dane wyjściowe zmieniają się.

11. Po sprawdzeniu wyników wyświetlanych w przeglądarce można **rozpocząć** zadanie.

## <a name="upload-sample-data-from-a-local-file"></a>Przekazywanie przykładowych danych z pliku lokalnego

Zamiast korzystać z danych na żywo, można użyć przykładowych danych z pliku lokalnego do testowania kwerendy Azure Stream Analytics.

1. Zaloguj się do Portalu Azure.
   
2. Znajdź istniejące zadanie Stream Analytics i wybierz je.

3. Na stronie Stream Analytics zadania w obszarze nagłówek **topologii zadania** wybierz pozycję **zapytanie** , aby otworzyć okno edytora zapytań.

4. Aby przetestować zapytanie przy użyciu pliku lokalnego, wybierz pozycję **Przekaż przykładowe dane wejściowe** na karcie **Podgląd danych wejściowych** . 

   ![Plik przykładowy przekazywania Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Przekaż plik lokalny, aby przetestować zapytanie. Można przekazać tylko pliki z formatami JSON, CSV lub AVRO. Kliknij przycisk **OK**.

   ![Plik przykładowy przekazywania Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Gdy tylko przekażesz plik, możesz również wyświetlić zawartość pliku w postaci tabeli lub w formacie nieprzetworzonym. W przypadku wybrania opcji **Zresetuj**dane przykładowe zostaną zwrócone do przychodzących danych wejściowych opisanych w poprzedniej sekcji. Można przekazać dowolny inny plik, aby przetestować zapytanie w dowolnym momencie.

7. Wybierz **kwerendę testową** , aby przetestować zapytanie względem przekazanego pliku przykładowego.

8. Wyniki testu są wyświetlane na podstawie zapytania. Możesz zmienić zapytanie i wybrać pozycję **Zapisz zapytanie** , aby przetestować nową logikę zapytań. Pozwala to na iteracyjne modyfikowanie zapytania i przetestowanie go w celu sprawdzenia, jak dane wyjściowe zmieniają się.

9. W przypadku użycia wielu wyjść w zapytaniu wyniki są wyświetlane na podstawie wybranych danych wyjściowych. 

   ![Azure Stream Analytics wybrane dane wyjściowe](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Po sprawdzeniu wyników wyświetlanych w przeglądarce można **uruchomić** zadanie.

## <a name="next-steps"></a>Następne kroki

* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [Przykłady zapytań dla typowych wzorców użycia Stream Analytics](stream-analytics-stream-analytics-query-patterns.md)

* [Informacje o danych wejściowych dla Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Poznanie danych wyjściowych z Azure Stream Analytics](stream-analytics-define-outputs.md)