---
title: Przewodnik rozwiązywania problemów dla usługi Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z zadań usługi Azure Stream Analytics, połączeń, danych wejściowych, danych wyjściowych, zapytań i danych.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: b1b5d0af3f2b149959bcb97ddaf29ba2fe1f4668
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702225"
---
# <a name="troubleshooting-guide-for-azure-stream-analytics"></a>Przewodnik rozwiązywania problemów dla usługi Azure Stream Analytics

Rozwiązywanie problemów z usługi Azure Stream Analytics może okazać się złożona nakładu pracy na pierwszy rzut oka. Po zakończeniu pracy z wieloma użytkownikami, utworzyliśmy tego przewodnika, aby usprawnić proces i Usuń wątpliwości dotyczące danych wejściowych, danych wyjściowych, zapytań i funkcje.

## <a name="troubleshoot-your-stream-analytics-job"></a>Rozwiązywanie problemów z zadania usługi Stream Analytics

Aby uzyskać najlepsze wyniki w rozwiązaniu zadania usługi Stream Analytics skorzystaj z poniższych wskazówek:

1.  Przetestuj połączenie:
    - Sprawdź łączność z wejściami i wyjściami przy użyciu **Testuj połączenie** przycisku dla wszystkich danych wejściowych i wyjściowych.

