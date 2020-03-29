---
title: Testowanie zadania usługi Azure Stream Analytics przy obliczu przykładowych danych
description: W tym artykule opisano, jak używać witryny Azure Portal do testowania zadania usługi Azure Stream Analytics, przykładowych danych wejściowych i przekazywania przykładowych danych.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: de2b99666dbed4eaec3db3e56800e9a7352e10e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898383"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Testowanie zadania usługi Azure Stream Analytics w portalu

W usłudze Azure Stream Analytics możesz przetestować zapytanie bez uruchamiania lub zatrzymywania zadania. Można przetestować zapytania dotyczące danych przychodzących ze źródeł przesyłania strumieniowego lub przekazać przykładowe dane z pliku lokalnego w witrynie Azure Portal. Można również przetestować kwerendy lokalnie z lokalnych danych przykładowych lub danych na żywo w [programie Visual Studio](stream-analytics-live-data-local-testing.md) i visual studio [code.](visual-studio-code-local-run-live-input.md)

## <a name="automatically-sample-incoming-data-from-input"></a>Automatyczne próbkowanie danych przychodzących z danych wejściowych

Usługa Azure Stream Analytics automatycznie pobiera zdarzenia z danych wejściowych przesyłania strumieniowego. Można uruchomić kwerendy w próbce domyślnej lub ustawić określony przedział czasu dla próbki.

1. Zaloguj się do Portalu Azure.

2. Znajdź i wybierz istniejące zadanie usługi Stream Analytics.

3. Na stronie zadania usługi Stream Analytics w nagłówku **Topologia zadań** wybierz pozycję **Zapytanie,** aby otworzyć okno Edytor zapytań. 

4. Aby wyświetlić przykładową listę zdarzeń przychodzących, wybierz wejście z ikoną pliku, a przykładowe zdarzenia będą automatycznie wyświetlane w **podglądzie danych wejściowych**.

   a. Typ serializacji danych jest automatycznie wykrywany, jeśli jest to jego JSON lub CSV. Możesz ręcznie zmienić go również na JSON, CSV, AVRO, zmieniając opcję w menu rozwijanym.
    
   b. Użyj selektora, aby wyświetlić dane w formacie **Tabela** lub **Raw.**
    
   d. Jeśli wyświetlane dane nie są aktualne, wybierz **odśwież,** aby wyświetlić najnowsze zdarzenia.

   Poniższa tabela jest przykładem danych w **formacie tabeli:**

   ![Przykładowe dane wejściowe usługi Azure Stream Analytics w formacie tabeli](./media/stream-analytics-test-query/asa-sample-table.png)

   Poniższa tabela jest przykładem danych w **formacie Raw:**

   ![Przykładowe dane wejściowe usługi Azure Stream Analytics w formacie raw](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Aby przetestować kwerendę z danymi przychodzącymi, wybierz opcję **Testuj kwerendę**. Wyniki są wyświetlane na karcie **Wyniki testu.** Można również wybrać **Pobierz wyniki,** aby pobrać wyniki.

   ![Wyniki przykładowych zapytań testowych usługi Azure Stream Analytics](./media/stream-analytics-test-query/asa-test-query.png)

6. Aby przetestować kwerendę względem określonego zakresu czasu zdarzeń przychodzących, wybierz **pozycję Wybierz zakres czasu**.
   
   ![Zakres czasu usługi Azure Stream Analytics dla przychodzących zdarzeń przykładowych](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Ustaw zakres czasu zdarzeń, których chcesz użyć do przetestowania kwerendy, i wybierz **pozycję Przykład .** W tym przedziale czasowym można pobrać maksymalnie 1000 zdarzeń lub 1 MB, w zależności od tego, co nastąpi wcześniej.

   ![Usługa Azure Stream Analytics ustawia zakres czasu dla przychodzących zdarzeń przykładowych](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Po próbkowanie zdarzeń dla wybranego zakresu czasu pojawiają się one na karcie **Podgląd danych wejściowych.**

   ![Wyniki testów widoku usługi Azure Stream Analytics](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Wybierz **reset,** aby wyświetlić przykładową listę zdarzeń przychodzących. Jeśli **wybierzesz Reset,** twój wybór zakresu czasu zostanie utracony. Wybierz **opcję Testuj kwerendę,** aby przetestować kwerendę i przejrzeć wyniki na karcie **Wyniki testu.**

10. Po wprowadzeniu zmian w kwerendzie wybierz pozycję **Zapisz kwerendę,** aby przetestować nową logikę kwerendy. Dzięki temu można iteratively zmodyfikować kwerendę i przetestować go ponownie, aby zobaczyć, jak zmienia się dane wyjściowe.

11. Po zweryfikowaniu wyników wyświetlanych w przeglądarce możesz przystąpić do **uruchomienia** zadania.

## <a name="upload-sample-data-from-a-local-file"></a>Przekazywanie przykładowych danych z pliku lokalnego

Zamiast używać danych na żywo, można użyć przykładowych danych z pliku lokalnego, aby przetestować zapytanie usługi Azure Stream Analytics.

1. Zaloguj się do Portalu Azure.
   
2. Znajdź istniejące zadanie usługi Stream Analytics i wybierz je.

3. Na stronie zadania usługi Stream Analytics w nagłówku **Topologia zadań** wybierz pozycję **Zapytanie,** aby otworzyć okno Edytor zapytań.

4. Aby przetestować kwerendę za pomocą pliku lokalnego, wybierz **pozycję Przekaż przykładowe dane wejściowe** na karcie **Podgląd danych wejściowych.** 

   ![Przykładowy plik przekazywania usługi Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Przekaż plik lokalny, aby przetestować kwerendę. Pliki można przesyłać tylko w formatach JSON, CSV lub AVRO. Kliknij przycisk **OK**.

   ![Przykładowy plik przekazywania usługi Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Po przesłaniu pliku można również wyświetlić zawartość pliku w formularzu jako tabelę lub w formacie raw. Jeśli wybierzesz **Reset,** przykładowe dane powrócą do przychodzących danych wejściowych wyjaśnionych w poprzedniej sekcji. Możesz przekazać dowolny inny plik, aby przetestować kwerendę w dowolnym momencie.

7. Wybierz **opcję Testuj kwerendę,** aby przetestować kwerendę względem przekazanego przykładowego pliku.

8. Wyniki testów są wyświetlane na podstawie zapytania. Można zmienić kwerendę i wybrać **zapisz kwerendę,** aby przetestować nową logikę kwerendy. Dzięki temu można iteratively zmodyfikować kwerendę i przetestować go ponownie, aby zobaczyć, jak zmienia się dane wyjściowe.

9. Podczas korzystania z wielu wyjść w kwerendzie, wyniki są wyświetlane na podstawie wybranych danych wyjściowych. 

   ![Wybrane dane wyjściowe usługi Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Po zweryfikowaniu wyników wyświetlanych w przeglądarce można **uruchomić** zadanie.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie rozwiązania IoT przy użyciu usługi Stream Analytics:](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)ten samouczek poprowadzi Cię do stworzenia kompleksowego rozwiązania z generatorem danych, który symuluje ruch na stanowisku poboru opłat.

* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [Przykłady zapytań dotyczące typowych wzorców użycia usługi Stream Analytics](stream-analytics-stream-analytics-query-patterns.md)

* [Opis danych wejściowych dla usługi Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Opis produktów z usługi Azure Stream Analytics](stream-analytics-define-outputs.md)
