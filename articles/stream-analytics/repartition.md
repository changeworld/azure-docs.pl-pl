---
title: Optymalizacja zadań usługi Azure Stream Analytics za pomocą partycjonowania
description: W tym artykule opisano, jak używać partycjonowania do optymalizacji zadań usługi Azure Stream Analytics, których nie można zrównolegli.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732386"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Użyj partycjonowania, aby zoptymalizować przetwarzanie za pomocą usługi Azure Stream Analytics

W tym artykule pokazano, jak używać partycjonowania do skalowania kwerendy usługi Azure Stream Analytics w scenariuszach, których nie można w pełni [zrównolegnąć.](stream-analytics-scale-jobs.md)

Korzystanie z [równoległości](stream-analytics-parallelization.md) może być nie możliwe, jeśli:

* Nie sterujesz kluczem partycji dla strumienia wejściowego.
* Źródło "rozpyla" dane wejściowe na wielu partycjach, które później muszą zostać scalone.

Ponowne partycjonowanie lub przetasowanie jest wymagane podczas przetwarzania danych w strumieniu, który nie jest podzielony na fragmenty zgodnie z naturalnym schematem danych wejściowych, takim jak **PartitionId** for Event Hubs. Podczas ponownej partycjonowania każdy niezależnego fragmentu mogą być przetwarzane niezależnie, co pozwala na liniowe skalowanie w poziomie potoku przesyłania strumieniowego.

## <a name="how-to-repartition"></a>Jak ponownie pogrupować

Aby podzielić na partycje, użyj słowa kluczowego **INTO** po **instrukcji PARTITION BY** w zapytaniu. W poniższym przykładzie partycje danych przez **DeviceID** do liczby partycji 10. Mieszanie **DeviceID** służy do określenia, która partycja akceptuje, który podwzłos. Dane są opróżniane niezależnie dla każdego strumienia partycjonowane, przy założeniu, że dane wyjściowe obsługuje zapisy podzielone na partycje i ma 10 partycji.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

Poniższa przykładowa kwerenda łączy dwa strumienie danych partycjonowanych. Podczas łączenia dwóch strumieni danych podzielonych na partycje strumienie muszą mieć ten sam klucz partycji i liczbę. Wynik jest strumień, który ma ten sam schemat partycji.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Schemat danych wyjściowych powinien być zgodny z kluczem schematu strumienia i liczą się, aby każdy podstrunnik mógł być opróżniany niezależnie. Strumień może być również scalane i partycjonowane ponownie przez inny schemat przed opróżnianie, ale należy unikać tej metody, ponieważ dodaje do ogólnego opóźnienia przetwarzania i zwiększa wykorzystanie zasobów.

## <a name="streaming-units-for-repartitions"></a>Jednostki przesyłania strumieniowego dla partycjonowania

Eksperymentuj i obserwuj użycie zasobów zadania, aby określić dokładną liczbę partycji, których potrzebujesz. Liczba [jednostek przesyłania strumieniowego (SU)](stream-analytics-streaming-unit-consumption.md) musi być dostosowana do zasobów fizycznych potrzebnych dla każdej partycji. Ogólnie rzecz biorąc, sześć SUs są potrzebne dla każdej partycji. Jeśli do zadania przypisano za mało zasobów, system zastosuje partycję tylko wtedy, gdy jest ono korzystne dla zadania.

## <a name="repartitions-for-sql-output"></a>Ponowne partycjoncje dla danych wyjściowych SQL

Gdy zadanie używa bazy danych SQL dla danych wyjściowych, użyj jawnego partycjonowania, aby dopasować optymalną liczbę partycji, aby zmaksymalizować przepływność. Ponieważ SQL działa najlepiej z ośmiu modułów zapisujących, partycjonowanie przepływu do ośmiu przed opróżniania lub gdzieś dalej w górę, może korzystać z wydajności zadania. 

Gdy istnieje więcej niż 8 partycji wejściowych, dziedziczenie schematu partycjonowania wejściowego może nie być odpowiednim wyborem. Należy rozważyć użycie [INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) w kwerendzie jawnie określić liczbę modułów zapisu danych wyjściowych. 

Poniższy przykład odczytuje z danych wejściowych, niezależnie od tego, że jest naturalnie podzielony na partycje i ponownie dzieli strumień dziesięciokrotnie zgodnie z deviceid wymiaru i opróżnia dane do danych wyjściowych. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Aby uzyskać więcej informacji, zobacz [dane wyjściowe usługi Azure Stream Analytics do usługi Azure SQL Database](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Wykorzystanie równoległości zapytań w usłudze Azure Stream Analytics](stream-analytics-parallelization.md)
