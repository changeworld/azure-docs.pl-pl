---
title: Skalowanie zadania usługi analiza strumienia w celu zwiększenia przepływności | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skalować zadania usługi analiza strumienia Konfigurowanie partycji wejściowych, dostosowywanie definicji zapytania i ustawiając zadania jednostki przesyłania strumieniowego.
keywords: przesyłanie strumieniowe, danych przesyłania strumieniowego przetwarzania danych, dostroić analityka
services: stream-analytics
documentationcenter: ''
author: JSeb225
manager: ryanw
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/22/2017
ms.author: jeanb
ms.openlocfilehash: 2e0487a9e4cd6346312c6817ef2768556cba72ba
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="scale-azure-stream-analytics-jobs-to-increase--throughput"></a>Skalowanie zadania usługi analiza strumienia Azure w celu zwiększenia przepływności
W tym artykule przedstawiono sposób dostrajania Stream Analytics zapytanie w celu zwiększenia przepływności zadań przesyłania strumieniowego usługi Analytics. Następujący Przewodnik umożliwia skalowanie zadania obsługi większych obciążeń i wykorzystać więcej zasobów systemowych (np. większą przepustowość, więcej zasobów procesora CPU, więcej pamięci).
Warunkiem wstępnym konieczne może być przeczytaj następujące artykuły:
-   [Opis i dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md)
-   [Tworzenie zadań działania równoległego](stream-analytics-parallelization.md)


## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Przypadek 1 – zapytania jest z założenia pełni działania równoległego między partycjami wejściowych
Jeśli zapytanie jest z założenia pełni działania równoległego między partycjami wejściowych, należy wykonać następujące czynności:
1.  Tworzyć kwerendy do embarrassingly równoległych przy użyciu **PARTITION BY** — słowo kluczowe. Dowiedz się więcej w sekcji Embarrassingly równoległych zadań [na tej stronie](stream-analytics-parallelization.md).
2.  W zależności od typów danych wyjściowych używane w zapytaniu, niektóre dane wyjściowe może albo nie może być działania równoległego, lub w celu konfiguracji do embarrassingly równoległych. Na przykład danych wyjściowych SQL magazynu danych SQL i usługi Power BI nie są działania równoległego. Dane wyjściowe są scalane zawsze przed wysłaniem do ujścia danych wyjściowych. Obiekty BLOB, tabel, ADLS, magistrali usług i funkcji platformy Azure automatycznie zarządzana przetwarzaniem. CosmosDB i Centrum zdarzeń musi być dopasowana z konfiguracji PartitionKey **PARTITION BY** pola (zazwyczaj PartitionId). Centrum zdarzeń również należy zwrócić uwagę dodatkowe pasuje do liczby partycji dla wszystkich danych wejściowych i wszystkie dane wyjściowe, aby uniknąć między w tryb failover między partycji. 
3.  Uruchom zapytanie z **6 SU** (czyli pełną pojemność jeden węzeł przetwarzania danych) do pomiaru maksymalnego osiągalna przepływność, i jeśli są używane **GROUP BY**, miary można liczbę grup (Kardynalność) zadanie dojście. Ogólne objawy zadania naciśnięcie ograniczeń zasobów systemowych są następujące.
    - Metryki użycia % SU jest ponad 80%. Oznacza to, pamięci, których użycie jest wysokie. Opisano czynniki przyczyniające się do zwiększenia ta metryka [tutaj](stream-analytics-streaming-unit-consumption.md). 
    -   Sygnatura czasowa danych wyjściowych jest objętych względem czas zegarowy. W zależności od logiki kwerendy sygnatury czasowej danych wyjściowych może być offset logiki z czas zegarowy. Jednak należy postępu w przybliżeniu tempie. Jeśli znacznik czasu danych wyjściowych jest objęte dalsze i dalsze za, jest wskaźnik jest overworking systemu. Może być wynikiem podrzędne dane wyjściowe obiektu sink ograniczania przepustowości lub wysokie użycie procesora CPU. Nie udostępniamy metryki użycia procesora CPU w tej chwili, więc może być trudne do odróżnienia dwa.
        - W przypadku problemu z powodu dławienia zbiornika, konieczne może być zwiększenie liczby partycji w danych wyjściowych (i również wejściowego partycji, aby zachować pełni działania równoległego zadania), lub zwiększ ilość zasobów zbiornika (na przykład liczba jednostek żądania dla CosmosDB).
    - Na diagramie zadania jest na partycji zaległości zdarzeń metryki dla każdej danych wejściowych. Jeśli Metryka zdarzenia zaległości stale, również jest wskaźnik, że zasób systemowy jest ograniczone (albo z powodu ograniczania ujście danych wyjściowych lub wysokie procesor CPU).
