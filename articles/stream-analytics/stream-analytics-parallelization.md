---
title: Użyj przetwarzania równoległego zapytań i skali w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób skalować zadania usługi Stream Analytics, konfigurowaniu partycji danych wejściowych, dostosowując definicję zapytania i ustawiając zadania jednostek przesyłania strumieniowego.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 0b68819ba032d7655433aadd30fe2852941096ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478882"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Wykorzystywanie przetwarzania równoległego zapytań w usłudze Azure Stream Analytics
W tym artykule pokazano, jak korzystać z zalet przetwarzania równoległego w usłudze Azure Stream Analytics. Dowiesz się, jak skalować zadania usługi Stream Analytics, konfigurując partycji danych wejściowych, a następnie dostosowując definicję zapytania usługi analytics.
Jako warunek wstępny, warto znać pojęcia opisane w zastrzeżona jednostka przesyłania strumieniowego [opis i dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Co to są elementy zadania usługi Stream Analytics?
Definicja zadania usługi Stream Analytics zawiera dane wejściowe, zapytania i danych wyjściowych. Dane wejściowe są, w którym zadanie odczytuje strumień danych z. Zapytanie jest używana do przekształcania danych strumienia wejściowego, a dane wyjściowe to, gdzie zadanie wysyła wyniki zadania, aby.  

Zadanie wymaga co najmniej jedno źródło danych wejściowych służącą do strumieniowego przesyłania danych. Źródło danych wejściowych strumienia danych mogą być przechowywane w Centrum zdarzeń platformy Azure lub w usłudze Azure blob storage. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md) i [rozpoczęcie korzystania z usługi Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Partycje w źródła i ujścia
Skalowanie zadań usługi Stream Analytics korzysta z partycjami w danych wejściowych lub wyjściowych. Partycjonowanie pozwala podzielić dane na podzestawy na podstawie klucza partycji. Proces, który wykorzystuje dane (na przykład zadanie usługi Stream Analytics) można korzystać i pisać różne partycje równolegle, co zwiększa przepustowość. 

### <a name="inputs"></a>Dane wejściowe
Wszystkie dane wejściowe z usługi Azure Stream Analytics można korzystać ze partycjonowania:
-   Centrum zdarzeń (trzeba ustawić klucz partycji, który jawnie zawiera słowo kluczowe PARTITION BY)
-   Usługa IoT Hub (trzeba ustawić klucz partycji, który jawnie zawiera słowo kluczowe PARTITION BY)
-   Blob Storage

### <a name="outputs"></a>Dane wyjściowe

Podczas pracy z usługą Stream Analytics, możesz korzystać z zalet partycjonowania w danych wyjściowych:
-   Azure Data Lake Storage
-   Azure Functions
-   Tabela platformy Azure
-   Magazyn obiektów blob (można ustawić klucza partycji jawnie)
-   Usługa cosmos DB (trzeba jawnie ustawić klucz partycji)
-   Usługa Event Hubs (trzeba jawnie ustawić klucz partycji)
-   Usługa IoT Hub (trzeba jawnie ustawić klucz partycji)
-   Service Bus
- SQL i SQL Data Warehouse z podziałem na partycje opcjonalne: zobaczyć więcej informacji o [danych wyjściowych do usługi Azure SQL Database strony](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

Usługa Power BI nie obsługuje partycjonowanie. Jednak można nadal podzielić dane wejściowe zgodnie z opisem w [w tej sekcji](#multi-step-query-with-different-partition-by-values) 

Aby uzyskać więcej informacji o partycjach zobacz następujące artykuły:

* [Event Hubs features overview (Omówienie funkcji usługi Event Hubs)](../event-hubs/event-hubs-features.md#partitions)
* [Partycjonowanie danych](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Zaskakująco równoległymi zadaniami
*Zaskakująco równoległymi* zadanie jest najbardziej skalowalny scenariusza, które mamy w usłudze Azure Stream Analytics. Jedna partycja danych wejściowych z jednym wystąpieniem zapytanie łączy się jednej partycji danych wyjściowych. Równoległość ta ma następujące wymagania:

1. Jeśli logika zapytania zależy od tego samego klucza, które są przetwarzane przez to samo wystąpienie zapytania, upewnij się, że zdarzenia, przejdź do tej samej partycji dane wejściowe. Usługa Event Hubs lub usługi IoT Hub, oznacza to, że dane zdarzenia muszą mieć **PartitionKey** wartość zestawu. Alternatywnie można użyć nadawców podzielonym na partycje. Dla magazynu obiektów blob oznacza to, że zdarzenia są wysyłane do tego samego folderu partycji. Jeśli logika zapytania nie wymaga tego samego klucza do przetworzenia przez tego samego wystąpienia zapytań, możesz zignorować to wymaganie. Przykładem tej logiki może być proste zapytanie select projektu filtru.  

2. Po danych jest poukładany stronie danych wejściowych, upewnij się, że zapytanie jest podzielona na partycje. Wymaga to użycia **PARTITION BY** wszystkich kroków. Wiele kroków są dozwolone, ale wszystkie one muszą być dzielone według tego samego klucza. Obecnie partycjonowania klucza musi być równa **PartitionId** w kolejności, można w pełni równoległe zadania.  

3. Większość naszych danych wyjściowych korzystać z zalet partycjonowania, jednak jeśli używasz typu danych wyjściowych, nie obsługuje partycjonowanie zadanie nie będzie w pełni równoległych. Zapoznaj się [danych wyjściowych sekcji](#outputs) Aby uzyskać więcej informacji.

4. Liczba partycji danych wejściowych musi być równa liczbie partycji danych wyjściowych. Dane wyjściowe z magazynu obiektów blob może obsługiwać partycje i dziedziczy schematu partycjonowania nadrzędnego zapytania. Gdy klucza partycji dla obiektu Blob magazynu jest określony, dane są podzielone na partycje na partycję danych wejściowych związku z tym wynikiem jest nadal pełni równoległych. Poniżej przedstawiono przykładowe wartości partycji, zezwalających na w pełni równoległe zadania:

   * 8 partycji danych wejściowych Centrum zdarzeń i Centrum zdarzeń 8 danych wyjściowych partycji
   * 8 partycji danych wejściowych Centrum zdarzeń i danych wyjściowych z magazynu obiektów blob
   * 8 wejściowych partycji Centrum zdarzeń i dane wyjściowe z magazynu obiektów blob, partycje według pola niestandardowego za pomocą dowolnego kardynalności
   * 8 partycji danych wejściowych magazynu obiektów blob i danych wyjściowych z magazynu obiektów blob
   * 8 obiektów blob magazynu partycji danych wejściowych i 8 partycji danych wyjściowych Centrum zdarzeń

W poniższych sekcjach omówiono niektóre przykładowe scenariusze, które są zaskakująco równoległymi.

### <a name="simple-query"></a>Proste zapytanie

* Dane wejściowe: Centrum zdarzeń z 8 partycji
* Dane wyjściowe: Centrum zdarzeń z 8 partycji

Zapytanie:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

To zapytanie jest proste filtru. W związku z tym nie musimy martwić się o partycjonowanie danych wejściowych, które są wysyłane do Centrum zdarzeń. Należy zauważyć, że kwerenda zawiera **PARTITION BY PartitionId**, więc jego spełnia wymagania #2 z wcześniej. Dla danych wyjściowych, należy skonfigurować dane wyjściowe z Centrum zdarzeń w ramach zadania, aby zestaw kluczy partycji do **PartitionId**. Jeden ostatniego sprawdzenia jest upewnij się, że liczba partycji danych wejściowych jest równa liczbie partycji danych wyjściowych.

### <a name="query-with-a-grouping-key"></a>Zapytania przy użyciu klucza grupowania

* Dane wejściowe: Centrum zdarzeń z 8 partycji
* Dane wyjściowe: Blob Storage

Zapytanie:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

To zapytanie ma klucz grupowania. W związku z tym należy wysyłać zdarzenia zgrupowane razem na tej samej partycji Centrum zdarzeń. Ponieważ w tym przykładzie pogrupujemy przez TollBoothID, firma Microsoft Pamiętaj, że TollBoothID jest używany jako klucz partycji zdarzenia są wysyłane do Centrum zdarzeń. Następnie w ASA, możemy użyć **PARTITION BY PartitionId** dziedziczyć ten schemat partycji i włączyć pełne przetwarzanie równoległe. Ponieważ dane wyjściowe magazynu obiektów blob, nie potrzebujemy już martwić się o Konfigurowanie wartość klucza partycji dla każdego wymogu #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Przykładowe scenariusze, które są *nie* zaskakująco równoległymi

Niektóre scenariusze zaskakująco równoległymi pokazaliśmy w poprzedniej sekcji. W tej sekcji omówiono scenariusze, które nie są spełnione wszystkie wymagania za zaskakująco równoległymi. 

### <a name="mismatched-partition-count"></a>Liczba niedopasowanych partycji
* Dane wejściowe: Centrum zdarzeń z 8 partycji
* Dane wyjściowe: Centrum zdarzeń z 32 partycjami.

W takim przypadku nie ma znaczenia, co to jest zapytanie. Jeśli liczba partycji danych wejściowych jest niezgodna z liczbą partycji danych wyjściowych, topologia nie jest zaskakująco równolegle. + jednak firma Microsoft może nadal otrzymywać niektóre poziomu lub przetwarzania równoległego.

### <a name="query-using-non-partitioned-output"></a>Wykonywanie zapytań przy użyciu niepartycjonowana danych wyjściowych
* Dane wejściowe: Centrum zdarzeń z 8 partycji
* Dane wyjściowe: Power BI

Wyjście usługi Power BI nie obsługuje obecnie partycjonowania. W związku z tym w tym scenariuszu nie jest zaskakująco równoległymi.

### <a name="multi-step-query-with-different-partition-by-values"></a>Wieloetapowy zapytania z różnymi wartościami PARTITION BY
* Dane wejściowe: Centrum zdarzeń z 8 partycji
* Dane wyjściowe: Centrum zdarzeń z 8 partycji

Zapytanie:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Jak widać, drugi etap używa **TollBoothId** jako klucza partycji. Ten krok nie jest taki sam jak pierwszy krok, a w związku z tym wymaga do zrobienia losowa. 

Niektórych zadań usługi Stream Analytics, które są zgodne z (lub nie) zaskakująco równoległymi topologii można znaleźć w poprzednich przykładach. Jeśli są one zgodne, mają możliwość maksymalną skalę. Aktualizacje dla zadań, które nie mieszczą się jeden z tych profilów skalowania wskazówki będą dostępne w przyszłości. Na razie użyj ogólne wskazówki w poniższych sekcjach.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Oblicz maksimum jednostki zadania przesyłania strumieniowego
Całkowita liczba jednostek przesyłania strumieniowego, które mogą być używane przez zadanie usługi Stream Analytics zależy od liczby kroków w zapytaniu, określony dla zadania i liczby partycji dla każdego kroku.

### <a name="steps-in-a-query"></a>Kroki zapytania
Zapytanie może mieć jeden lub kilka kroków. Każdy krok znajduje się podzapytanie definicją **WITH** — słowo kluczowe. Zapytanie, które znajduje się poza **WITH** — słowo kluczowe (tylko jedno zapytanie) również są traktowane jako krok, na przykład **wybierz** poufności informacji w następującej kwerendy:

Zapytanie:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId
```

Ta kwerenda zawiera dwa kroki.

> [!NOTE]
> To zapytanie jest omówiona bardziej szczegółowo w dalszej części tego artykułu.
>  

### <a name="partition-a-step"></a>Krok partycji
Partycjonowanie krok wymaga następujących warunków:

* Źródło danych wejściowych musi być dzielony na partycje. 
* **Wybierz** instrukcja kwerendy musi odczytywać partycjonowane źródła danych wejściowych.
* Zapytanie w ramach kroku musi zawierać **PARTITION BY** — słowo kluczowe.

Gdy zapytanie jest podzielone na partycje, zdarzenia wejściowe są grupami przetworzone i zagregowane w oddzielnej partycji, a dane wyjściowe zdarzenia są generowane dla każdej grupy. Chcąc połączone agregacji, należy utworzyć drugi etap niepartycjonowana do zagregowania.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Oblicz maksymalna liczba jednostek w zadaniu przesyłania strumieniowego
Wszystkie kroki niepartycjonowana ze sobą można skalować do sześciu jednostek przesyłania strumieniowego (SUs) dla zadania usługi Stream Analytics. Oprócz tego możesz dodać SUs 6 dla każdej partycji w kroku podzielonym na partycje.
Możesz zobaczyć niektóre **przykłady** w poniższej tabeli.

| Zapytanie                                               | Maksymalna liczba SUs dla zadania |
| --------------------------------------------------- | ------------------- |
| <ul><li>Zapytanie zawiera jeden krok.</li><li>Ten krok nie jest podzielona na partycje.</li></ul> | 6 |
| <ul><li>Strumień danych wejściowych jest podzielona na partycje przy 16.</li><li>Zapytanie zawiera jeden krok.</li><li>Ten krok jest podzielona na partycje.</li></ul> | 96 (partycje 6 * 16) |
| <ul><li>Zapytanie zawiera dwa kroki.</li><li>Żaden z kroków jest podzielona na partycje.</li></ul> | 6 |
| <ul><li>Strumień danych wejściowych jest podzielona na partycje przez 3.</li><li>Zapytanie zawiera dwa kroki. Danych wejściowych kroku jest podzielona na partycje, a drugi krok nie jest.</li><li><strong>Wybierz</strong> instrukcji odczytuje z podzielonym na partycje dane wejściowe.</li></ul> | 24 (18 partycjonowane kroki + 6 niepartycjonowana kroków |

### <a name="examples-of-scaling"></a>Przykłady skalowania

Następujące zapytanie oblicza liczbę samochodów w oknie trzyminutowe pośrednictwa stacji płatny, która ma trzy tollbooths. To zapytanie może być skalowana w maksymalnie sześć SUs.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Aby użyć więcej SUs dla zapytania, musi być podzielony strumienia danych wejściowych i zapytania. Ponieważ partycja strumień danych jest ustawiony na 3, następujące zmodyfikowane zapytanie może być skalowana w taki sposób, maksymalnie 18 SUs:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Gdy zapytanie jest podzielone na partycje, zdarzenia wejściowe są przetwarzane i zagregowane w oddzielnej partycji grupy. Zdarzenia wyjściowe również są generowane dla każdej grupy. Partycjonowanie może spowodować, że niektóre nieoczekiwane wyniki podczas **GROUP BY** pole nie jest częścią klucza partycji w strumieniu danych wejściowych. Na przykład **TollBoothId** pola w poprzednim zapytaniu nie jest klucz partycji **wejście1**. Powoduje to, że dane z budki nr 1 mogły być rozkładane w wielu partycjach.

Każdy z **wejście1** partycje będą przetwarzane osobno przez usługę Stream Analytics. W rezultacie wielu rekordów samochodu liczbę elementów w tej samej budki w tym samym oknie wirowania zostanie utworzony. Jeśli nie można zmienić klucza partycji danych wejściowych, można naprawić ten problem, dodając krok-partition do wartości zagregowanych w partycjach, jak w poniższym przykładzie:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

To zapytanie może być skalowane do 24 SUs.

> [!NOTE]
> Jeśli są Sprzęganie dwóch strumieni, upewnij się, że strumienie są partycjonowane na podstawie klucza partycji kolumny, która służy do tworzenia sprzężenia. Upewnij się również mają taką samą liczbę partycji w obu strumieni.
> 
> 





## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

