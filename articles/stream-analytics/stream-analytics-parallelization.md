---
title: Używanie równoległości i skalowania zapytań w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób skalowania zadań usługi Stream Analytics przez skonfigurowanie partycji wejściowych, dostrajanie definicji kwerendy i ustawianie jednostek przesyłania strumieniowego zadań.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 31ac43ec796d305b8a8f4b62ea09481e262b6b3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256984"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Wykorzystanie równoległości zapytań w usłudze Azure Stream Analytics
W tym artykule pokazano, jak korzystać z równoległości w usłudze Azure Stream Analytics. Dowiesz się, jak skalować zadania usługi Stream Analytics, konfigurując partycje wejściowe i dostrajając definicję zapytania analitycznego.
Jako warunek wstępny warto zapoznać się z pojęciem jednostki przesyłania strumieniowego opisaną w opisie [i dostosuj jednostki przesyłania strumieniowego.](stream-analytics-streaming-unit-consumption.md)

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Jakie są części zadania usługi Stream Analytics?
Definicja zadania usługi Stream Analytics zawiera dane wejściowe, kwerendę i dane wyjściowe. Dane wejściowe są, gdzie zadanie odczytuje strumień danych z. Kwerenda jest używana do przekształcania strumienia wejściowego danych, a dane wyjściowe to miejsce, w którym zadanie wysyła wyniki zadania.

Zadanie wymaga co najmniej jednego źródła wejściowego do przesyłania strumieniowego danych. Źródło wejściowe strumienia danych mogą być przechowywane w centrum zdarzeń platformy Azure lub w magazynie obiektów blob platformy Azure. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md) i wprowadzenie do korzystania z usługi Azure Stream [Analytics.](stream-analytics-real-time-fraud-detection.md)

## <a name="partitions-in-sources-and-sinks"></a>Partycje w źródłach i zlewozmywakach
Skalowanie zadania usługi Stream Analytics korzysta z partycji w danych wejściowych lub wyjściowych. Partycjonowanie umożliwia dzielenie danych na podzbiory na podstawie klucza partycji. Proces, który zużywa dane (takie jak zadanie analizy przesyłania strumieniowego) może korzystać i zapisywać różne partycje równolegle, co zwiększa przepływność. 

### <a name="inputs"></a>Dane wejściowe
Wszystkie dane wejściowe usługi Azure Stream Analytics mogą korzystać z partycjonowania:
-   EventHub (należy ustawić klucz partycji jawnie za pomocą słowa kluczowego PARTITION BY)
-   Centrum IoT Hub (należy ustawić klucz partycji jawnie za pomocą słowa kluczowego PARTITION BY)
-   Blob Storage

### <a name="outputs"></a>Dane wyjściowe

Podczas pracy z usługi Stream Analytics można korzystać z partycjonowania w danych wyjściowych:
-   Azure Data Lake Storage
-   Azure Functions
-   Tabela platformy Azure
-   Magazyn obiektów Blob (można ustawić klucz partycji jawnie)
-   Usługa Cosmos DB (należy jawnie ustawić klucz partycji)
-   Centra zdarzeń (należy ustawić klucz partycji jawnie)
-   Centrum IoT Hub (należy ustawić klucz partycji jawnie)
-   Service Bus
- Magazyn danych SQL i SQL z opcjonalnym partycjonowaniem: zobacz więcej informacji na [stronie Dane wyjściowe do bazy danych SQL platformy Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-sql-output-perf).