4.  Po określeniu limitów co 6 zadania SU może nawiązać połączenie, możesz umożliwiają ekstrapolację liniowo wydajności przetwarzania zadania dodawania więcej SUs, przy założeniu, że nie masz żadnych danych pochylanie, które powoduje, że niektóre partycji "gorących".
>[!Note]
> Wybierz prawa liczbę jednostek przesyłania strumieniowego: ponieważ Stream Analytics tworzy węzeł przetwarzania dla każdego 6 SU dodane, najlepiej powiększając liczby węzłów dzielnikiem liczby partycji wejściowych partycji można równomiernie rozłożony między węzły.
> Na przykład mieć mierzona z 6 SU zadania można osiągnąć 4 MB/s przetwarzania szybkości i liczba partycji wejściowe to 4. Można uruchamiać zadanie z 12 SU do osiągnięcia około 8 szybkości przetwarzania MB/s lub 24 SU do osiągnięcia 16 MB/s. Następnie można zdecydować, kiedy należy zwiększyć liczbę SU na jakie korzyści zadania jako funkcja częstotliwość wprowadzania.



## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Przypadek 2 - Jeśli zapytanie nie jest embarrassingly równoległych.
Jeżeli zapytanie nie jest embarrassingly równoległe, należy wykonać następujące kroki.
1.  Rozpoczyna się od zapytania bez **PARTITION BY** Aby uniknąć partycjonowania złożoność i uruchom zapytanie z 6 SU do pomiaru maksymalnego obciążenia jak w [przypadek 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Jeżeli można osiągnąć przewidywane obciążenia w okresie przepływności, wszystko będzie gotowe. Alternatywnie można mierzyć to samo zadanie uruchomione w 3 SU i 1 SU, aby dowiedzieć się, minimalną liczbę SU, która działa w przypadku danego scenariusza.
3.  Nie można osiągnąć żądany przepływności, próbować Podziel zapytanie na wielu kroków, jeśli to możliwe, jeśli nie ma już wielu kroków i przydzielić SU do 6 dla każdego kroku w zapytaniu. Na przykład jeśli masz kroki 3, Przydziel 18 SU w opcji "Skalować".
4.  Podczas uruchamiania takie zadania, Stream Analytics umieszcza każdy krok na własne węzła z zasobów dedykowanych 6 SU. 
5.  Jeśli nadal nie zostały osiągnięte urządzenie docelowe obciążenia, możesz spróbować użyć **PARTITION BY** od bliżej kroki w danych wejściowych. Dla **GROUP BY** operator, który nie może być naturalnie partitionable, można używać lokalnego/globalne wzorca agregacji do wykonania partycjonowanej **GROUP BY** następuje niepartycjonowany **GROUP BY** . Na przykład jeśli chcesz liczba samochodów ile pośrednictwa każdej kabiny przez co 3 minuty, a ilość danych jest poza co są obsługiwane przez 6 SU.

Zapytanie:

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

W powyższym zapytaniu są zliczania samochodów na stoisku przez przypadających na partycję, a następnie dodanie licznika z wszystkie partycje razem.

Po podzielona na partycje, dla każdej partycji w kroku, należy przydzielić do 6 SU, każda partycja o 6 SU to maksymalna, więc każda partycja można umieścić we własne węzła przetwarzania.

> [!Note]
> Jeśli nie można podzielić na partycje kwerendy, dodanie dodatkowych SU w zapytaniu wielu czynności może nie zawsze poprawia przepływności. Jednym ze sposobów uzyskania wydajności jest zmniejszyć wolumin na początkowe kroki przy użyciu lokalnej/globalne wzorca agregacji, zgodnie z powyższym opisem w kroku 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>W przypadku 3 - wiele niezależnych kwerend działają w ramach zadania.
Dla niektórych niezależnego dostawcy oprogramowania zastosowań, gdzie jest bardziej ekonomiczne do przetwarzania danych z wielu dzierżawców w jednym zadaniu, przy użyciu osobnych wejścia i wyjścia dla każdego dzierżawcy, może to spowodować systemem dość kilka (na przykład 20) niezależnych kwerend w jednym zadaniu. Zakłada się, każdy taki podzapytania obciążenia jest stosunkowo mały. W takim przypadku można wykonać następujące kroki.
1.  W takim przypadku nie należy używać **PARTITION BY** w kwerendzie
2.  Zredukowanie liczby partycji wejściowych najniższą wartość możliwe, 2, korzystając z Centrum zdarzeń.
3.  Uruchom zapytanie z 6 SU. Z oczekiwanego obciążenia dla każdego podzapytania dodać dowolną liczbę takich podzapytania jak to możliwe, dopóki zadanie jest naciśnięcie ograniczeń zasobów systemowych. Zapoznaj się [przypadek 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) dla objawy w takim przypadku.
4.  Po czy osiągnięto limit podzapytania mierzony powyżej, przystąpić do dodawania podzapytanie do nowego zadania. Liczba zadań uruchamianych w zależności od liczby niezależnych zapytań powinna być dość liniowego, przy założeniu, że nie masz żadnych obciążenia pochylenia. Następnie można prognozy liczbę zadań SU 6 należy uruchomić w zależności od liczby dzierżawców, którą chcesz obsługiwać.
5.  Jeśli sprzężenia danych odwołania z tych zapytań, należy Unii wejść, przed dołączeniem danych o tej samej odwołania, następnie podzielić się zdarzenia w razie potrzeby. W przeciwnym razie wartość każdego sprzężenia danych odwołania przechowuje kopie danych referencyjnych w pamięci, prawdopodobnie niepotrzebnie wdmuchiwanie się użycie pamięci.

> [!Note] 
> Ilu dzierżawców w każdym zadaniu?
> Ten wzorzec zapytania często ma dużą liczbę podzapytania i powoduje bardzo dużych i złożonych topologii. Kontroler zadania nie można obsłużyć dużych topologii. Jako zasadą pozostanie mniej niż 40 dzierżaw 1 zadania SU i 60 dzierżaw 3 SU i 6 zadania SU. Gdy Przekraczasz pojemności kontrolera zadanie nie zostanie uruchomiona pomyślnie.


## <a name="an-example-of-stream-analytics-throughput-at-scale"></a>Przykład Stream Analytics przepustowość na dużą skalę
Aby ułatwić zrozumienie, jak skalować zadania usługi analiza strumienia, wykonane eksperyment na podstawie danych wprowadzonych z urządzenia malina Pi. Tego eksperymentu poinformować nas, zobacz wpływu na przepustowość wiele jednostek przesyłania strumieniowego i partycje.

W tym scenariuszu urządzenie wysyła dane czujników (klienci) do Centrum zdarzeń. Przesyłanie strumieniowe Analytics przetwarza dane i wysyła alert ani w statystyce jako dane wyjściowe do innego Centrum zdarzeń. 

Klient wysyła dane czujników w formacie JSON. Dane wyjściowe jest również w formacie JSON. Dane wygląda następująco:

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Następujące zapytanie służy do wysyłania alertu po wyłączeniu światło:

    SELECT AVG(lght), "LightOff" as AlertText
    FROM input TIMESTAMP BY devicetime 
    PARTITION BY PartitionID
    WHERE lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>Miara przepływności

W tym kontekście przepływność wynosi ilość danych wejściowych przetworzone przez usługę Stream Analytics w stałej ilości czasu. (Firma Microsoft mierzy przez 10 minut.) Aby uzyskać najlepsze przepływności przetwarzania danych wejściowych, zarówno wejściowego strumienia danych i zapytania zostały podzielone na partycje. Jest dostępna **COUNT()** do mierzenia, ile zdarzenia wejściowe były przetwarzane w kwerendzie. Aby upewnij się, że zadania nie oczekiwał po prostu dla zdarzenia wejściowe przejdzie, każdej partycji Centrum zdarzeń wejściowych został wstępnie ładowane z około 300 MB danych wejściowych.

W poniższej tabeli przedstawiono wyniki, którą widzieliśmy, gdy firma Microsoft zwiększyć liczbę jednostek przesyłania strumieniowego i odpowiadająca mu partycja liczby w usłudze event hubs.  

<table border="1">
<tr><th>Partycje wejściowych</th><th>Partycje danych wyjściowych</th><th>Jednostki przesyłania strumieniowego</th><th>Długoterminowa przepustowość
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/s</td>
</tr>
</table>

I Wykres ukazuje wizualizacji relacji między usługami SUs i przepływności.

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

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

