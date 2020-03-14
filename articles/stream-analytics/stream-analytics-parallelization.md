---
title: Użyj przetwarzania równoległego zapytań i skali w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób skalować zadania usługi Stream Analytics, konfigurowaniu partycji danych wejściowych, dostosowując definicję zapytania i ustawiając zadania jednostek przesyłania strumieniowego.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: d1afb6037b5fc290de93faba405982ebd1fb68ea
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254341"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Wykorzystywanie przetwarzania równoległego zapytań w usłudze Azure Stream Analytics
W tym artykule pokazano, jak korzystać z zalet przetwarzania równoległego w usłudze Azure Stream Analytics. Dowiesz się, jak skalować zadania usługi Stream Analytics, konfigurując partycji danych wejściowych, a następnie dostosowując definicję zapytania usługi analytics.
Jako warunek wstępny warto zapoznać się z pojęciem jednostki przesyłania strumieniowego opisanej w temacie [Omówienie i Dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Co to są elementy zadania usługi Stream Analytics?
Definicja zadania usługi Stream Analytics zawiera dane wejściowe, zapytania i danych wyjściowych. Dane wejściowe są, w którym zadanie odczytuje strumień danych z. Zapytanie jest używana do przekształcania danych strumienia wejściowego, a dane wyjściowe to, gdzie zadanie wysyła wyniki zadania, aby.

Zadanie wymaga co najmniej jedno źródło danych wejściowych służącą do strumieniowego przesyłania danych. Źródło danych wejściowych strumienia danych mogą być przechowywane w Centrum zdarzeń platformy Azure lub w usłudze Azure blob storage. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md) i rozpoczynanie [pracy przy użyciu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

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
-   Cosmos DB (należy jawnie ustawić klucz partycji)
-   Event Hubs (należy jawnie ustawić klucz partycji)
-   Usługa IoT Hub (trzeba jawnie ustawić klucz partycji)
-   Service Bus
- SQL i SQL Data Warehouse z opcjonalnym partycjonowaniem: Zobacz więcej informacji na [stronie dane wyjściowe do Azure SQL Database strony](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

Power BI nie obsługuje partycjonowania. Jednak nadal można podzielić na partycje dane wejściowe zgodnie z opisem w [tej sekcji](#multi-step-query-with-different-partition-by-values) . 

Aby uzyskać więcej informacji o partycjach zobacz następujące artykuły:

* [Event Hubs features overview (Omówienie funkcji usługi Event Hubs)](../event-hubs/event-hubs-features.md#partitions)
* [Data partitioning](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning) (Partycjonowanie danych)


## <a name="embarrassingly-parallel-jobs"></a>Zaskakująco równoległymi zadaniami
Zadanie *równoległe zaskakująco* jest najbardziej skalowalnym scenariuszem w Azure Stream Analytics. Jedna partycja danych wejściowych z jednym wystąpieniem zapytanie łączy się jednej partycji danych wyjściowych. Równoległość ta ma następujące wymagania:

1. Jeśli logika zapytania zależy od tego samego klucza, które są przetwarzane przez to samo wystąpienie zapytania, upewnij się, że zdarzenia, przejdź do tej samej partycji dane wejściowe. W przypadku Event Hubs lub IoT Hub oznacza to, że dane zdarzenia muszą mieć ustawioną wartość **PartitionKey** . Alternatywnie można użyć nadawców podzielonym na partycje. Dla magazynu obiektów blob oznacza to, że zdarzenia są wysyłane do tego samego folderu partycji. Jeśli logika zapytania nie wymaga tego samego klucza do przetworzenia przez tego samego wystąpienia zapytań, możesz zignorować to wymaganie. Przykładem tej logiki może być proste zapytanie select projektu filtru.  

2. Po danych jest poukładany stronie danych wejściowych, upewnij się, że zapytanie jest podzielona na partycje. Wymaga to użycia **partycji przez** wszystkie kroki. Wiele kroków są dozwolone, ale wszystkie one muszą być dzielone według tego samego klucza. W obszarze poziom zgodności 1,0 i 1,1 klucz partycjonowania musi mieć ustawioną wartość **PartitionID** , aby zadanie było w pełni równoległe. W przypadku zadań z compatility poziomu 1,2 i wyższych kolumna niestandardowa może być określona jako klucz partycji w ustawieniach wejściowych, a zadanie zostanie paralellized automatycznie nawet bez klauzuli PARTITION BY. W przypadku danych wyjściowych centrum zdarzeń Właściwość "kolumna klucza partycji" musi mieć ustawioną wartość "PartitionId".

3. Większość naszych danych wyjściowych korzystać z zalet partycjonowania, jednak jeśli używasz typu danych wyjściowych, nie obsługuje partycjonowanie zadanie nie będzie w pełni równoległych. Więcej informacji można znaleźć w [sekcji Output (dane wyjściowe](#outputs) ).

4. Liczba partycji danych wejściowych musi być równa liczbie partycji danych wyjściowych. Dane wyjściowe z magazynu obiektów blob może obsługiwać partycje i dziedziczy schematu partycjonowania nadrzędnego zapytania. Gdy klucza partycji dla obiektu Blob magazynu jest określony, dane są podzielone na partycje na partycję danych wejściowych związku z tym wynikiem jest nadal pełni równoległych. Poniżej przedstawiono przykładowe wartości partycji, zezwalających na w pełni równoległe zadania:

   * 8 partycji danych wejściowych Centrum zdarzeń i Centrum zdarzeń 8 danych wyjściowych partycji
   * 8 partycji danych wejściowych Centrum zdarzeń i danych wyjściowych z magazynu obiektów blob
   * 8 wejściowych partycji Centrum zdarzeń i dane wyjściowe z magazynu obiektów blob, partycje według pola niestandardowego za pomocą dowolnego kardynalności
   * 8 partycji danych wejściowych magazynu obiektów blob i danych wyjściowych z magazynu obiektów blob
   * 8 obiektów blob magazynu partycji danych wejściowych i 8 partycji danych wyjściowych Centrum zdarzeń

W poniższych sekcjach omówiono niektóre przykładowe scenariusze, które są zaskakująco równoległymi.

### <a name="simple-query"></a>Proste zapytanie

* Dane wejściowe: Centrum zdarzeń z 8 partycji
* Dane wyjściowe: centrum zdarzeń z 8 partycjami (kolumna klucza partycji musi mieć ustawioną wartość "PartitionId")

Kwerenda:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

To zapytanie jest proste filtru. W związku z tym nie musimy martwić się o partycjonowanie danych wejściowych, które są wysyłane do Centrum zdarzeń. Zwróć uwagę, że zadania o poziomie zgodności przed 1,2 muszą zawierać klauzulę **Partition by PartitionID** , dlatego spełnia wymagania #2 ze starszych wersji. W przypadku danych wyjściowych musimy skonfigurować dane wyjściowe centrum zdarzeń w zadaniu, aby klucz partycji miał ustawioną wartość **PartitionID**. Jeden ostatniego sprawdzenia jest upewnij się, że liczba partycji danych wejściowych jest równa liczbie partycji danych wyjściowych.

### <a name="query-with-a-grouping-key"></a>Zapytania przy użyciu klucza grupowania

* Dane wejściowe: Centrum zdarzeń z 8 partycji
* Dane wyjściowe: Blob storage

Kwerenda:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

To zapytanie ma klucz grupowania. W związku z tym należy wysyłać zdarzenia zgrupowane razem na tej samej partycji Centrum zdarzeń. Ponieważ w tym przykładzie pogrupujemy przez TollBoothID, firma Microsoft Pamiętaj, że TollBoothID jest używany jako klucz partycji zdarzenia są wysyłane do Centrum zdarzeń. Następnie w ASA można użyć **partycji według identyfikatora PartitionID** do dziedziczenia z tego schematu partycji i włączenia pełnej przetwarzanie równoległe. Ponieważ dane wyjściowe magazynu obiektów blob, nie potrzebujemy już martwić się o Konfigurowanie wartość klucza partycji dla każdego wymogu #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Przykładowe scenariusze, które *nie* są zaskakująco równoległe

Niektóre scenariusze zaskakująco równoległymi pokazaliśmy w poprzedniej sekcji. W tej sekcji omówiono scenariusze, które nie są spełnione wszystkie wymagania za zaskakująco równoległymi. 

### <a name="mismatched-partition-count"></a>Liczba niedopasowanych partycji
* Dane wejściowe: Centrum zdarzeń z 8 partycji
* Dane wyjściowe: Centrum zdarzeń z 32 partycjami.

W takim przypadku nie ma znaczenia, co to jest zapytanie. Jeśli liczba partycji danych wejściowych jest niezgodna z liczbą partycji danych wyjściowych, topologia nie jest zaskakująco równolegle. + jednak firma Microsoft może nadal otrzymywać niektóre poziomu lub przetwarzania równoległego.

### <a name="query-using-non-partitioned-output"></a>Wykonywanie zapytań przy użyciu niepartycjonowana danych wyjściowych
* Dane wejściowe: Centrum zdarzeń z 8 partycji
* Dane wyjściowe: Power BI

Dane wyjściowe Power BI nie obsługują obecnie partycjonowania. W związku z tym w tym scenariuszu nie jest zaskakująco równoległymi.

### <a name="multi-step-query-with-different-partition-by-values"></a>Wieloetapowy zapytania z różnymi wartościami PARTITION BY
* Dane wejściowe: Centrum zdarzeń z 8 partycji
* Dane wyjściowe: Centrum zdarzeń z 8 partycji

Kwerenda:

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

Jak widać, drugi krok używa **TollBoothId** jako klucza partycjonowania. Ten krok nie jest taki sam jak pierwszy krok, a w związku z tym wymaga do zrobienia losowa. 

Niektórych zadań usługi Stream Analytics, które są zgodne z (lub nie) zaskakująco równoległymi topologii można znaleźć w poprzednich przykładach. Jeśli są one zgodne, mają możliwość maksymalną skalę. Aktualizacje dla zadań, które nie mieszczą się jeden z tych profilów skalowania wskazówki będą dostępne w przyszłości. Na razie użyj ogólne wskazówki w poniższych sekcjach.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Poziom zgodności 1,2 — zapytanie wieloetapowe z inną PARTYCJą według wartości 
* Dane wejściowe: Centrum zdarzeń z 8 partycji
* Dane wyjściowe: centrum zdarzeń z 8 partycjami (kolumna klucza partycji musi mieć ustawioną wartość "TollBoothId")

Kwerenda:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Poziom zgodności 1,2 domyślnie włącza równoległe wykonywanie zapytań. Na przykład zapytanie z poprzedniej sekcji zostanie parttioned, tak długo, jak kolumna "TollBoothId" jest ustawiona jako klucz partycji wejściowej. Klauzula PARTITION BY ParttionId nie jest wymagana.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Oblicz maksimum jednostki zadania przesyłania strumieniowego
Całkowita liczba jednostek przesyłania strumieniowego, które mogą być używane przez zadanie usługi Stream Analytics zależy od liczby kroków w zapytaniu, określony dla zadania i liczby partycji dla każdego kroku.

### <a name="steps-in-a-query"></a>Kroki zapytania
Zapytanie może mieć jeden lub kilka kroków. Każdy krok jest podzapytaniem zdefiniowanym przez słowo kluczowe **with** . Zapytanie, które znajduje się poza słowem kluczowym **with** (tylko jedno zapytanie), jest również zliczane jako krok, taki jak instrukcja **SELECT** w następującej kwerendzie:

Kwerenda:

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
* Instrukcja **SELECT** zapytania musi odczytywać ze partycjonowanego źródła danych wejściowych.
* Zapytanie w ramach kroku musi zawierać słowo kluczowe **Partition by** .

Gdy zapytanie jest podzielone na partycje, zdarzenia wejściowe są grupami przetworzone i zagregowane w oddzielnej partycji, a dane wyjściowe zdarzenia są generowane dla każdej grupy. Chcąc połączone agregacji, należy utworzyć drugi etap niepartycjonowana do zagregowania.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Oblicz maksymalna liczba jednostek w zadaniu przesyłania strumieniowego
Wszystkie kroki niepartycjonowana ze sobą można skalować do sześciu jednostek przesyłania strumieniowego (SUs) dla zadania usługi Stream Analytics. Oprócz tego możesz dodać SUs 6 dla każdej partycji w kroku podzielonym na partycje.
**Przykłady** można znaleźć w poniższej tabeli.

| Zapytanie                                               | Maksymalna liczba SUs dla zadania |
| --------------------------------------------------- | ------------------- |
| <ul><li>Zapytanie zawiera jeden krok.</li><li>Ten krok nie jest podzielona na partycje.</li></ul> | 6 |
| <ul><li>Strumień danych wejściowych jest podzielona na partycje przy 16.</li><li>Zapytanie zawiera jeden krok.</li><li>Ten krok jest podzielona na partycje.</li></ul> | 96 (partycje 6 * 16) |
| <ul><li>Zapytanie zawiera dwa kroki.</li><li>Żaden z kroków jest podzielona na partycje.</li></ul> | 6 |
| <ul><li>Strumień danych wejściowych jest podzielona na partycje przez 3.</li><li>Zapytanie zawiera dwa kroki. Danych wejściowych kroku jest podzielona na partycje, a drugi krok nie jest.</li><li>Instrukcja <strong>SELECT</strong> odczytuje z partycjonowanego danych wejściowych.</li></ul> | 24 (18 partycjonowane kroki + 6 niepartycjonowana kroków |

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

Gdy zapytanie jest podzielone na partycje, zdarzenia wejściowe są przetwarzane i zagregowane w oddzielnej partycji grupy. Zdarzenia wyjściowe również są generowane dla każdej grupy. Partycjonowanie może spowodować pewne nieoczekiwane wyniki, jeśli pole **Grupuj według** nie jest kluczem partycji w strumieniu danych wejściowych. Na przykład pole **TollBoothId** w powyższym zapytaniu nie jest kluczem partycji **Input1**. Powoduje to, że dane z budki nr 1 mogły być rozkładane w wielu partycjach.

Każda partycja **Input1** zostanie przetworzona osobno Stream Analytics. W rezultacie wielu rekordów samochodu liczbę elementów w tej samej budki w tym samym oknie wirowania zostanie utworzony. Jeśli nie można zmienić klucza partycji danych wejściowych, można naprawić ten problem, dodając krok-partition do wartości zagregowanych w partycjach, jak w poniższym przykładzie:

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

## <a name="achieving-higher-throughputs-at-scale"></a>Osiąganie większej przepływności na dużą skalę

Zadanie [równoległe zaskakująco](#embarrassingly-parallel-jobs) jest niezbędne, ale nie jest wystarczające, aby utrzymać wyższą przepływność na dużą skalę. Każdy system przechowywania i odpowiadający mu Stream Analytics dane wyjściowe mają różne informacje na temat sposobu osiągnięcia najlepszej możliwej przepustowości zapisu. Podobnie jak w przypadku dowolnego scenariusza w skali, istnieją pewne wyzwania, które można rozwiązać za pomocą odpowiednich konfiguracji. W tej sekcji omówiono konfiguracje kilku typowych danych wyjściowych i przedstawiono przykłady dla pozyskania szybkości pozyskiwania, 5 K i 10 000 zdarzeń na sekundę.

Poniższe obserwacje wykorzystują zadanie Stream Analytics z zapytaniem bezstanowym (Passthrough), czyli podstawowym formatem UDF języka JavaScript, który zapisuje w centrum zdarzeń, Azure SQL DB lub Cosmos DB.

#### <a name="event-hub"></a>Centrum zdarzeń

|Współczynnik pozyskiwania (zdarzenia na sekundę) | Jednostki przesyłania strumieniowego | Zasoby wyjściowe  |
|--------|---------|---------|
| 1000     |    1    |  2 JEDNOSTEK PRZEPŁYWNOŚCI   |
| 5 K     |    6    |  6 JEDNOSTEK PRZEPŁYWNOŚCI   |
| Min    |    12   |  10 JEDNOSTEK PRZEPŁYWNOŚCI  |

Rozwiązanie [centrum zdarzeń](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) skaluje się liniowo w zakresie jednostek przesyłania strumieniowego (Su) i przepływność, co sprawia, że jest to najbardziej wydajne i wydajny sposób analizowania i przesyłania strumieniowego danych poza Stream Analytics. Zadania można skalować do 192 SU, co w przybliżeniu tłumaczy na przetwarzanie do 200 MB/s lub 19 000 000 000 000 zdarzeń dziennie.

#### <a name="azure-sql"></a>Azure SQL
|Współczynnik pozyskiwania (zdarzenia na sekundę) | Jednostki przesyłania strumieniowego | Zasoby wyjściowe  |
|---------|------|-------|
|    1000   |   3  |  S3   |
|    5 K   |   18 |  P4   |
|    Min  |   36 |  P6   |

[Usługa Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) obsługuje pisanie równoległe, zwane dziedziczeniem partycjonowania, ale nie jest domyślnie włączona. Jednak włączenie dziedziczenia partycjonowania oraz w pełni równoległe zapytanie może być niewystarczające do osiągnięcia większej przepływności. Przepływność zapisu SQL zależy znacznie od konfiguracji bazy danych SQL Azure i schematu tabeli. Artykuł dotyczący [wydajności danych wyjściowych SQL](./stream-analytics-sql-output-perf.md) zawiera więcej szczegółów na temat parametrów, które mogą zmaksymalizować przepływność zapisu. Jak wskazano w [Azure Stream Analytics dane wyjściowe do Azure SQL Database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) artykułu, to rozwiązanie nie jest skalowane liniowo jako w pełni równoległe potoki powyżej 8 partycji i może wymagać ponownego partycjonowania danych wyjściowych SQL (zobacz [do](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). Jednostki SKU w warstwie Premium są konieczne w celu utrzymania wysokich stawek we/wy wraz z obciążeniem kopii zapasowych dziennika co kilka minut.

#### <a name="cosmos-db"></a>Cosmos DB
|Współczynnik pozyskiwania (zdarzenia na sekundę) | Jednostki przesyłania strumieniowego | Zasoby wyjściowe  |
|-------|-------|---------|
|  1000   |  3    | 20 000 RU  |
|  5 K   |  24   | 60K RU  |
|  Min  |  48   | 120K RU |

[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) dane wyjściowe z Stream Analytics zostały zaktualizowane w celu korzystania z natywnej integracji w ramach [poziomu zgodności 1,2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Poziom zgodności 1,2 zapewnia znacznie większą przepływność i zmniejsza zużycie RU w porównaniu do 1,1, który jest domyślnym poziomem zgodności dla nowych zadań. Rozwiązanie używa kontenerów CosmosDB podzielonych na/deviceId, a pozostałe rozwiązanie jest skonfigurowane identycznie.

Wszystkie [transmisje strumieniowe na platformie Azure przykłady](https://github.com/Azure-Samples/streaming-at-scale) używają centrum zdarzeń, które jest używane przez załadowanie symulowane klientów testowych jako dane wejściowe. Każde zdarzenie wejściowe jest dokumentem JSON rozmiarze 1 KB, który umożliwia łatwe tłumaczenie skonfigurowanych stawek za pozyskiwanie przepływności (1 MB/s, 5 MB/s i 10 MB/s). Zdarzenia symulowania urządzenia IoT wysyłającego następujące dane JSON (w skróconej formie) dla maksymalnie 1000 urządzeń:

```
{
    "eventId": "b81d241f-5187-40b0-ab2a-940faf9757c0",
    "complexData": {
        "moreData0": 51.3068118685458,
        "moreData22": 45.34076957651598
    },
    "value": 49.02278128887753,
    "deviceId": "contoso://device-id-1554",
    "type": "CO2",
    "createdAt": "2019-05-16T17:16:40.000003Z"
}
```

> [!NOTE]
> Konfiguracje mogą ulec zmianie ze względu na różne składniki używane w rozwiązaniu. Aby uzyskać dokładniejsze oszacowanie, Dostosuj przykłady tak, aby pasowały do Twojego scenariusza.

### <a name="identifying-bottlenecks"></a>Identyfikowanie wąskich gardeł

Użyj okienka metryki w zadaniu Azure Stream Analytics, aby identyfikować wąskie gardła w potoku. Przejrzyj **zdarzenia wejściowe/wyjściowe** dla przepływności i ["opóźnienie znaku wodnego"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) lub **zaległe zdarzenia** , aby sprawdzić, czy zadanie jest zgodne z szybkością danych wejściowych. W przypadku metryk centrum zdarzeń należy poszukać **żądań z ograniczeniami** i odpowiednio dostosować jednostki progowe. W przypadku metryk Cosmos DB Sprawdź **maksymalną liczbę użytych jednostek ru/s na klucz partycji** w obszarze przepływność, aby upewnić się, że zakresy kluczy partycji są jednolicie używane. W przypadku usługi Azure SQL DB Monitoruj **operacje we/wy dziennika** i **procesora CPU**.

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, wypróbuj nasze [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
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

