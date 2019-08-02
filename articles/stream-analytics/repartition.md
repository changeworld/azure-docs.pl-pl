---
title: Użyj ponownego partycjonowania, aby zoptymalizować przetwarzanie za pomocą Azure Stream Analytics
description: W tym artykule opisano sposób użycia ponownego partycjonowania w celu zoptymalizowania Azure Stream Analytics zadań, które nie mogą być równoległe.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 07/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9c802e6d23daf502da351549c66a7dae1247c068
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517438"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Użyj ponownego partycjonowania, aby zoptymalizować przetwarzanie za pomocą Azure Stream Analytics

W tym artykule pokazano, jak za pomocą ponownego partycjonowania skalować zapytanie Azure Stream Analytics w scenariuszach, które nie mogą być [](stream-analytics-scale-jobs.md)w pełni równoległe.

Nie można używać [przetwarzanie równoległe](stream-analytics-parallelization.md) , jeśli:

* Nie kontrolujesz klucza partycji dla strumienia wejściowego.
* Twoje Źródło "rozpylania" na wielu partycjach, które później muszą zostać scalone. 

## <a name="how-to-repartition"></a>Jak ponownie podzielić na partycje

Ponowne partycjonowanie lub reshuffling jest wymagane podczas przetwarzania danych w strumieniu, który nie jest podzielonej na fragmenty zgodnie z naturalnym schematem wejściowym, takim jak **PartitionID** dla Event Hubs. Po ponownym partycjonowaniu każdy fragmentu może być przetwarzany niezależnie, co pozwala na liniowe skalowanie potoku przesyłania strumieniowego.

Aby ponownie podzielić na partycje, użyj słowa kluczowego **do** po instrukcji **Partition by** w zapytaniu. Poniższy przykład dzieli dane według elementu **DeviceID** na liczbę partycji 10. Mieszanie elementu **DeviceID** służy do określenia, która partycja akceptuje Podstrumień. Dane są opróżniane niezależnie dla każdego strumienia partycjonowanego, przy założeniu, że dane wyjściowe obsługują operacje zapisu partycjonowane i mają 10 partycji.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

Poniższe przykładowe zapytanie łączy dwa strumienie danych z podziałem na partycje. W przypadku sprzęgania dwóch strumieni danych z podziałem na partycje, strumienie muszą mieć ten sam klucz partycji i liczbę. Wynik jest strumieniem, który ma ten sam schemat partycji.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Schemat danych wyjściowych powinien być zgodny z kluczem schematu strumienia i liczbą, tak aby każdy Podstrumień mógł zostać opróżniony niezależnie. Strumień można także scalić i ponownie podzielić na partycje przez inny schemat przed użyciem operacji opróżniania, ale należy unikać tej metody, ponieważ dodaje do ogólnego opóźnienia przetwarzania i zwiększa wykorzystanie zasobów.

## <a name="streaming-units-for-repartitions"></a>Jednostki przesyłania strumieniowego na potrzeby ponownego partycjonowania

Eksperymentuj i Obserwuj użycie zasobów zadania, aby określić dokładną liczbę potrzebnych partycji. Liczba [jednostek przesyłania strumieniowego (Su)](stream-analytics-streaming-unit-consumption.md) musi zostać skorygowana zgodnie z zasobami fizycznymi wymaganymi dla każdej partycji. Ogólnie rzecz biorąc, dla każdej partycji są dostępne sześć usług SUs. Jeśli do zadania są przypisane niewystarczające zasoby, system zastosuje repartycję tylko wtedy, gdy będzie ona czerpać korzyści z zadania.

## <a name="repartitions-for-sql-output"></a>Podział na partycje dla danych wyjściowych SQL

Gdy zadanie korzysta z usługi SQL Database na potrzeby danych wyjściowych, należy użyć jawnego ponownego partycjonowania, aby dopasować optymalną liczbę partycji. Ze względu na to, że program SQL działa najlepiej z ośmioma składnikami zapisywania, należy ponownie podzielić przepływ na osiem przed użyciem operacji opróżniania lub w innym miejscu, w którym można skorzystać z wydajności zadań. Aby uzyskać więcej informacji, zobacz [Azure Stream Analytics danych wyjściowych Azure SQL Database](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Kolejne kroki

* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Korzystanie z przetwarzanie równoległe zapytań w Azure Stream Analytics](stream-analytics-parallelization.md)