---
title: Rozwiązywanie problemów z danymi wejściowymi usługi Azure Stream Analytics
description: W tym artykule opisano techniki rozwiązywania problemów z połączeniami wejściowymi w zadaniach usługi Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 3d88123b3dd79e5707c5c19cbbae13c30cbdeb84
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409411"
---
# <a name="troubleshoot-input-connections"></a>Rozwiązywanie problemów z połączeniami wejściowymi

W tym artykule opisano typowe problemy z połączeniami wejściowymi usługi Azure Stream Analytics, jak rozwiązywać problemy z wprowadzaniem danych i jak rozwiązać problemy. Wiele kroków rozwiązywania problemów wymaga włączenia dzienników diagnostycznych dla zadania usługi Usługi Stream Analytics. Jeśli nie masz włączonych dzienników diagnostycznych, zobacz [Rozwiązywanie problemów z usługą Azure Stream Analytics przy użyciu dzienników diagnostycznych](stream-analytics-job-diagnostic-logs.md).

## <a name="input-events-not-received-by-job"></a>Zdarzenia wejściowe nie odebrane przez zadanie 

1.  Przetestuj łączność wejściową i wyjściową. Sprawdź łączność z wejściami i wyjściami za pomocą przycisku **Testuj połączenie** dla każdego wejścia i wyjścia.

2.  Sprawdź dane wejściowe.

    1. Użyj przycisku [**Przykładowe dane**](stream-analytics-sample-data-input.md) dla każdego wejścia. Pobierz dane przykładowe.
        
    1. Sprawdź przykładowe dane, aby zrozumieć schemat i [typy danych](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).
    
    1. Sprawdź [metryki Usługi event hub,](../event-hubs/event-hubs-metrics-azure-monitor.md) aby upewnić się, że zdarzenia są wysyłane. Metryki wiadomości powinny być większe niż zero, jeśli centra zdarzeń odbierają wiadomości.

3.  Upewnij się, że w podglądzie danych wejściowych wybrano zakres czasu. Wybierz **pozycję Wybierz zakres czasu**, a następnie wprowadź przykładowy czas trwania przed przetestowaniem kwerendy.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Źle sformułowane zdarzenia wejściowe powodują błędy deserializacji 

Problemy z deserializacją są spowodowane, gdy strumień wejściowy zadania usługi Stream Analytics zawiera nieprawidłowo sformułowane komunikaty. Na przykład nieprawidłowo sformułowany komunikat może być spowodowany brakiem nawiasu lub nawiasem klamrowym w obiekcie JSON lub niepoprawnym formatem sygnatury czasowej w polu czasu. 
 
Gdy zadanie usługi Stream Analytics odbiera nieprawidłowo sformułowany komunikat z danych wejściowych, porzuca komunikat i powiadamia o tym z ostrzeżeniem. Symbol ostrzeżenia jest wyświetlany na **kafelku Wejścia** zadania usługi Stream Analytics. Następujący symbol ostrzeżenia istnieje tak długo, jak zadanie jest w stanie uruchomienia:

