---
title: Rozwiązywanie problemów z zapytaniami usługi Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z zapytaniami w zadaniach usługi Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: f049dc6d1261a8201cf79d1779e522b30d13c4b0
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409444"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Rozwiązywanie problemów z zapytaniami usługi Azure Stream Analytics

W tym artykule opisano typowe problemy z tworzeniem zapytań usługi Stream Analytics i rozwiązywaniem ich problemów.

W tym artykule opisano typowe problemy z tworzeniem zapytań usługi Azure Stream Analytics, jak rozwiązywać problemy z zapytaniami i jak rozwiązać problemy. Wiele kroków rozwiązywania problemów wymaga włączenia dzienników diagnostycznych dla zadania usługi Usługi Stream Analytics. Jeśli nie masz włączonych dzienników diagnostycznych, zobacz [Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych](stream-analytics-job-diagnostic-logs.md).

## <a name="query-is-not-producing-expected-output"></a>Kwerenda nie wytwarza oczekiwanych danych wyjściowych

1.  Sprawdź błędy, testując lokalnie:

    - W witrynie Azure portal na karcie **Zapytanie** wybierz pozycję **Testuj**. Użyj pobranych przykładowych danych, aby [przetestować kwerendę](stream-analytics-test-query.md). Sprawdź wszelkie błędy i spróbuj je poprawić.   
    - Można również [przetestować kwerendę lokalnie](stream-analytics-live-data-local-testing.md) przy użyciu narzędzi usługi Azure Stream Analytics dla programu Visual Studio lub [Visual Studio Code.](visual-studio-code-local-run-live-input.md) 

2.  [Kwerendy debugowania krok po kroku lokalnie przy użyciu diagramu zadań](debug-locally-using-job-diagram.md) w narzędziach usługi Azure Stream Analytics dla programu Visual Studio. Diagram zadań pokazuje, jak przepływy danych ze źródeł wejściowych (Centrum zdarzeń, Centrum IoT itp.) za pośrednictwem wielu kroków kwerendy i na koniec do ujścia danych wyjściowych. Każdy krok kwerendy jest mapowany na tymczasowy zestaw wyników zdefiniowany w skrypcie przy użyciu instrukcji WITH. Można wyświetlić dane, a także metryki, w każdym zestawie wyników pośrednich, aby znaleźć źródło problemu.

    ![Wynik podglądu diagramu zadania](./media/debug-locally-using-job-diagram/preview-result.png)

3.  Jeśli używasz [**sygnatury czasowej przez**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), sprawdź, czy zdarzenia mają sygnatury czasowe większe niż [czas rozpoczęcia zadania](stream-analytics-out-of-order-and-late-events.md).

4.  Wyeliminuj typowe pułapki, takie jak:
    - Klauzula [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) w kwerendzie odfiltrowała wszystkie zdarzenia, uniemożliwiając wygenerowanie danych wyjściowych.
    - Funkcja [**CAST**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) kończy się niepowodzeniem, powodując niepowodzenie zadania. Aby uniknąć błędów rzutowanie typu, należy użyć [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics) zamiast tego.
    - Korzystając z funkcji okna, poczekaj na cały czas trwania okna, aby zobaczyć dane wyjściowe z kwerendy.
    - Sygnatura czasowa dla zdarzeń poprzedza czas rozpoczęcia zadania i zdarzenia są usuwane.
    - [**Warunki JOIN**](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) nie są zgodne. Jeśli nie ma żadnych dopasowań, nie będzie zero danych wyjściowych.

5.  Upewnij się, że zasady zamawiania zdarzeń są skonfigurowane zgodnie z oczekiwaniami. Przejdź do **pozycji Ustawienia** i wybierz pozycję [**Kolejność zdarzeń**](stream-analytics-out-of-order-and-late-events.md). Zasady *nie* są stosowane podczas korzystania z przycisku **Testuj,** aby przetestować kwerendę. Ten wynik jest jedną różnicą między testowaniem w przeglądarce a uruchamianiem zadania w produkcji. 

6. Debugowanie przy użyciu dzienników inspekcji i diagnostyki:
    - Użyj [dzienników inspekcji](../azure-resource-manager/resource-group-audit.md)i filtruj, aby zidentyfikować i debugować błędy.
    - Dzienniki [diagnostyczne zadań służy](stream-analytics-job-diagnostic-logs.md) do identyfikowania i debugowania błędów.

