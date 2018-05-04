---
title: Rozwiązywanie problemów z odbiorników Centrum zdarzeń w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób korzystania z wielu grup odbiorców do usługi Event Hubs dane wejściowe zadania usługi analiza strumienia.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: aaa8c4e8d273b44f453d3f63f0be1d4baf980649
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="troubleshoot-event-hub-receivers-in-azure-stream-analytics"></a>Rozwiązywanie problemów z odbiorników Centrum zdarzeń w usłudze Azure Stream Analytics

Azure Event Hubs Azure Stream Analytics umożliwia pozyskiwania lub wysyłania danych z zadania. Najlepsze rozwiązanie dotyczące korzystania z usługi Event Hubs ma używać wielu grup odbiorców, w celu zapewnienia skalowalności zadania. Jedną z przyczyn jest to, że liczbę czytników w zadaniu Stream Analytics dla określonych danych wejściowych wpływa na liczbę czytników w grupie jednego konsumenta. Dokładne liczby odbiorników opiera się na szczegóły wewnętrznej implementacji logiki topologii skalowalnego w poziomie. Liczba odbiorcy nie jest uwidaczniana zewnętrznie. Podczas uruchamiania zadania lub podczas uaktualniania zadania, można zmienić liczbę czytników.

Błąd wyświetlany, gdy liczba odbiorniki przekracza maksymalną liczbę jest: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Po zmianie podczas uaktualniania zadania liczbę czytników przejściowej ostrzeżenia są zapisywane dzienniki inspekcji. Zadania usługi analiza strumienia automatycznie odzyskać te przejściowych problemów.

## <a name="add-a-consumer-group-in-event-hubs"></a>Dodaj grupy odbiorców w usłudze Event Hubs
Aby dodać nowe grupy odbiorców w wystąpieniu usługi Event Hubs, wykonaj następujące kroki:

1. Zaloguj się do Portalu Azure.

2. Zlokalizuj centrów zdarzeń.

3. Wybierz **usługi Event Hubs** w obszarze **jednostek** nagłówka.

4. Wybierz Centrum zdarzeń według nazwy.

5. Na **wystąpienia centra zdarzeń** w obszarze **jednostek** nagłówek, wybierz **grupy konsumentów**. Grupy konsumentów o nazwie **$Default** ma na liście.

6. Wybierz **+ grupy odbiorców** Aby dodać nowe grupy odbiorców. 

   ![Dodaj grupy odbiorców w usłudze Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Po utworzeniu danych wejściowych w zadaniu Stream Analytics, aby wskazywał Centrum zdarzeń określono grupy odbiorców. $Default jest używany, gdy nie jest określona. Po utworzeniu nowej grupy konsumentów, edytować dane wejściowe Centrum zdarzeń w zadaniu Stream Analytics i określ nazwę nowej grupy odbiorców.


## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Liczba czytników dla każdej partycji przekracza limit usługi Event Hubs pięć

Jeśli przesyłania strumieniowego Składnia kwerendy odwołuje się do tego samego zasobu Centrum zdarzeń wejściowych wiele razy, aparat zadania można użyć wielu czytników dla kwerendy z tej samej grupy odbiorców. Jeśli ma zbyt wiele odwołań do tej samej grupy konsumentów, zadanie może przekroczyć limit pięć i Zgłoszono błąd. W tych sytuacjach można dalej podzielić przy użyciu wielu danych wejściowych w przypadku wielu grup odbiorców, za pomocą rozwiązania opisane w poniższej sekcji. 

Następujące scenariusze, w których liczba czytników dla każdej partycji przekracza limit usługi Event Hubs pięć:

* Wiele instrukcji "SELECT": użycie wielu instrukcji SELECT, które odwołują się do **tego samego** wprowadzania Centrum zdarzeń, każda instrukcja SELECT powoduje, że nowe odbiornika ma zostać utworzony.
* Unia: Użycie Unii, jest możliwe wielu danych wejściowych, które odwołują się do **tego samego** grupy koncentratora i odbiorców zdarzeń.
* SAMOSPRZĘŻENIE: Korzystając z operacją JOIN SAMOOBSŁUGOWEGO, jest możliwe do odwoływania się do **tego samego** Centrum zdarzeń wiele razy.

## <a name="solution"></a>Rozwiązanie

Następujące najlepsze rozwiązania może pomóc ograniczyć scenariusze, w których liczba czytników dla każdej partycji przekracza limit usługi Event Hubs pięć.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Podziel zapytanie na wielu kroków przy użyciu klauzuli WITH

Klauzula WITH Określa tymczasowy nazwany zestaw wyników, który może odwoływać się klauzuli FROM w zapytaniu. Klauzula WITH należy zdefiniować w zakresie wykonanie jednej instrukcji SELECT.

Na przykład zamiast tego zapytania:

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Skorzystaj z tej kwerendy:

```
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

Dla zapytań, w których co najmniej trzech dane wejściowe są podłączone do tej samej grupy konsumentów centra zdarzeń należy utworzyć grupy konsumentów oddzielne. To wymaga utworzenia dodatkowych analiza strumienia danych wejściowych.


## <a name="next-steps"></a>Kolejne kroki
* [Zadania usługi analiza strumienia skali](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytania usługi analiza strumienia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
