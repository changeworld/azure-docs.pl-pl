---
title: Użyj zapytania paralelizacja i skali w Azure Stream Analytics
description: W tym artykule opisano sposób skalowania zadania usługi analiza strumienia Konfigurowanie partycji wejściowych, dostosowywanie definicji zapytania i ustawiając zadania jednostki przesyłania strumieniowego.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: fd373093264122fda45697acc81929d3c723c957
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Korzystać z zapytania paralelizacja w Azure Stream Analytics
W tym artykule przedstawiono sposób wykorzystać paralelizacja Azure Stream Analytics. Sposób skalowania zadania usługi analiza strumienia, konfigurując wejściowych partycji i dostrajania analytics definicji zapytania.
Jako warunek wstępny, warto należy zapoznać się z pojęcie jednostki przesyłania strumieniowego opisanego w [omówienie i dostosować jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Co to są elementy zadania Stream Analytics?
Definicji zadania Stream Analytics zawiera dane wejściowe, zapytań i danych wyjściowych. Dane wejściowe są, gdy zadanie odczytuje strumienia danych z. Zapytanie jest używany do transformacji strumienia danych wejściowych, a dane wyjściowe są, gdy zadanie wysyła wyników zadania do.  

Zadanie wymaga co najmniej jedno źródło danych wejściowych do strumieniowego przesyłania danych. Źródło dla wejścia strumienia danych mogą być przechowywane w Centrum zdarzeń platformy Azure lub w magazynie obiektów blob Azure. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md) i [rozpocząć korzystanie z usługi Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Partycje w źródłach i wychwytywanie
Skalowanie zadania Stream Analytics korzysta z partycjami w danych wejściowych lub wyjściowych. Partycjonowanie umożliwia dzielenia danych na podzestawy oparte na kluczu partycji. Proces, który używa danych (na przykład zadanie analizy przesyłania strumieniowego) może wykorzystywać i zapisać różnych partycji równolegle, co zwiększa przepustowość. 

### <a name="inputs"></a>Dane wejściowe
Wszystkie dane wejściowe Azure Stream Analytics można wykorzystać partycjonowania:
-   EventHub (należy ustawić klucz partycji jawnie z PARTITION BY słowem kluczowym)
-   Centrum IoT (należy ustawić klucz partycji jawnie z PARTITION BY słowem kluczowym)
-   Blob Storage

### <a name="outputs"></a>Dane wyjściowe

Podczas pracy z usługi Stream Analytics, możesz korzystać z partycjonowania dla większości sink danych wyjściowych. Więcej informacji na temat partycjonowania danych wyjściowych jest dostępna na [partycjonowania sekcji danych wyjściowych strony](stream-analytics-define-outputs.md#partitioning).

Wyjść usługi Power BI, SQL i magazyn danych SQL nie obsługują partycjonowania. Jednak można nadal podzielić danych wejściowych zgodnie z opisem w [w tej sekcji](#multi-step-query-with-different-partition-by-values) 

Aby uzyskać więcej informacji o partycjach zobacz następujące artykuły:

* [Omówienie funkcji usługi Event Hubs](../event-hubs/event-hubs-features.md#partitions)
* [Partycjonowanie danych](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)


## <a name="embarrassingly-parallel-jobs"></a>Embarrassingly równoległych zadań
*Embarrassingly równoległych* zadanie jest najbardziej skalowalny scenariusza mamy w Azure Stream Analytics. Jedna partycja danych wejściowych do jednego wystąpienia zapytania łączy się jedną partycję w danych wyjściowych. Równoległość ten ma następujące wymagania:

1. Logika zapytania zależy od tego samego klucza przetwarzanych przez to samo wystąpienie zapytania, należy się upewnić, że zdarzenia przejdź do tej samej partycji dane wejściowe. Centra zdarzeń lub Centrum IoT oznacza to, że dane zdarzeń muszą mieć **PartitionKey** zestawu wartości. Alternatywnie można użyć nadawców podzielonym na partycje. W przypadku magazynu obiektów blob oznacza to, że zdarzenia są wysyłane do tego samego folderu partycji. Jeśli logika zapytania nie wymaga tego samego klucza do przetworzenia przez to samo wystąpienie zapytania, możesz zignorować to wymaganie. Przykładem tego logiki może być prostego zapytania wybierz projekt filtru.  

2. Po danych jest rozmieszczona na stronie wprowadzania, to należy się upewnić, że zapytanie jest podzielona na partycje. Wymaga to użycia **PARTITION BY** wszystkich kroków. Wiele kroków są dozwolone, ale wszystkie muszą podzielone na partycje przy użyciu tego samego klucza. Obecnie, podziału klucza musi być ustawiona na **PartitionId** aby pełni równoległe zadania.  

3. Większość wyjście mogą wykorzystać partycjonowania, jednak jeśli jest używany typ danych wyjściowych która nie obsługuje partycjonowanie zadania nie będzie w pełni równoległych. Zapoznaj się [output sekcji](#outputs) więcej szczegółów.

4. Liczby partycji wejściowych musi być równa liczbie partycji danych wyjściowych. Dane wyjściowe z magazynu obiektów blob nie obsługuje obecnie partycji. Ale nie szkodzi, ponieważ dziedziczy ona schemat partycjonowania nadrzędnego zapytania. Poniżej przedstawiono przykładowe wartości partycji, umożliwiających pełni równoległe zadania:  

   * 8 partycje wejściowych Centrum zdarzeń i Centrum zdarzeń 8 output partycji
   * 8 partycje wejściowych Centrum zdarzeń i danych wyjściowych z magazynu obiektów blob  
   * 8 partycje wejściowych Centrum Iot i Centrum zdarzeń 8 output partycji
   * 8 partycje wejściowych magazynu obiektów blob i dane wyjściowe z magazynu obiektów blob  
   * 8 obiektu blob magazynu wejściowego partycji i 8 partycje danych wyjściowych Centrum zdarzeń  

W poniższych sekcjach omówiono niektóre przykładowe scenariusze, które są embarrassingly równoległe.

### <a name="simple-query"></a>Proste zapytanie

* Wejście: Centrum zdarzeń z partycjami 8
* Dane wyjściowe: Centrum zdarzeń z partycjami 8

Zapytanie:

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

To zapytanie jest proste filtru. W związku z tym firma Microsoft nie trzeba martwić partycjonowanie danych wejściowych, który jest wysyłany do Centrum zdarzeń. Należy zauważyć, że kwerenda zawiera **PARTYCJI przez PartitionId**, więc jego spełnia wymagania #2 z wcześniej. Dla danych wyjściowych, należy skonfigurować w ramach zadania, aby zestaw kluczy partycji do danych wyjściowych Centrum zdarzeń **PartitionId**. Jeden ostatni test jest upewnij się, że liczba partycji wejściowy jest równa liczbie partycji danych wyjściowych.

### <a name="query-with-a-grouping-key"></a>Zapytanie z kluczem grupowania

* Wejście: Centrum zdarzeń z partycjami 8
* Danych wyjściowych: Magazyn obiektów Blob

Zapytanie:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Ta kwerenda zawiera klucz grupowania. W związku z tym należy wysłać zdarzenia zgrupowane w tej samej partycji Centrum zdarzeń. Ponieważ w tym przykładzie mamy Grupuj według TollBoothID, możemy Pamiętaj, że TollBoothID jest używany jako klucza partycji zdarzenia są wysyłane do Centrum zdarzeń. Następnie w ASA, możemy użyć **PARTYCJI przez PartitionId** dziedziczy ten schemat partycji i włączyć pełną paralelizacja. Ponieważ dane wyjściowe magazynu obiektów blob, firma Microsoft nie trzeba martwić się o konfigurowaniu wartość klucza partycji, zgodnie z harmonogramem wymaganie #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Przykładowe scenariusze, które są *nie* embarrassingly równoległych

W poprzedniej sekcji możemy pokazano kilka scenariuszy embarrassingly równoległych. W tej sekcji omówiono scenariusze, które nie spełniają wszystkie wymagania dotyczące być embarrassingly równoległe. 

### <a name="mismatched-partition-count"></a>Liczba partycji niezgodne
* Wejście: Centrum zdarzeń z partycjami 8
* Dane wyjściowe: Centrum zdarzeń z partycjami 32

W takim przypadku nie ma znaczenia, zapytanie jest. Jeśli liczba partycji wejściowych nie odpowiada liczba partycji danych wyjściowych, topologia nie jest embarrassingly równolegle. + jednak firma Microsoft może nadal otrzymywać niektórych poziom lub paralelizacja.

### <a name="query-using-non-partitioned-output"></a>Zapytań przy użyciu niepartycjonowany danych wyjściowych
* Wejście: Centrum zdarzeń z partycjami 8
* Dane wyjściowe: usługi Power BI

Wyjście usługi Power BI aktualnie nie obsługuje partycjonowanie. Dlatego w tym scenariuszu nie jest embarrassingly równoległych.

### <a name="multi-step-query-with-different-partition-by-values"></a>Zapytanie wieloetapowych o różnych wartościach PARTITION BY
* Wejście: Centrum zdarzeń z partycjami 8
* Dane wyjściowe: Centrum zdarzeń z partycjami 8

Zapytanie:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Jak widać, drugi etap używa **TollBoothId** jako klucza partycji. Ten krok nie jest taka sama, co w pierwszym kroku i dlatego wymaga do zrobienia losowa. 

W poprzednich przykładach pokazano, niektóre zadania Stream Analytics, które odpowiadają (lub nie) embarrassingly równoległych topologii. Jeśli są one zgodne, mają one potencjalnie maksymalną skalę. Aktualizacje dla zadania, które nie pasują do jednego z tych profilów skalowania wskazówki będą dostępne w przyszłości. Na razie użyj ogólne wskazówki w poniższych sekcjach.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Oblicz maksymalną jednostki zadania przesyłania strumieniowego
Całkowita liczba jednostek przesyłania strumieniowego, które mogą być używane przez zadanie usługi Stream Analytics zależy od liczby kroków w zapytaniu zdefiniowane dla zadania i liczby partycji dla każdego kroku.

### <a name="steps-in-a-query"></a>Kroki w kwerendzie
Kwerenda może mieć jeden lub wiele kroków. Każdy krok jest zdefiniowane przez podzapytania **WITH** — słowo kluczowe. Zapytanie, które znajduje się poza **WITH** — słowo kluczowe (tylko w jednej kwerendzie) również będzie traktowany jako krok, na przykład **wybierz** instrukcji w następującym zapytaniu:

Zapytanie:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

Ta kwerenda zawiera dwa kroki.

> [!NOTE]
> To zapytanie jest omówiona bardziej szczegółowo w dalszej części tego artykułu.
>  

### <a name="partition-a-step"></a>Krok partycji
Partycjonowanie krok wymaga spełnienia następujących warunków:

* Źródło danych wejściowych musi być partycjonowane. 
* **Wybierz** instrukcji zapytania musi odczytywać partycjonowanej źródło danych wejściowych.
* Zapytanie w ramach kroku musi mieć **PARTITION BY** — słowo kluczowe.

Jeśli zapytanie jest podzielona na partycje, zdarzenia wejściowe są przetwarzane i zagregowane w oddzielnej partycji grupy, a dane wyjściowe zdarzenia są generowane dla poszczególnych grup. Jeśli chcesz połączonych agregacja, należy utworzyć drugi etap niepartycjonowany do zagregowania.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Obliczenia maksymalnej liczby jednostek w zadaniu przesyłania strumieniowego
Wszystkie kroki niepartycjonowany razem można skalować do sześciu jednostki przesyłania strumieniowego (SUs) dla zadania usługi analiza strumienia. Oprócz tego można dodać SUs 6 dla każdej partycji w kroku podzielonym na partycje.
Niektóre widoczne **przykłady** w poniższej tabeli.

| Zapytanie                                               | Maksymalna liczba SUs zadania |
| --------------------------------------------------- | ------------------- |
| <ul><li>Zapytanie zawiera w jednym kroku.</li><li>Krok nie jest podzielona na partycje.</li></ul> | 6 |
| <ul><li>Strumień danych wejściowych jest podzielona na partycje przy 16.</li><li>Zapytanie zawiera w jednym kroku.</li><li>Krok jest podzielona na partycje.</li></ul> | 96 (6 * 16 partycje) |
| <ul><li>Zapytanie zawiera dwa kroki.</li><li>Żadne kroki są podzielone na partycje.</li></ul> | 6 |
| <ul><li>Strumień danych wejściowych jest podzielona na partycje przez 3.</li><li>Zapytanie zawiera dwa kroki. Wejściowych kroku jest podzielona na partycje, a drugi etap nie.</li><li><strong>Wybierz</strong> instrukcji odczytuje z podzielonym na partycje danych wejściowych.</li></ul> | 24 (18 partycjonowanej kroki + 6 niepartycjonowany czynności |

### <a name="examples-of-scaling"></a>Przykłady skalowania

Następujące zapytanie oblicza liczbę samochodów w ramach pośrednictwa stacji przez, który ma trzy tollbooths okna trzy minuty. To zapytanie może być skalowana maksymalnie sześć SUs.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Aby użyć więcej SUs dla zapytania, musi być podzielony zarówno strumienia danych wejściowych, jak i zapytania. Ponieważ partycji strumień danych jest ustawiona na 3, następujące zmodyfikowanej zapytania mogą być skalowane maksymalnie 18 SUs:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Zapytanie jest podzielona na partycje, zdarzenia wejściowe są przetwarzane i zagregowane w oddzielnej partycji grup. Dane wyjściowe zdarzenia są także generowane dla poszczególnych grup. Partycjonowanie może powodować pewne nieoczekiwane wyniki podczas **GROUP BY** pole nie jest częścią klucza partycji w strumieniu danych wejściowych. Na przykład **TollBoothId** pole w poprzednich zapytań nie jest klucz partycji **Input1**. Wynik jest, że dane z budki #1 może rozprzestrzeniać się w wielu partycji.

Każdy z **Input1** partycje będą przetwarzane oddzielnie przez Stream Analytics. W związku z tym wiele rekordów liczba samochodów dla tego samego budki w tym samym oknie wirowania zostanie utworzony. Jeśli nie można zmienić klucza partycji wejściowych, można naprawić ten problem, dodając krok-partition do wartości zagregowanych w partycji, jak w poniższym przykładzie:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

To zapytanie może być skalowana do 24 SUs.

> [!NOTE]
> Jeśli są Sprzęganie dwóch strumieni, upewnij się, że strumienie są dzielone przez klucz partycji kolumny, która służy do tworzenia sprzężenia. Upewnij się również mieć taką samą liczbę partycji w obu strumieni.
> 
> 





## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

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

[microsoft.support]: http://support.microsoft.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

