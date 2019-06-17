---
title: Rozwiązywanie problemów z zapytań usługi Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z zapytań w zadaniach usługi Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7a1e440a8dc8f518e272df9e126771df54390ed5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60762484"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Rozwiązywanie problemów z zapytań usługi Azure Stream Analytics

W tym artykule opisano typowe problemy związane z tworzenia zapytań usługi Stream Analytics oraz sposób rozwiązać ten problem.

## <a name="query-is-not-producing-expected-output"></a>Zapytanie nie daje oczekiwanych danych wyjściowych 
1.  Sprawdź błędy, testując lokalnie:
    - Na **zapytania** zaznacz **testu**. Użyj pobranych przykładowych danych w celu [przetestować zapytanie](stream-analytics-test-query.md). Sprawdź błędy i ich skorygowania.   
    - Możesz również [przetestować zapytanie bezpośrednio na żywo dane wejściowe](stream-analytics-live-data-local-testing.md) przy użyciu narzędzia Stream Analytics tools for Visual Studio.

2.  Jeśli używasz [ **Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), sprawdź, czy zdarzenia mają sygnatury czasowe późniejsze niż [czas rozpoczęcia zadania](stream-analytics-out-of-order-and-late-events.md).

3.  Wyeliminuj typowych pułapek, takich jak:
    - A [ **gdzie** ](https://msdn.microsoft.com/library/azure/dn835048.aspx) klauzuli w zapytaniu odfiltrowała wszystkie zdarzenia, w uniemożliwia generowany żadnych danych wyjściowych.
    - A [ **RZUTOWANIA** ](https://msdn.microsoft.com/azure/stream-analytics/reference/cast-azure-stream-analytics) funkcja kończy się niepowodzeniem, co powoduje niepowodzenie zadania. Aby uniknąć błędów rzutowanie typu, należy użyć [ **TRY_CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/try-cast-azure-stream-analytics) zamiast tego.
    - Podczas korzystania z funkcji okna, odczekanie przez czas całe okno wyświetlić dane wyjściowe z zapytania.
    - Sygnatura czasowa zdarzeń przed godziną rozpoczęcia zadania, a w związku z tym, zdarzenia są porzucane.

4.  Upewnij się, że zdarzenie szeregowania zasad są skonfigurowane zgodnie z oczekiwaniami. Przejdź do **ustawienia** i wybierz [ **określanie kolejności zdarzeń**](stream-analytics-out-of-order-and-late-events.md). Zasady są *nie* stosowane, gdy używasz **Test** przycisk, aby przetestować zapytanie. Ten wynik jest jedną różnicą między testowaniem w przeglądarce wykonywaniem zadania w środowisku produkcyjnym. 

5. Debugowanie przy użyciu inspekcji i dzienniki diagnostyczne:
    - Użyj [dzienników inspekcji](../azure-resource-manager/resource-group-audit.md)i Filtruj, aby zidentyfikować i debugować błędy.
    - Użyj [zadania dzienniki diagnostyczne](stream-analytics-job-diagnostic-logs.md) do identyfikowania i debugować błędy.

## <a name="job-is-consuming-too-many-streaming-units"></a>Zadanie zużywa zbyt wiele jednostek przesyłania strumieniowego
Upewnij się, że możesz korzystać z przetwarzania równoległego w usłudze Azure Stream Analytics. Dowiedz się, jak [skalowania za pomocą przetwarzania równoległego zapytań](stream-analytics-parallelization.md) zadań usługi Stream Analytics, konfigurując partycji danych wejściowych i dostrajanie definicja zapytania usługi analytics.

## <a name="debug-queries-progressively"></a>Stopniowo debugowanie zapytań

W przetwarzaniu danych w czasie rzeczywistym wiedząc, jak wygląda danych w trakcie wykonywania zapytania mogą być pomocne. Ponieważ dane wejściowe lub kroki zadania usługi Azure Stream Analytics może zostać odczytany wielokrotnie, można napisać dodatkowych instrukcji SELECT INTO. Wykonanie tej czynności wysyła pośrednie dane do magazynu co pozwala sprawdzić poprawność danych, podobnie jak *Obserwuj zmienne* czy podczas debugowania programu.

Poniższe przykładowe zapytanie zadania usługi Azure Stream Analytics ma jeden strumień danych wejściowych, dwa wejścia danych referencyjnych i dane wyjściowe do usługi Azure Table Storage. Zapytanie łączy dane z Centrum zdarzeń i odwołanie do dwóch obiektów blob, można pobrać informacji o nazwie i kategorii:

![Przykładowe zapytanie Stream Analytics SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Należy pamiętać, że zadanie zostało uruchomione, ale żadne zdarzenia nie jest generowany w danych wyjściowych. Na **monitorowanie** kafelka, pokazano tutaj widać, że dane wejściowe jest tworzenie danych, ale nie wiesz, który krok **Dołącz** spowodowane wszystkich zdarzeń, które ma zostać przerwane.

![Kafelek monitorowanie usługi Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
W takiej sytuacji można dodać kilka dodatkowych instrukcji SELECT INTO "logowania" wyniki pośrednie sprzężenia i danych, które są odczytywane z danych wejściowych.

W tym przykładzie dodaliśmy dwa nowe "tymczasowe dane wyjściowe." Mogą być dowolnego ujścia, jaką chcesz. Tutaj używamy usługi Azure Storage, na przykład:

![Dodawanie dodatkowych instrukcji SELECT INTO do zapytania usługi Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Następnie można ponownie napisać zapytanie następująco:

![Nowych zapytań wybierz do Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Teraz ponownie uruchomić zadanie i pozwolić mu działać przez kilka minut. Następnie utworzyć zapytanie względem temp1 i temp2 za pomocą programu Visual Studio Cloud Explorer do produkcji w poniższych tabelach:

**Tabela temp1**
![SELECT INTO temp1 usługi Stream Analytics zapytanie dotyczące tabeli](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**Tabela temp2**
![SELECT INTO temp2 usługi Stream Analytics zapytanie dotyczące tabeli](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Jak widać, temp1 i temp2 znajdują się dane, a w temp2 w kolumnie Nazwa została wpisana poprawnie. Jednakże ponieważ nadal nie ma danych w danych wyjściowych, coś jest nie tak:

![Polecenie SELECT INTO tabeli output1 za pomocą zapytań usługi Stream Analytics nie danych](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Przez pobieranie próbek danych, użytkownik może być prawie pewne, czy problem dotyczy drugi sprzężenia. Można pobrać danych referencyjnych z magazynu obiektów blob i zapoznaj się z:

![Polecenie SELECT INTO zapytań usługi Stream Analytics tabeli referencyjnej](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Jak widać, format identyfikatora GUID w tymi danymi referencyjnymi różni się od formatu [z] kolumny temp2. Dlatego dane nie dotrze do output1, zgodnie z oczekiwaniami.

Można naprawić format danych, przekaż go do odwoływać się do obiektu blob, a następnie spróbuj ponownie:

![SELECT INTO zapytania usługi Stream Analytics tabeli tymczasowej](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Tym razem danych w danych wyjściowych jest sformatowany i wypełniane zgodnie z oczekiwaniami.

![Zapytanie usługi Stream Analytics tabeli końcowej SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)