## <a name="resource-utilization-is-high"></a>Wykorzystanie zasobów jest wysokie

Upewnij się, że korzystasz z równoległości w usłudze Azure Stream Analytics. Możesz nauczyć się [skalować za pomocą równoległości zapytań](stream-analytics-parallelization.md) zadań usługi Stream Analytics, konfigurując partycje wejściowe i dostrajając definicję zapytania analitycznego.

## <a name="debug-queries-progressively"></a>Debug queries progressively (Stopniowe debugowanie zapytań)

W przetwarzaniu danych w czasie rzeczywistym, wiedząc, jak dane wyglądają w środku kwerendy może być pomocne. Widać to przy użyciu diagramu zadań w programie Visual Studio. Jeśli nie masz programu Visual Studio, można podjąć dodatkowe kroki w celu wytłkować dane pośrednie.

Ponieważ dane wejściowe lub kroki zadania usługi Azure Stream Analytics można odczytać wiele razy, można napisać dodatkowe instrukcje SELECT INTO. W ten sposób wyprowadza dane pośrednie do magazynu i pozwala sprawdzić poprawność danych, podobnie jak *zmienne zegarka* zrobić podczas debugowania programu.

Poniższa przykładowa kwerenda w zadaniu usługi Azure Stream Analytics ma jedno dane wejściowe strumienia, dwa dane wejściowe i dane wyjściowe do usługi Azure Table Storage. Kwerenda łączy dane z Centrum zdarzeń i dwa obiekty blob odwołania, aby uzyskać informacje o nazwie i kategorii:

![Przykładowa kwerenda usługi Stream Analytics SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Należy zauważyć, że zadanie jest uruchomione, ale żadne zdarzenia są produkowane w danych wyjściowych. Na kafelku **Monitorowanie,** pokazane w tym miejscu, widać, że dane wejściowe jest tworzenie danych, ale nie wiesz, który krok **JOIN** spowodował wszystkie zdarzenia do upuszczenia.

![Kafelek Monitorowania usługi Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

W tej sytuacji można dodać kilka dodatkowych select into instrukcji do "dziennika" pośrednie wyniki JOIN i dane, które są odczytywane z danych wejściowych.

W tym przykładzie dodaliśmy dwa nowe "tymczasowe dane wyjściowe". Mogą to być dowolne zlewozmywaki. W tym miejscu używamy usługi Azure Storage jako przykładu:

![Dodawanie dodatkowych instrukcji SELECT INTO do kwerendy usługi Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Następnie można przepisać kwerendę w ten sposób:

![Przepisana kwerenda SELECT INTO Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Teraz ponownie rozpocznij pracę i pozwól jej uruchomić przez kilka minut. Następnie kwerendy temp1 i temp2 z Visual Studio Cloud Explorer do tworzenia następujących tabel:

**temp1 tabela**
![WYBIERZ DO tabeli temp1 Kwerenda Usługi Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 tabela**
![WYBIERZ DO tabeli temp2 Kwerenda Usługi Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Jak widać, temp1 i temp2 mają dane, a kolumna nazwa jest wypełniana poprawnie w temp2. Jednak ponieważ nadal nie ma danych w danych wyjściowych, coś jest nie tak:

![WYBIERZ tabelę danych wyjściowych1 bez kwerendy usługi Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Pobierając próbki danych, można mieć prawie pewność, że problem jest z drugim JOIN. Możesz pobrać dane referencyjne z obiektu blob i spojrzeć:

![WYBIERZ DO tabeli ref Kwerenda usługi Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Jak widać, format identyfikatora GUID w tych danych referencyjnych różni się od formatu kolumny [od] w temp2. Dlatego dane nie dotarły do danych wyjściowych1 zgodnie z oczekiwaniami.

Możesz naprawić format danych, przekazać go do obiektu blob odniesienia i spróbować ponownie:

![SELECT INTO temp table Stream Analytics query SELECT INTO temp table Stream Analytics query SELECT INTO temp table Stream Analytics query SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Tym razem dane w danych wyjściowych są formatowane i wypełniane zgodnie z oczekiwaniami.

![WYBIERZ DO tabeli końcowej kwerendy usługi Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, wypróbuj nasze [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