2.  Sprawdź dane wejściowe:
    - Aby sprawdzić, czy dane wejściowe przepływają do Centrum zdarzeń, użyj [Eksploratora usługi Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) połączyć się z usługi Azure Event Hub (Jeśli używane są wejścia Centrum zdarzeń).  
    - Użyj [ **przykładowych danych** ](stream-analytics-sample-data-input.md) znajdujący się na każdym dane wejściowe i Pobierz przykładowe dane wejściowe.
    - Sprawdź przykładowe dane, aby zrozumieć układ danych: schemat i [typy danych](https://msdn.microsoft.com/library/azure/dn835065.aspx).

3.  Testowanie zapytania:
    - Na **zapytania** kartę, należy użyć **Test** przycisk, aby przetestować zapytanie i użyj pobranych przykładowych danych w celu [przetestować zapytanie](stream-analytics-test-query.md). Sprawdź błędy i ich skorygowania.
    - Jeśli używasz [ **Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), sprawdź, czy zdarzenia mają sygnatury czasowe późniejsze niż [czas rozpoczęcia zadania](stream-analytics-out-of-order-and-late-events.md).

4.  Debugowanie zapytania:
    - Za pomocą procedury, należy ponownie utworzyć zapytanie, rozpoczynając od prostej instrukcji select do bardziej złożonych agregacji. Do zbudowania krok po kroku logiki zapytania, należy użyć [WITH](https://msdn.microsoft.com/library/azure/dn835049.aspx) klauzul.
    - Użyj [SELECT INTO](stream-analytics-select-into.md) debugowania kroki zapytania.

5.  Wyeliminuj typowych pułapek, takich jak:
    - A [ **gdzie** ](https://msdn.microsoft.com/library/azure/dn835048.aspx) klauzuli w zapytaniu odfiltrowała wszystkie zdarzenia, w uniemożliwia generowany żadnych danych wyjściowych.
    - A [ **RZUTOWANIA** ](https://msdn.microsoft.com/azure/stream-analytics/reference/cast-azure-stream-analytics) funkcja kończy się niepowodzeniem, co powoduje niepowodzenie zadania. Aby uniknąć błędów rzutowanie typu, należy użyć [ **TRY_CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/try-cast-azure-stream-analytics) zamiast tego.
    - Podczas korzystania z funkcji okna, odczekanie przez czas całe okno wyświetlić dane wyjściowe z zapytania.
    - Sygnatura czasowa zdarzeń przed godziną rozpoczęcia zadania, a w związku z tym, zdarzenia są porzucane.

6.  Użyj określanie kolejności zdarzeń:
    - Jeśli poprzednie kroki działały prawidłowo, przejdź do strony **ustawienia** bloku, a następnie wybierz pozycję [ **określanie kolejności zdarzeń**](stream-analytics-out-of-order-and-late-events.md). Sprawdź, czy ta zasada jest skonfigurowana dla co ma sens w danym scenariuszu. Zasady są *nie* stosowane, gdy używasz **Test** przycisk, aby przetestować zapytanie. Ten wynik jest jedną różnicą między testowaniem w przeglądarce wykonywaniem zadania w środowisku produkcyjnym.

7.  Debugowanie przy użyciu metryk:
    - Jeśli żadne dane wyjściowe można uzyskać po określonym czasie trwania (na podstawie zapytania), spróbuj wykonać następujące czynności:
        - Przyjrzyj się [ **metryki monitorowania** ](stream-analytics-monitoring.md) na **Monitor** kartę. Ponieważ wartości są agregowane, metryki są opóźnione o kilka minut.
            - Jeśli zdarzenia wejściowe > 0, zadanie jest w stanie odczytać dane wejściowe. Jeśli zdarzeń wejściowych nie jest > 0, wtedy:
                - Aby sprawdzić, czy źródło danych zawiera prawidłowe dane, należy sprawdzić je za pomocą [Eksploratora usługi Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Ta sprawdzenie jest stosowane, jeśli zadanie używa Centrum zdarzeń jako dane wejściowe.
                - Sprawdź, czy format serializacji danych i kodowanie są zgodne z oczekiwaniami.
                - Jeśli zadanie używa Centrum zdarzeń, sprawdź, czy treść komunikatu jest *Null*.
            - Jeśli błędy konwersji danych > 0 i rośnie, następujące może mieć wartość true:
                - Zadania nie można deserializować zdarzeń.
                - Schemat zdarzeń mogą być niezgodne ze schematem zdefiniowanego lub oczekiwanego zdarzenia w zapytaniu.
                - Typy danych niektórych pól w zdarzeniu może być niezgodny oczekiwania.
            - Jeśli błędy czasu wykonywania > 0, oznacza to, że zadanie może odbierać dane, ale generuje błędy podczas przetwarzania zapytania.
                - Aby znaleźć błędy, przejdź do [dzienników inspekcji](../azure-resource-manager/resource-group-audit.md) i*odfiltrować* stanu.
            - Jeśli liczba > 0, a liczba zdarzeń wyjściowych = 0, oznacza to, że jest spełniony jeden z następujących czynności:
                - W wyniku przetwarzania zapytania nie zostało zwrócone żadne zdarzenie wyjściowe.
                - Zdarzenia lub ich pola mogą być źle sformułowane w efekcie zero wynik po zakończeniu przetwarzania zapytania.
                - Zadanie nie mógł wypychania danych do [ujścia danych wyjściowych](stream-analytics-select-into.md) ze względów łączności lub uwierzytelniania.
        - We wszystkich przypadkach błąd wspomniano wcześniej, komunikaty dziennika operacji zawierają dodatkowe szczegóły (włącznie, co dzieje się), z wyjątkiem przypadków, gdy logika zapytania odfiltrowała wszystkie zdarzenia. Jeśli przetwarzanie wielu zdarzeń generuje błędy, Stream Analytics rejestruje pierwsze trzy komunikaty tego samego typu w ciągu 10 minut do dzienników operacji. Następnie pomija dodatkowe identyczne błędy z komunikatem "Błędy występują zbyt często są one pomijane."

8. Debugowanie przy użyciu inspekcji i dzienniki diagnostyczne:
    - Użyj [dzienników inspekcji](../azure-resource-manager/resource-group-audit.md)i Filtruj, aby zidentyfikować i debugować błędy.
    - Użyj [zadania dzienniki diagnostyczne](stream-analytics-job-diagnostic-logs.md) do identyfikowania i debugować błędy.

9. Sprawdź dane wyjściowe:
    - Jeśli stan zadania to *systemem*, w zależności od czasu trwania, które jest określone w zapytaniu, można zobaczyć wyniki w źródle danych ujścia.
    - Jeśli nie widzisz danych wyjściowych, które mają wyjścia określonego typu, należy przekierowywać je do typu danych wyjściowych, który jest mniej skomplikowany, takich jak konto usługi Azure Blob. Za pomocą Eksploratora usługi Storage, sprawdź, czy dane wyjściowe są widoczne. Ponadto sprawdź, czy limity ograniczania w danych wyjściowych uniemożliwiają dane otrzymywane.

10. Użyj analizy przepływu danych metryki diagram zadania:
    - Aby analizować przepływ danych i zidentyfikować problemy, użyj [diagram zadania za pomocą metryk](stream-analytics-job-diagram-with-metrics.md).

11. Otwórz zgłoszenie do pomocy technicznej:
    - Ponadto jeśli wszystko inne nie powiedzie się, otwórz zgłoszenie do pomocy technicznej firmy Microsoft przy użyciu identyfikatora subskrypcji, która zawiera zadania.

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
