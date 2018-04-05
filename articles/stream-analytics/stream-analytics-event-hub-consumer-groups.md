---
title: Debugowanie Azure Stream Analytics przy użyciu odbiorcy Centrum zdarzeń | Dokumentacja firmy Microsoft
description: Zapytanie najlepsze rozwiązania dotyczące uwzględnieniu grupy konsumentów centra zdarzeń w zadania usługi analiza strumienia.
keywords: limit Centrum zdarzeń, grupy odbiorców
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 48fa5d0274549aa35e67526a758eef1f34198a6a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="debug-azure-stream-analytics-with-event-hub-receivers"></a>Debugowanie Azure Stream Analytics przy użyciu odbiorcy Centrum zdarzeń

Azure Event Hubs Azure Stream Analytics umożliwia pozyskiwania lub wysyłania danych z zadania. Najlepsze rozwiązanie dotyczące korzystania z usługi Event Hubs ma używać wielu grup odbiorców, w celu zapewnienia skalowalności zadania. Jedną z przyczyn jest to, że liczbę czytników w zadaniu Stream Analytics dla określonych danych wejściowych wpływa na liczbę czytników w grupie jednego konsumenta. Dokładne liczby odbiorników opiera się na szczegóły wewnętrznej implementacji logiki topologii skalowalnego w poziomie. Liczba odbiorcy nie jest uwidaczniana zewnętrznie. Podczas uruchamiania zadania lub podczas uaktualniania zadania, można zmienić liczbę czytników.

> [!NOTE]
> Po zmianie podczas uaktualniania zadania liczbę czytników przejściowej ostrzeżenia są zapisywane dzienniki inspekcji. Zadania usługi analiza strumienia automatycznie odzyskać te przejściowych problemów.

## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Liczba czytników dla każdej partycji przekracza limit usługi Event Hubs pięć

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


## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi analiza strumienia](stream-analytics-introduction.md)
* [Wprowadzenie do usługi analiza strumienia](stream-analytics-real-time-fraud-detection.md)
* [Zadania usługi analiza strumienia skali](stream-analytics-scale-jobs.md)
* [Dokumentacja języka zapytania usługi analiza strumienia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Strumienia Analytics management REST API reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
