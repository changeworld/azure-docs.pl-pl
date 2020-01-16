---
title: Rozwiązywanie problemów z zapytaniami Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z zapytaniami w Azure Stream Analytics zadania.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 5534a46ba99d1536d331b5852ef47588f03d73a4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980277"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Rozwiązywanie problemów z zapytaniami Azure Stream Analytics

W tym artykule opisano typowe problemy związane z tworzeniem zapytań Stream Analytics i sposobach ich rozwiązywania.

## <a name="query-is-not-producing-expected-output"></a>Zapytanie nie produkuje oczekiwanych danych wyjściowych
1.  Sprawdzanie błędów przez testowanie lokalne:
    - Na karcie **zapytanie** wybierz pozycję **test**. Aby [przetestować zapytanie,](stream-analytics-test-query.md)Użyj pobranych przykładowych danych. Przejrzyj błędy i spróbuj je poprawić.   
    - Możesz również [testować zapytanie bezpośrednio na żywo](stream-analytics-live-data-local-testing.md) przy użyciu narzędzi Stream Analytics Tools for Visual Studio.

2.  Jeśli używasz [**sygnatur czasowych przez**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), sprawdź, czy zdarzenia mają sygnatury czasowe większe niż [godzina rozpoczęcia zadania](stream-analytics-out-of-order-and-late-events.md).

3.  Eliminowanie typowych pułapek, takich jak:
    - Klauzula [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) w zapytaniu odfiltrowana wszystkie zdarzenia, uniemożliwiając wygenerowanie danych wyjściowych.
    - Funkcja [**Cast**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) kończy się niepowodzeniem, co powoduje niepowodzenie zadania. Aby uniknąć błędów rzutowania typu, należy zamiast tego użyć [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics) .
    - Gdy korzystasz z funkcji okna, poczekaj, aż cały czas trwania okna zobaczy dane wyjściowe zapytania.
    - Sygnatura czasowa dla zdarzeń poprzedza czas rozpoczęcia zadania, dlatego zdarzenia są usuwane.

4.  Upewnij się, że zasady określania kolejności zdarzeń zostały skonfigurowane zgodnie z oczekiwaniami. Przejdź do obszaru **Ustawienia** i wybierz pozycję [**porządkowanie zdarzeń**](stream-analytics-out-of-order-and-late-events.md). Zasady *nie* są stosowane, gdy do testowania zapytania jest używany przycisk **Testuj** . Ten wynik jest jedną różnicą między testowaniem w przeglądarce a uruchomieniem zadania w środowisku produkcyjnym.

5. Debuguj przy użyciu dzienników inspekcji i diagnostyki:
    - Użyj [dzienników inspekcji](../azure-resource-manager/management/view-activity-logs.md)i przefiltruj, aby identyfikować i debugować błędy.
    - [Dzienniki diagnostyczne zadań](stream-analytics-job-diagnostic-logs.md) umożliwiają identyfikowanie i Debugowanie błędów.

## <a name="job-is-consuming-too-many-streaming-units"></a>Zadanie zużywa zbyt wiele jednostek przesyłania strumieniowego
Upewnij się, że korzystasz z przetwarzanie równoległe w Azure Stream Analytics. Możesz dowiedzieć się, jak [skalować za pomocą zapytań przetwarzanie równoległe](stream-analytics-parallelization.md) Stream Analytics zadań przez skonfigurowanie partycji wejściowych i dostrajanie definicji zapytania analitycznego.

## <a name="debug-queries-progressively"></a>Stopniowo Debuguj zapytania

W czasie rzeczywistym, wiedząc, jakie dane wyglądają jak w środku zapytania, mogą być przydatne. Ponieważ dane wejściowe lub kroki zadania Azure Stream Analytics mogą być odczytywane wiele razy, można napisać dodatkowe instrukcje SELECT INTO. Wykonanie tej operacji spowoduje wyjście danych pośrednich do magazynu i pozwala sprawdzić poprawność danych, tak jak *zmienne czujki* , podczas debugowania programu.

Następujące przykładowe zapytanie w zadaniu Azure Stream Analytics ma jedno wejście strumienia, dwa dane wejściowe danych referencyjnych i dane wyjściowe do usługi Azure Table Storage. Zapytanie łączy dane z centrum zdarzeń i dwóch referencyjnych obiektów BLOB w celu uzyskania informacji o nazwie i kategorii:

![Przykład Stream Analytics wybierz do zapytania](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Należy pamiętać, że zadanie jest uruchomione, ale żadne zdarzenia nie są generowane w danych wyjściowych. Na kafelku **monitorowanie** , widocznym tutaj, można zobaczyć, że dane wejściowe są wytwarzane, ale nie wiesz, który krok **sprzężenia** spowodował porzucenie wszystkich zdarzeń.

![Kafelek monitorowanie Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

W takiej sytuacji można dodać kilka dodatkowych instrukcji SELECT INTO do "log" pośrednich wyników SPRZĘŻENIa i danych odczytywanych z danych wejściowych.

W tym przykładzie dodaliśmy dwa nowe "tymczasowe dane wyjściowe". Mogą to być dowolny ujścia, którego potrzebujesz. Oto przykład użycia usługi Azure Storage:

![Dodawanie dodatkowych instrukcji SELECT INTO do zapytania Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Następnie można napisać ponownie zapytanie w następujący sposób:

![Zapisano ponownie polecenie SELECT w Stream Analytics Query](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Teraz ponownie Rozpocznij zadanie i pozwól, aby było uruchamiane przez kilka minut. Następnie wykonaj zapytania dotyczące temp1 i temp2 z programem Visual Studio Cloud Explorer, aby utworzyć następujące tabele:


**tabeli temp1** ![SELECT INTO temp1 Table Stream Analytics Query](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)


**tabeli temp2** ![SELECT INTO temp2 Table Stream Analytics Query](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Jak widać, temp1 i temp2 mają dane, a kolumna Name jest wypełniana prawidłowo w temp2. Jednak ze względu na to, że w danych wyjściowych nadal nie ma danych, wystąpił problem:

![Zaznacz w tabeli output1 bez zapytania dotyczącego danych Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Pobierając próbkowanie danych, można prawie upewnić się, że występuje problem z drugim SPRZĘŻENIem. Możesz pobrać dane referencyjne z obiektu BLOB i zajrzeć do:

![Zaznacz w tabeli ref Stream Analytics zapytanie](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Jak widać, format identyfikatora GUID w tych danych referencyjnych różni się od formatu kolumny [from] w temp2. To dlatego, że dane nie dotarły do output1 zgodnie z oczekiwaniami.

Możesz naprawić format danych, przekazać go do referencyjnego obiektu BLOB, a następnie spróbować ponownie:

![Wybierz tabelę tymczasową Stream Analytics zapytanie](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Tym razem dane w danych wyjściowych są formatowane i wypełniane zgodnie z oczekiwaniami.

![Wybierz do ostatecznej kwerendy Stream Analytics tabeli](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
