---
title: Podręcznik rozwiązywania problemów dotyczących usługi Azure Stream Analytics
description: W tym artykule opisano technik rozwiązywania problemów z zadania usługi analiza strumienia Azure, połączeń, wejść, wyjść, zapytań i danych.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 2eefabcc0484fca0e6e3ad1dd5037684a759d010
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850450"
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Podręcznik rozwiązywania problemów dotyczących usługi Azure Stream Analytics

Rozwiązywanie problemów z usługi Azure Stream Analytics może wydawać się złożonych nakładu pracy na pierwszy rzut oka. Po zakończeniu pracy z wieloma użytkownikami, utworzono ten Przewodnik ułatwia usprawnić proces i Usuń czynności o wejść, wyjść, kwerendy i funkcje.

## <a name="troubleshoot-your-stream-analytics-job"></a>Rozwiązywanie problemów z zadania usługi analiza strumienia

Aby uzyskać najlepsze wyniki przy rozwiązywaniu zadania usługi analiza strumienia skorzystaj z poniższych wskazówek:

1.  Przetestuj połączenie:
    - Sprawdź łączność z wejściami i wyjściami przy użyciu **Testuj połączenie** przycisk dla każdego wejścia i wyjścia.

2.  Sprawdź dane wejściowe:
    - Aby sprawdzić, czy dane wejściowe jest otrzymywanych przez Centrum zdarzeń, użyj [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) do połączenia z Centrum zdarzeń Azure (Jeśli dane wejściowe Centrum zdarzeń są używane).  
    - Użyj [ **przykładowych danych** ](stream-analytics-sample-data-input.md) przycisk dla każdego danych wejściowych i Pobierz wejściowych przykładowych danych.
    - Sprawdź przykładowych danych, aby zrozumieć kształtu danych: schemat i [typy danych](https://msdn.microsoft.com/library/azure/dn835065.aspx).

3.  Testowanie kwerendy:
    - Na **zapytania** użyj **Test** przycisk, aby przetestować zapytanie i użyj pobranych przykładowych danych do [przetestować zapytanie](stream-analytics-test-query.md). Sprawdź błędy i spróbuj je poprawić.
    - Jeśli używasz [ **Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), sprawdź, czy zdarzenia mają sygnatur czasowych jest większa niż [godzina rozpoczęcia zadania](stream-analytics-out-of-order-and-late-events.md).

4.  Debugowanie kwerendy:
    - Skompiluj ponownie zapytanie stopniowo z prostej instrukcji select bardziej złożonych wartości zagregowane za pomocą kroków. Aby tworzenie logiki kwerendy krok po kroku, należy użyć [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) klauzul.
    - Użyj [SELECT INTO](stream-analytics-select-into.md) debugowania kroki zapytań.

5.  Eliminowanie typowych problemów, takich jak:
    - A [ **gdzie** ](https://msdn.microsoft.com/library/azure/dn835048.aspx) klauzula w zapytaniu odfiltrowane wszystkie zdarzenia generowane uniemożliwia żadnych danych wyjściowych.
    - A [ **RZUTOWANIA** ](https://msdn.microsoft.com/azure/stream-analytics/reference/cast-azure-stream-analytics) funkcji nie powiedzie się, co powoduje niepowodzenie zadania. Aby uniknąć niepowodzeń rzutowanie typu, należy użyć [ **TRY_CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/try-cast-azure-stream-analytics) zamiast tego.
    - Podczas korzystania z funkcji okna, poczekaj na czas trwania całe okno, aby wyświetlić dane wyjściowe z zapytania.
    - Godzina rozpoczęcia zadania poprzedza sygnaturę czasową dla zdarzeń i w związku z tym zdarzeń są usuwane.

6.  Użyj kolejności zdarzeń:
    - Jeśli wszystkie wcześniejsze kroki działał prawidłowo, przejdź do **ustawienia** bloku, a następnie wybierz [ **kolejność zdarzeń**](stream-analytics-out-of-order-and-late-events.md). Sprawdź, czy ta zasada jest skonfigurowana do co ma sens w danym scenariuszu. Zasady są *nie* użycie **Test** przycisk, aby przetestować zapytanie. Ten wynik jest jeden różnica między testowania w przeglądarce i uruchomić zadanie w środowisku produkcyjnym.

7.  Debugowanie przy użyciu metryk:
    - Po uzyskaniu żadne dane wyjściowe po oczekiwany czas trwania (na podstawie kwerendy), należy spróbować wykonać następujące czynności:
        - Przyjrzyj się [ **monitorowania metryki** ](stream-analytics-monitoring.md) na **Monitor** kartę. Ponieważ dane są agregowane, metryki opóźnienia za kilka minut.
            - Jeśli zdarzenia wejściowe > 0, zadanie jest w stanie odczytanie danych wejściowych. Jeśli dane wejściowe zdarzeń nie jest > 0, następnie:
                - Aby sprawdzić, czy źródło danych zawiera prawidłowe dane, sprawdź go za pomocą [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Ta sprawdzenie jest stosowane Jeśli zadanie używa Centrum zdarzeń jako dane wejściowe.
                - Sprawdź, czy format serializacji danych i kodowania danych są zgodne z oczekiwaniami.
                - Jeśli zadanie używa Centrum zdarzeń, sprawdź, czy treść komunikatu jest *Null*.
            - Jeśli błędy konwersji danych > 0 i typu mogą być spełnione następujące warunki:
                - Zadania nie można deserializować zdarzenia.
                - Schemat zdarzeń mogą być niezgodne schemat zdefiniowane lub oczekiwanego zdarzenia w zapytaniu.
                - Typy danych niektórych pól zdarzeń mogą być niezgodne oczekiwań.
            - Jeśli błędy podczas wykonywania > 0, jego oznacza, że zadanie może odbierać dane, ale generuje błędy podczas przetwarzania zapytania.
                - Aby znaleźć błędy, przejdź do [dzienników inspekcji](../azure-resource-manager/resource-group-audit.md) i*odfiltrować* stanu.
            - Jeśli InputEvents > 0 i OutputEvents = 0, oznacza to, że jest spełniony jeden z następujących czynności:
                - W wyniku przetwarzania zapytania nie zostało zwrócone żadne zdarzenie wyjściowe.
                - Zdarzenia lub jego pól może być nieprawidłowo uformowany, w efekcie zero wynik po zakończeniu przetwarzania zapytania.
                - Zadanie nie może przekazywaniu danych do [ujście danych wyjściowych](stream-analytics-select-into.md) łączności lub uwierzytelnianie przyczyn.
        - We wszystkich przypadkach opisanych powyżej błąd komunikaty dziennika operacji opisano dodatkowe szczegóły (w tym działania wykonywane), z wyjątkiem przypadków, w którym logiki kwerendy odfiltrowane wszystkich zdarzeń. Jeśli przetwarzanie wiele zdarzeń generuje błędy, Stream Analytics rejestruje pierwsze trzy komunikaty tego samego typu w ciągu 10 minut do dzienników operacji. Następnie pomija dodatkowe błędy identyczne z następującym komunikatem o treści "Błędy są wykonywane zbyt szybko te są pomijane."

8. Debugowanie przy użyciu inspekcji i dzienniki diagnostyczne:
    - Użyj [dzienników inspekcji](../azure-resource-manager/resource-group-audit.md)i Filtruj, aby zidentyfikować i debugowanie błędów.
    - Użyj [zadania dzienników diagnostycznych](stream-analytics-job-diagnostic-logs.md) do identyfikowania i debugowanie błędów.

9. Sprawdź dane wyjściowe:
    - Gdy stan zadania to *systemem*w oparciu o czas trwania, który określono w zapytaniu, można wyświetlić dane wyjściowe w źródle danych obiektu sink.
    - Jeśli nie widać danych wyjściowych, które mają typ określonych danych wyjściowych, przekierowanie do typu danych wyjściowych, który jest mniej złożona, takich jak obiektów Blob platformy Azure. Za pomocą Eksploratora usługi Storage, sprawdź, czy dane wyjściowe są widoczne. Ponadto należy sprawdzić, czy limity ograniczania przepustowości w danych wyjściowych uniemożliwiają danych odbierane.

10. Analiza przepływu danych za pomocą zadania diagram metryki:
    - Aby analizować przepływu danych i zidentyfikować problemy, należy użyć [diagram zadania z metryki](stream-analytics-job-diagram-with-metrics.md).

11. Otwieranie sprawy pomocy technicznej:
    - Na koniec Jeśli wszystkie inne nie powiedzie się, otwórz sprawę pomocy technicznej firmy Microsoft przy użyciu identyfikatora subskrypcji, która zawiera zadania.

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