![Kafelek danych wejściowych usługi Azure Stream Analytics](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Włącz dzienniki diagnostyczne, aby wyświetlić szczegóły błędu i komunikat (ładunek), który spowodował błąd. Istnieje wiele powodów, dla których mogą wystąpić błędy deserializacji. Aby uzyskać więcej informacji dotyczących określonych błędów deserializacji, zobacz [Błędy danych wejściowych](data-errors.md#input-data-errors). Jeśli dzienniki diagnostyczne nie są włączone, krótkie powiadomienie będzie dostępne w witrynie Azure portal.

![Powiadomienie o ostrzeżeniu o szczegółach wprowadzenia](media/stream-analytics-malformed-events/warning-message-with-offset.png)

W przypadkach, gdy ładunek wiadomości jest większy niż 32 KB lub jest w formacie binarnym, uruchom kod CheckMalformedEvents.cs dostępny w [repozytorium próbek GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Ten kod odczytuje identyfikator partycji, przesunięcie i drukuje dane, które znajdują się w tym przesunięciu. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Zadanie przekracza maksymalną liczbę odbiorników Usługi Event Hub

Najlepszym rozwiązaniem w zakresie korzystania z centrów zdarzeń jest użycie wielu grup odbiorców do skalowalności zadań. Liczba czytników w zadaniu usługi Stream Analytics dla określonego wkładu wpływa na liczbę czytelników w jednej grupie odbiorców. Dokładna liczba odbiorników jest oparta na wewnętrznych szczegółach implementacji logiki topologii skalowawki w poziomie i nie jest narażona zewnętrznie. Liczba czytników może ulec zmianie po uruchomieniu zadania lub podczas uaktualniania zadania.

Błąd wyświetlany w przypadku przekroczenia maksymalnej liczby odbiorników to: 

`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Gdy liczba czytników zmienia się podczas uaktualniania zadania, ostrzeżenia przejściowe są zapisywane w dziennikach inspekcji. Zadania usługi Stream Analytics automatycznie odzyskują siły po tych przejściowych problemach.

### <a name="add-a-consumer-group-in-event-hubs"></a>Dodawanie grupy odbiorców w centrach zdarzeń

Aby dodać nową grupę odbiorców w wystąpieniu usługi Event Hubs, wykonaj następujące kroki:

1. Zaloguj się do Portalu Azure.

2. Znajdź Centrum zdarzeń.

3. Wybierz **pozycję Centra zdarzeń** pod nagłówkiem **Jednostki.**

4. Wybierz Centrum zdarzeń według nazwy.

5. Na stronie **Wystąpienie centrów zdarzeń** w nagłówku **Jednostki** wybierz pozycję **Grupy odbiorców**. Na liście znajduje się grupa odbiorców o **nazwie $Default.**

6. Wybierz **+ Grupa odbiorców,** aby dodać nową grupę odbiorców. 

   ![Dodawanie grupy odbiorców w centrach zdarzeń](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Podczas tworzenia danych wejściowych w zadaniu usługi Stream Analytics, aby wskazać Centrum zdarzeń, określono tam grupę odbiorców. **$Default** jest używany, gdy nie jest określony. Po utworzeniu nowej grupy odbiorców edytuj dane wejściowe Centrum zdarzeń w zadaniu usługi Stream Analytics i określ nazwę nowej grupy odbiorców.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Czytniki na partycję przekraczają limit centrów zdarzeń

Jeśli składnia kwerendy przesyłania strumieniowego odwołuje się do tego samego wejściowego zasobu Centrum zdarzeń wiele razy, aparat zadań może używać wielu czytników na kwerendę z tej samej grupy odbiorców. Gdy istnieje zbyt wiele odwołań do tej samej grupy konsumentów, zadanie może przekroczyć limit pięciu i wyrzucił błąd. W takich okolicznościach można dodatkowo podzielić przy użyciu wielu danych wejściowych w wielu grupach odbiorców przy użyciu rozwiązania opisanego w poniższej sekcji. 

Scenariusze, w których liczba czytników na partycję przekracza limit centrów zdarzeń wynoszący pięć, są następujące:

* Wiele instrukcji SELECT: Jeśli używasz wielu instrukcji SELECT, które odwołują się do **tego samego** wejścia centrum zdarzeń, każda instrukcja SELECT powoduje utworzenie nowego odbiornika.

* UNIA: Gdy używasz UNII, istnieje możliwość wielu danych wejściowych, które odnoszą się do **tego samego** centrum zdarzeń i grupy konsumentów.

* SELF JOIN: Podczas korzystania z operacji SELF JOIN, można odwoływać się do **tego samego** centrum zdarzeń wiele razy.

Poniższe najlepsze rozwiązania mogą pomóc w łagodzeniu scenariuszy, w których liczba czytników na partycję przekracza limit centrów zdarzeń wynoszący pięć.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Podziel zapytanie na wiele kroków przy użyciu klauzuli WITH

Klauzula WITH określa tymczasowy nazwany zestaw wyników, do którego można odwoływać się klauzula FROM w kwerendzie. Zdefiniowanie klauzuli WITH w zakresie wykonywania pojedynczej instrukcji SELECT.

Na przykład zamiast tej kwerendy:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Użyj tej kwerendy:

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

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Upewnij się, że dane wejściowe wiążą się z różnymi grupami konsumentów

W przypadku kwerend, w których trzy lub więcej danych wejściowych są połączone z tej samej grupy konsumentów Centrum zdarzeń, należy utworzyć oddzielne grupy odbiorców. Wymaga to utworzenia dodatkowych danych wejściowych usługi Stream Analytics.

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, wypróbuj nasze [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
