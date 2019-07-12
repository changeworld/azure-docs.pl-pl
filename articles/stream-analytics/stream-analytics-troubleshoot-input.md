---
title: Rozwiązywanie problemów z danych wejściowych dla usługi Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z połączeniami danych wejściowych w zadań usługi Azure Stream Analytics.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 8357a53ee065812922b5df53fbdef7c14e5f0ff7
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621024"
---
# <a name="troubleshoot-input-connections"></a>Rozwiązywanie problemów z połączeniami danych wejściowych

Ta strona zawiera opis typowych problemów z połączeniami danych wejściowych i sposób rozwiązać ten problem.

## <a name="input-events-not-received-by-job"></a>Zdarzenia wejściowe nie są odbierane przez zadania 
1.  Przetestuj połączenie. Sprawdź łączność z wejściami i wyjściami przy użyciu **Testuj połączenie** przycisku dla wszystkich danych wejściowych i wyjściowych.

2.  Sprawdź dane wejściowe.

    Aby sprawdzić, czy dane wejściowe przepływają do Centrum zdarzeń, użyj [Eksploratora usługi Service Bus](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) połączyć się z usługi Azure Event Hub (Jeśli używane są wejścia Centrum zdarzeń).
        
    Użyj [ **przykładowych danych** ](stream-analytics-sample-data-input.md) znajdujący się na każdym dane wejściowe i Pobierz przykładowe dane wejściowe.
        
    Sprawdź przykładowe dane, aby zrozumieć układ danych: schemat i [typy danych](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="malformed-input-events-causes-deserialization-errors"></a>Błędy deserializacji powoduje, że źle sformułowane zdarzenia wejściowe 
Deserializacji problemy są spowodowane, gdy strumień wejściowy zadania usługi Stream Analytics zawiera źle sformułowane komunikaty. Na przykład nieprawidłowo sformułowany komunikat może być spowodowane przez brak nawiasu lub nawiasów w obiekcie JSON lub format sygnatury czasowej niepoprawne, w polu czas. 
 
Gdy zadanie usługi Stream Analytics otrzymuje nieprawidłowo sformułowany komunikat z danych wejściowych, porzuca wiadomość i powiadamia użytkownika z ostrzeżeniem. Symbol ostrzeżenia jest wyświetlany na **dane wejściowe** Kafelek zadania usługi Stream Analytics. Znak to ostrzeżenie występuje, tak długo, jak długo zadanie jest w stanie uruchomienia:

![Kafelek usługi Azure Stream Analytics danych wejściowych.](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Włącz dzienniki diagnostyki wyświetlić szczegóły ostrzeżenia. Źle sformułowane zdarzenia wejściowe dzienniki wykonywania zawiera wpis z komunikat, który wygląda następująco: 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>Co spowodowało błąd deserializacji
Można wykonać poniższe kroki, aby analizować zdarzenia wejściowe szczegółowo zapoznanie co spowodowało błąd deserializacji. Następnie można ustalić źródła zdarzeń do generowania zdarzeń w odpowiednim formacie, aby zapobiec osiągnięciu ten problem, ponownie.

1. Przejdź do kafelka danych wejściowych i kliknij pozycję symbole ostrzeżenia, aby zapoznać się z listą problemów.

2. Na kafelku dane wejściowe Wyświetla listę ostrzeżeń ze szczegółowymi informacjami o poszczególnych problemów. Przykładowy komunikat ostrzegawczy poniżej zawiera partycję, przesunięcie i numery sekwencyjne w przypadku, gdy są źle sformułowane dane JSON. 

   ![Stream Analytics komunikat ostrzegawczy z przesunięciem](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Aby znaleźć dane JSON z niepoprawny format, uruchom dostępne w kodzie CheckMalformedEvents.cs [repozytorium przykładów usługi GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Ten kod odczytuje identyfikator partycji: przesunięcie i drukuje dane, które znajdują się w tym przesunięciu. 

4. Po odczytaniu danych możesz przeanalizować i poprawić format serializacji.

5. Możesz również [odczytywać zdarzenia z usługi IoT Hub za pomocą Eksploratora usługi Service Bus](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Zadania przekracza maksymalny odbiorcy usługi Event Hubs
Najlepszym rozwiązaniem jest dotyczące korzystania z usługi Event Hubs jest na potrzeby zapewnienia skalowalności zadania przez wiele grup odbiorców. Liczbę czytników w zadaniu Stream Analytics określone dane wejściowe wpływa na liczbę czytników w grupie jednego konsumenta. Dokładną liczbę odbiorników opiera się na szczegóły wewnętrznej implementacji logiki topologii skalowalnego w poziomie i nie jest widoczna zewnętrznie. Po uruchomieniu zadania lub podczas uaktualniania zadania, można zmienić liczbę czytników.

Błąd pokazany, gdy przekracza wartość maksymalną liczbę odbiorników to: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Po zmianie podczas uaktualniania zadania liczbę czytników przejściowy ostrzeżenia są zapisywane do dzienników inspekcji. Zadania usługi Stream Analytics automatycznie odzyskać te problemy przejściowe.

### <a name="add-a-consumer-group-in-event-hubs"></a>Dodaj grupę odbiorców w usłudze Event Hubs
Aby dodać nową grupę odbiorców w wystąpieniu usługi Event Hubs, wykonaj następujące kroki:

1. Zaloguj się do Portalu Azure.

2. Znajdź usługi Event Hubs.

3. Wybierz **usługi Event Hubs** w obszarze **jednostek** nagłówka.

4. Wybierz Centrum zdarzeń według nazwy.

5. Na **wystąpienie usługi Event Hubs** w obszarze **jednostek** nagłówka, wybierz **grupy konsumentów**. Grupa konsumentów o nazwie **$Default** znajduje się na liście.

6. Wybierz **+ Grupa odbiorców** można dodać nowe grupy konsumentów. 

   ![Dodaj grupę odbiorców w usłudze Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Po utworzeniu danych wejściowych w ramach zadania usługi Stream Analytics, aby wskazać Centrum zdarzeń jest określona grupa odbiorców. $Default jest używany, gdy nie jest określona. Po utworzeniu nowej grupy konsumentów, edytować danych wejściowych Centrum zdarzeń w ramach zadania usługi Stream Analytics i określ nazwę nowej grupy odbiorców.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Czytelnicy dla każdej partycji przekracza limit usługi Event Hubs

Jeśli przesyłania strumieniowego składnia zapytania odwołuje się do tego samego zasobu Centrum zdarzeń wejściowych wiele razy, aparat zadania można użyć wielu elementów odczytujących na zapytanie z tej samej grupy odbiorców. W przypadku zbyt wiele odwołań do tej samej grupy odbiorców może przekroczyć limit pięciu, zadania i zgłoszony błąd. W tych okolicznościach można dalej podzielić przy użyciu wielu danych wejściowych przez wiele grup odbiorców przy użyciu rozwiązania opisane w poniższej sekcji. 

Następujące scenariusze, w których liczbę czytników w jednej partycji przekracza limit usługi Event Hubs do 5:

* Wiele instrukcji SELECT: Jeśli użyjesz wielu instrukcji SELECT, które odwołują się do **tego samego** Centrum zdarzeń do wprowadzania, każda instrukcja SELECT powoduje, że nowy odbiornik ma zostać utworzony.
* UNII: Gdy używasz Unii, istnieje możliwość mają wielu danych wejściowych, które odwołują się do **tego samego** grupy Centrum i odbiorcę zdarzeń.
* SELF JOIN: Korzystając z operacją DOŁĄCZYĆ SAMODZIELNIE, jest możliwe do odwoływania się do **tego samego** Centrum zdarzeń wiele razy.

Poniższe najlepsze rozwiązania może pomóc zmniejszyć scenariusze, w których liczbę czytników w jednej partycji przekracza limit usługi Event Hubs do 5.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Dzielenie zapytanie na wiele kroków za pomocą klauzuli WITH

Klauzula WITH Określa tymczasowy nazwany zestaw wyników, które mogą być przywoływane przez klauzuli FROM w kwerendzie. Należy zdefiniować klauzuli WITH w zakresie wykonywania pojedynczej instrukcji SELECT.

Na przykład, zamiast tego zapytania:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Skorzystaj z tej kwerendy:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Upewnij się, że dane wejściowe powiązać z różnych grup konsumenckich

Dla zapytań, w których co najmniej trzech danych wejściowych są podłączone do tej samej grupy konsumentów usługi Event Hubs należy utworzyć grupy konsumentów oddzielne. Ta migracja wymaga utworzenia dodatkowych danych wejściowych usługi Stream Analytics.

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