Usługa Power BI nie obsługuje partycjonowania. Jednak nadal można podzielić dane wejściowe zgodnie z opisem w [tej sekcji](#multi-step-query-with-different-partition-by-values) 

Aby uzyskać więcej informacji na temat partycji, zobacz następujące artykuły:

* [Event Hubs features overview (Omówienie funkcji usługi Event Hubs)](../event-hubs/event-hubs-features.md#partitions)
* [Data partitioning](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning) (Partycjonowanie danych)


## <a name="embarrassingly-parallel-jobs"></a>Żenująco równoległe oferty pracy
*Żenująco równoległe* zadanie jest najbardziej skalowalnym scenariuszem, który mamy w usłudze Azure Stream Analytics. Łączy jedną partycję danych wejściowych z jednym wystąpieniem kwerendy z jedną partycją danych wyjściowych. Ten równoległość ma następujące wymagania:

1. Jeśli logika kwerendy zależy od tego samego klucza przetwarzane przez to samo wystąpienie kwerendy, należy upewnić się, że zdarzenia przejść do tej samej partycji danych wejściowych. W przypadku centrów zdarzeń lub usługi IoT Hub oznacza to, że dane zdarzenia muszą mieć ustawioną wartość **PartitionKey.** Alternatywnie można użyć nadawców podzielonych na partycje. W przypadku magazynu obiektów blob oznacza to, że zdarzenia są wysyłane do tego samego folderu partycji. Jeśli logika kwerendy nie wymaga tego samego klucza do przetworzenia przez to samo wystąpienie kwerendy, można zignorować to wymaganie. Przykładem tej logiki może być proste zapytanie select-project-filter.  

2. Gdy dane są rozmieszczone po stronie danych wejściowych, należy upewnić się, że kwerenda jest podzielony na partycje. Wymaga to użycia **PARTITION BY** we wszystkich krokach. Wiele kroków są dozwolone, ale wszystkie muszą być podzielone na partycje przez ten sam klucz. W obszarze poziomu zgodności 1.0 i 1.1 klucz partycjonowania musi być ustawiony na **PartitionId,** aby zadanie było w pełni równoległe. W przypadku zadań o poziomie zgodności 1.2 lub nowszym kolumna niestandardowa może być określona jako klucz partycji w ustawieniach wejściowych, a zadanie zostanie automatycznie paralellized nawet bez klauzuli PARTITION BY. Dla wyjścia centrum zdarzeń właściwość "Kolumna klucza partycji" musi być ustawiona na użycie "PartitionId".

3. Większość naszych danych wyjściowych może korzystać z partycjonowania, jednak jeśli używasz typu wyjściowego, który nie obsługuje partycjonowania zadania nie będzie w pełni równoległy. W przypadku wyjść Centrum zdarzeń upewnij się, że **kolumna klucz partycji** jest ustawiona tak samo jak klucz partycji kwerendy. Więcej informacji można znaleźć w [sekcji danych wyjściowych.](#outputs)

4. Liczba partycji wejściowych musi być równa liczbie partycji wyjściowych. Dane wyjściowe magazynu obiektów Blob mogą obsługiwać partycje i dziedziczy schemat partycjonowania kwerendy nadrzędnej. Po określeniu klucza partycji dla magazynu obiektów Blob dane są partycjonowane na partycję wejściową, dzięki czemu wynik jest nadal w pełni równoległy. Oto przykłady wartości partycji, które umożliwiają zadanie w pełni równoległe:

   * 8 partycji wejściowych centrum zdarzeń i 8 partycji wyjściowych koncentratora zdarzeń
   * Partycje wejściowe centrum zdarzeń 8 i dane wyjściowe magazynu obiektów blob
   * Partycje wejściowe centrum zdarzeń 8 i dane wyjściowe magazynu obiektów blob podzielone na partycje według pola niestandardowego z dowolną kardynalność
   * 8 partycji wejściowych magazynu obiektów blob i danych wyjściowych magazynu obiektów blob
   * 8 partycji wejściowych magazynu obiektów blob i 8 partycji wyjściowych koncentratora zdarzeń

W poniższych sekcjach omówiono niektóre przykładowe scenariusze, które są żenująco równoległe.

### <a name="simple-query"></a>Proste zapytanie

* Dane wejściowe: Centrum zdarzeń z 8 partycjami
* Dane wyjściowe: Centrum zdarzeń z 8 partycjami ("Kolumna klucza partycji" musi być ustawiona na użycie "PartitionId")

Zapytanie:

```SQL
    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100
```

Ta kwerenda jest prostym filtrem. W związku z tym nie musimy się martwić o partycjonowanie danych wejściowych, które są wysyłane do centrum zdarzeń. Należy zauważyć, że zadania z poziomem zgodności przed 1.2 musi zawierać **partition by partitionid** klauzuli, więc spełnia wymagania #2 z wcześniej. Dla danych wyjściowych, musimy skonfigurować dane wyjściowe centrum zdarzeń w zadaniu, aby klucz partycji ustawiony na **PartitionId**. Ostatnim sprawdziadaniem jest upewnienie się, że liczba partycji wejściowych jest równa liczbie partycji wyjściowych.

### <a name="query-with-a-grouping-key"></a>Zapytanie z kluczem grupowania

* Dane wejściowe: Centrum zdarzeń z 8 partycjami
* Dane wyjściowe: magazyn obiektów blob

Zapytanie:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Ta kwerenda ma klucz grupowania. W związku z tym zdarzenia zgrupowane razem muszą być wysyłane do tej samej partycji Centrum zdarzeń. Ponieważ w tym przykładzie grupujemy według TollBoothID, powinniśmy mieć pewność, że TollBoothID jest używany jako klucz partycji, gdy zdarzenia są wysyłane do Centrum zdarzeń. Następnie w ASA, możemy użyć **PARTITION BY PartitionId** dziedziczyć z tego schematu partycji i włączyć pełną równoległość. Ponieważ dane wyjściowe to magazyn obiektów blob, nie musimy się martwić o konfigurowanie wartości klucza partycji, zgodnie z wymaganiami #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Przykład scenariuszy, które *nie* są żenująco równoległe

W poprzedniej sekcji pokazaliśmy kilka żenująco równoległych scenariuszy. W tej sekcji omówimy scenariusze, które nie spełniają wszystkich wymagań, które mają być żenująco równoległe. 

### <a name="mismatched-partition-count"></a>Niedopasowana liczba partycji
* Dane wejściowe: Centrum zdarzeń z 8 partycjami
* Dane wyjściowe: Centrum zdarzeń z 32 partycjami

W takim przypadku nie ma znaczenia, co to jest kwerenda. Jeśli liczba partycji wejściowych nie pasuje do liczby partycji wyjściowych, topologia nie jest żenująco równoległa.+ Jednak nadal możemy uzyskać pewnego poziomu lub równoległości.

### <a name="query-using-non-partitioned-output"></a>Kwerenda przy użyciu danych wyjściowych nieobjętych partycjami
* Dane wejściowe: Centrum zdarzeń z 8 partycjami
* Wyjście: Usługa Power BI

Dane wyjściowe usługi Power BI obecnie nie obsługują partycjonowania. W związku z tym ten scenariusz nie jest żenująco równolegle.

### <a name="multi-step-query-with-different-partition-by-values"></a>Kwerenda wieloetapowa z różnymi wartościami PARTITION BY
* Dane wejściowe: Centrum zdarzeń z 8 partycjami
* Dane wyjściowe: Centrum zdarzeń z 8 partycjami

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

Jak widać, drugi krok używa **TollBoothId** jako klucz partycjonowania. Ten krok nie jest tym samym, co pierwszy krok, a zatem wymaga od nas przetasowania. 

W poprzednich przykładach pokazano niektóre zadania usługi Stream Analytics, które są zgodne z (lub nie) żenująco równoległej topologii. Jeśli są one zgodne, mają potencjał maksymalnej skali. W przypadku zadań, które nie pasują do jednego z tych profilów, wskazówki dotyczące skalowania będą dostępne w przyszłych aktualizacjach. Na razie należy użyć ogólnych wskazówek w poniższych sekcjach.

### <a name="compatibility-level-12---multi-step-query-with-different-partition-by-values"></a>Poziom zgodności 1.2 — wieloetapowe zapytanie z różnymi wartościami PARTITION BY 
* Dane wejściowe: Centrum zdarzeń z 8 partycjami
* Dane wyjściowe: Centrum zdarzeń z 8 partycjami ("Kolumna klucza partycji" musi być ustawiona na użycie "TollBoothId")

Zapytanie:

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

Poziom zgodności 1.2 domyślnie umożliwia równoległe wykonywanie zapytań. Na przykład kwerenda z poprzedniej sekcji będzie podzielony na partycje tak długo, jak kolumna "TollBoothId" jest ustawiona jako wejściowy klucz partycji. Klauzula PARTITION BY PartitionId nie jest wymagana.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Oblicz maksymalną jednostkę przesyłania strumieniowego zadania
Całkowita liczba jednostek przesyłania strumieniowego, które mogą być używane przez zadanie usługi Stream Analytics zależy od liczby kroków w kwerendzie zdefiniowanej dla zadania i liczby partycji dla każdego kroku.

### <a name="steps-in-a-query"></a>Kroki w kwerendzie
Kwerenda może mieć jeden lub wiele kroków. Każdy krok jest podkwerendą zdefiniowaną przez słowo kluczowe **WITH.** Kwerenda, która znajduje się poza słowem kluczowym **WITH** (tylko jedna kwerenda) jest również liczona jako krok, taki jak instrukcja **SELECT** w następującej kwerendzie:

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

Ta kwerenda ma dwa kroki.

> [!NOTE]
> Ta kwerenda została omówiona bardziej szczegółowo w dalszej części artykułu.
>  

### <a name="partition-a-step"></a>Partycja krok
Partycjonowanie kroku wymaga następujących warunków:

* Źródło danych wejściowych musi być podzielony na partycje. 
* Instrukcja **SELECT** kwerendy musi być odczytywana z podzielonego na partycje źródła danych wejściowych.
* Kwerenda w kroku musi mieć słowo kluczowe **PARTITION BY.**

Gdy kwerenda jest podzielony na partycje, zdarzenia wejściowe są przetwarzane i agregowane w oddzielnych grupach partycji, a zdarzenia wyjściowe są generowane dla każdej z grup. Jeśli chcesz połączone agregacji, należy utworzyć drugi krok nie podzielony na partycje do agregacji.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Oblicz maksymalną liczbę jednostek przesyłania strumieniowego dla zadania
Wszystkie kroki nieobjęte partycją razem można skalować do sześciu jednostek przesyłania strumieniowego (SUs) dla zadania usługi Stream Analytics. Oprócz tego można dodać 6 SUs dla każdej partycji w kroku podzielonym na partycje.
Możesz zobaczyć kilka **przykładów** w poniższej tabeli.

| Zapytanie                                               | Maksymalna liczba SU dla zadania |
| --------------------------------------------------- | ------------------- |
| <ul><li>Kwerenda zawiera jeden krok.</li><li>Krok nie jest podzielony na partycje.</li></ul> | 6 |
| <ul><li>Strumień danych wejściowych jest podzielony na partycje przez 16.</li><li>Kwerenda zawiera jeden krok.</li><li>Krok jest podzielony na partycje.</li></ul> | 96 (6 * 16 partycji) |
| <ul><li>Kwerenda zawiera dwa kroki.</li><li>Żaden z kroków nie jest podzielony na partycje.</li></ul> | 6 |
| <ul><li>Strumień danych wejściowych jest podzielony na partycje przez 3.</li><li>Kwerenda zawiera dwa kroki. Krok wejściowy jest podzielony na partycje, a drugi krok nie jest.</li><li>Instrukcja <strong>SELECT</strong> odczytuje z danych wejściowych podzielonych na partycje.</li></ul> | 24 (18 dla podzielonych na partycje kroków + 6 dla kroków nie podzielonych na partycje |

### <a name="examples-of-scaling"></a>Przykłady skalowania

Poniższa kwerenda oblicza liczbę samochodów w ciągu trzech minut przechodzących przez punkt poboru opłat, który ma trzy budki poboru opłat. Ta kwerenda może być skalowana do sześciu SU.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Aby użyć więcej SUs dla kwerendy, zarówno strumień danych wejściowych i kwerendy muszą być podzielone na partycje. Ponieważ partycja strumienia danych jest ustawiona na 3, następujące zmodyfikowane zapytanie można skalować do 18 SUs:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Gdy kwerenda jest podzielony na partycje, zdarzenia wejściowe są przetwarzane i agregowane w oddzielnych grupach partycji. Zdarzenia wyjściowe są również generowane dla każdej z grup. Partycjonowanie może spowodować pewne nieoczekiwane wyniki, gdy pole **GROUP BY** nie jest kluczem partycji w strumieniu danych wejściowych. Na przykład pole **TollBoothId** w poprzedniej kwerendzie nie jest kluczem partycji **Input1**. W rezultacie dane z TollBooth #1 mogą być rozłożone na wielu partycjach.

Każda partycja **Input1** będzie przetwarzana oddzielnie przez analizę stream analytics. W rezultacie zostanie utworzonych wiele rekordów liczby samochodów dla tego samego punktu poboru opłat w tym samym oknie tumbling. Jeśli nie można zmienić klucza partycji wejściowej, ten problem można rozwiązać, dodając krok bez partycji do wartości agregujących na partycjach, jak w poniższym przykładzie:

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

Ta kwerenda może być skalowana do 24 SUs.

> [!NOTE]
> Jeśli łączysz dwa strumienie, upewnij się, że strumienie są podzielone na partycje przez klucz partycji kolumny, który służy do tworzenia sprzężeń. Upewnij się również, że masz taką samą liczbę partycji w obu strumieniach.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Osiąganie wyższych przepustowości na dużą skalę

[Żenująco równoległe](#embarrassingly-parallel-jobs) zadanie jest konieczne, ale nie wystarczające do utrzymania wyższej przepustowości na dużą skalę. Każdy system pamięci masowej i odpowiadające mu dane wyjściowe usługi Stream Analytics mają różne informacje na temat jak osiągnąć najlepszą możliwą przepustowość zapisu. Podobnie jak w przypadku każdego scenariusza na skalę, istnieją pewne wyzwania, które można rozwiązać za pomocą odpowiednich konfiguracji. W tej sekcji omówiono konfiguracje dla kilku typowych wyjść i zawiera przykłady podtrzymujące szybkość pozyskiwania 1K, 5K i 10K zdarzeń na sekundę.

Poniższe obserwacje używają zadania usługi Stream Analytics z kwerendą bezstanową (passthrough), podstawową udf języka JavaScript, która zapisuje w centrum zdarzeń, usłudze Azure SQL DB lub usłudze Cosmos DB.

#### <a name="event-hub"></a>Centrum zdarzeń

|Szybkość pozyskiwania (zdarzenia na sekundę) | Jednostki przesyłania strumieniowego | Zasoby wyjściowe  |
|--------|---------|---------|
| 1 tys.     |    1    |  2 TU   |
| 5 tys.     |    6    |  6 TU   |
| 10 tys.    |    12   |  10 TU  |

Rozwiązanie [Centrum zdarzeń](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-eventhubs) skaluje się liniowo pod względem jednostek przesyłania strumieniowego (SU) i przepływności, dzięki czemu jest to najbardziej wydajny i wydajny sposób analizowania i przesyłania strumieniowego danych z usługi Stream Analytics. Zadania można skalować do 192 SU, co w przybliżeniu przekłada się na przetwarzanie do 200 MB /s lub 19 bilionów zdarzeń dziennie.

#### <a name="azure-sql"></a>Azure SQL
|Szybkość pozyskiwania (zdarzenia na sekundę) | Jednostki przesyłania strumieniowego | Zasoby wyjściowe  |
|---------|------|-------|
|    1 tys.   |   3  |  S3   |
|    5 tys.   |   18 |  P4   |
|    10 tys.  |   36 |  P6   |

[Usługa Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-azuresql) obsługuje pisanie równolegle, o nazwie Dziedzicz partycjonowanie, ale nie jest domyślnie włączona. Jednak włączenie dziedziczenie partycjonowania, wraz z kwerendą w pełni równoległe, może nie być wystarczające do osiągnięcia wyższej przepływności. Przepływności zapisu SQL zależą znacznie od konfiguracji bazy danych SQL Azure i schematu tabeli. Artykuł [Wydajności wyjściowej SQL](./stream-analytics-sql-output-perf.md) zawiera więcej szczegółów na temat parametrów, które można zmaksymalizować przepływność zapisu. Jak wspomniano w [danych wyjściowych usługi Azure Stream Analytics do usługi Azure SQL Database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) artykuł, to rozwiązanie nie skaluje liniowo jako w pełni równoległy potok poza 8 partycji i może wymagać ponownego partycjonowania przed wyjściem SQL (zobacz [INTO).](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) Jednostki SKU premium są potrzebne do utrzymania wysokich szybkości we/wy wraz z obciążeniem z kopii zapasowych dziennika dzieje się co kilka minut.

#### <a name="cosmos-db"></a>Cosmos DB
|Szybkość pozyskiwania (zdarzenia na sekundę) | Jednostki przesyłania strumieniowego | Zasoby wyjściowe  |
|-------|-------|---------|
|  1 tys.   |  3    | 20K RU  |
|  5 tys.   |  24   | 60K RU  |
|  10 tys.  |  48   | 120K RU |

Dane [wyjściowe usługi Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) firmy Stream Analytics zostały zaktualizowane w celu użycia natywnej integracji na [poziomie zgodności 1.2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Poziom zgodności 1.2 umożliwia znacznie wyższą przepustowość i zmniejsza zużycie ru w porównaniu do 1.1, co jest domyślnym poziomem zgodności dla nowych zadań. Rozwiązanie używa kontenerów usługi CosmosDB podzielonych na partycje na /deviceId, a reszta rozwiązania jest identycznie skonfigurowana.

Wszystkie [usługi przesyłania strumieniowego w skali próbek platformy Azure](https://github.com/Azure-Samples/streaming-at-scale) używać Centrum zdarzeń zasilany przez obciążenia symulujące klientów testowych jako dane wejściowe. Każde zdarzenie wejściowe jest dokumentem JSON 1KB, który łatwo tłumaczy skonfigurowane współczynniki pozyskiwania na współczynniki przepływności (1MB/s, 5MB/s i 10MB/s). Zdarzenia symulują urządzenie IoT wysyłające następujące dane JSON (w skróconej formie) dla urządzeń o przekątnej do 1K:

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
> Konfiguracje mogą ulec zmianie ze względu na różne składniki używane w rozwiązaniu. Aby uzyskać dokładniejsze oszacowanie, dostosuj przykłady, aby pasowały do scenariusza.

### <a name="identifying-bottlenecks"></a>Identyfikowanie wąskich gardeł

Użyj okienka Metryki w zadaniu usługi Azure Stream Analytics, aby zidentyfikować wąskie gardła w potoku. Przejrzyj **zdarzenia wejściowe/wyjściowe** dla przepływności i ["Opóźnienie znaku wodnego"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) lub **zdarzenia zaległe,** aby sprawdzić, czy zadanie nadąża za szybkością wprowadzania. W przypadku metryk Usługi zdarzeń poszukaj **żądań ograniczonego** i odpowiednio dostosuj jednostki progowe. W przypadku metryk usługi Cosmos DB, przejrzyj **maksymalną ilość zużytych ru/s na zakres klucza partycji** w obszarze Przepływność, aby upewnić się, że zakresy kluczy partycji są jednolicie używane. W przypadku usługi Azure SQL DB należy monitorować **we/wy** dziennika i **procesora .**

## <a name="get-help"></a>Uzyskiwanie pomocy

Aby uzyskać dalszą pomoc, wypróbuj nasze [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
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

