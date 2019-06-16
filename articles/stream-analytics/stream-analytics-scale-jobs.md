---
title: Skalowanie w górę i w zadaniach usługi Azure Stream Analytics
description: W tym artykule opisano sposób skalować zadania usługi Stream Analytics, partycjonowanie danych wejściowych, dostosowywanie zapytania i ustawiając zadania jednostek przesyłania strumieniowego.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: f4307da2e74846507cafb9f767a6ccae855e42a2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60816809"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Skalować zadania usługi Azure Stream Analytics, w celu zwiększenia przepływności
W tym artykule pokazano, jak dostosowywanie zapytania usługi Stream Analytics w celu zwiększenia przepływności zadania usługi Stream Analytics. Następującymi wskazówkami umożliwia skalowanie zadania do obsługi wyższe obciążenie i korzystać z zalet więcej zasobów systemowych (np. większą przepustowość, więcej zasobów procesora CPU, więcej pamięci).
Jako warunek wstępny może być konieczne przeczytaj następujące artykuły:
-   [Opis i dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md)
-   [Tworzenie zadań równoległego](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Przypadek 1 — zapytania jest natury pełni równoległego partycjach danych wejściowych
Jeśli zapytanie jest natury pełni równoległego partycjach danych wejściowych, należy wykonać następujące czynności:
1.  Tworzenie zapytania do zaskakująco równoległymi przy użyciu **PARTITION BY** — słowo kluczowe. Zobacz więcej szczegółów w sekcji zaskakująco równoległymi zadaniami [na tej stronie](stream-analytics-parallelization.md).
2.  W zależności od typów danych wyjściowych jest używany w zapytaniu, niektóre dane wyjściowe może albo nie może być równoległego, lub potrzebujesz dalszych zaskakująco równoległymi konfigurację. Na przykład danych wyjściowych SQL, SQL data Warehouse i usługi Power BI nie są równoległego. Dane wyjściowe są zawsze scalane przed wysłaniem do ujścia danych wyjściowych. Obiekty BLOB, tabel, Azure Data Lake Store, magistrali usług i funkcji platformy Azure automatycznie zrównoleglona. Bazy danych cosmos DB i Centrum zdarzeń musi mieć ustawiony na zgodny z konfiguracji PartitionKey **PARTITION BY** pola (zazwyczaj PartitionId). Dla Centrum zdarzeń należy też zwracać szczególną uwagę na jest zgodna z liczbą partycji dla wszystkich danych wejściowych i wszystkie dane wyjściowe, aby uniknąć cross-over między partycjami. 
3.  Uruchamianie zapytania przy użyciu **6 SU** (czyli pełną pojemność pojedynczego węzła obliczeń) do pomiaru maksymalnego osiągalnej przepływności, i jeśli używasz **GROUP BY**, zmierzyć, jak wiele grup (kardynalności) zadanie może dojście. Poniżej przedstawiono ogólne objawy zadania osiągnięcia limitów zasobów systemowych.
    - Metryki % SU wynosi ponad 80%. Oznacza to, pamięci, których użycie jest wysokie. Czynniki przyczyniające się do wzrostu tej metryki są opisane [tutaj](stream-analytics-streaming-unit-consumption.md). 
    -   Sygnatura czasowa danych wyjściowych jest objęte względem czas zegarowy. W zależności od logiki kwerendy sygnatura czasowa danych wyjściowych może być przesunięcie logiki, od czas zegarowy. Jednak należy postępu w przybliżeniu tej samej stawki. Jeśli sygnatura czasowa danych wyjściowych jest objęte dalsze i dalsze tyle, jest wskaźnik, który jest overworking systemu. Może być to wynikiem podrzędnego dane wyjściowe obiektu sink ograniczania przepływności lub wysokie wykorzystanie procesora CPU. Nie firma Microsoft zapewnia metryki użycia procesora CPU w tej chwili, dlatego może być trudne do odróżniania dwóch.
        - Jeśli problem będzie się z powodu dławienia ujścia, może być konieczne zwiększenie liczby partycji danych wyjściowych (i również dane wejściowe partycje zapewnienie w pełni równoległego zadania), lub zwiększ ilość zasobów obiektu sink (na przykład liczbę jednostek żądania dla usługi CosmosDB).
    - W diagramie zadania znajduje się na partycji zaległości zdarzenie metryki dla każdego danych wejściowych. Jeśli Metryka zdarzenia zaległości stale rośnie, jest również wskazuje, że zasób systemowy jest ograniczony (albo z powodu ograniczania ujścia danych wyjściowych lub wysokie użycie procesora CPU).
4.  Po określeniu limitów co 6 zadania SU może nawiązać połączenie, należy można ekstrapolację liniowo wydajnością przetwarzania zadania w miarę dodawania więcej SUs, przy założeniu, że nie masz żadnych wynikające z niesymetryczności danych, które sprawia, że niektóre partycji "gorącymi".

> [!NOTE]
> Wybierz odpowiednią liczbę jednostek przesyłania strumieniowego: Ponieważ usługi Stream Analytics tworzy węzeł przetwarzania dla każdego 6 SU dodane, najlepiej jest zapewnienie liczbę węzłów dzielnikiem liczby partycji danych wejściowych, dzięki czemu partycje mogą być równomiernie rozłożona na węzły.
> Na przykład mieć mierzona z 6 SU zadania można osiągnąć 4 MB/s, takich jak przetwarzanie częstotliwość i liczba Twoich partycję danych wejściowych wynosi 4. Można uruchomić zadanie z 12 polecenia SU to osiągnąć szybkość przetwarzania w usłudze około 8 MB/s lub 24 SU, aby osiągnąć 16 MB/s. Następnie można zdecydować, kiedy należy zwiększyć liczbę SU zadania jaka wartość jako funkcja częstotliwość danych wejściowych.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Przypadek 2 — Jeśli zapytanie nie jest zaskakująco równoległymi.
Jeśli zapytanie nie jest zaskakująco równoległymi, możesz wykonać następujące czynności.
1.  Rozpoczyna się od zapytania bez **PARTITION BY** aby, najpierw należy unikać podziału na partycje złożoności i uruchomić zapytania z 6 SU do pomiaru maksymalnego obciążenia, podobnie jak w [przypadek 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Jeśli przewidywanego obciążenia można osiągnąć, w okresie przepływności, wszystko będzie gotowe. Alternatywnie można mierzyć to samo zadanie uruchomione w 3 SU i 1 jednostka wyszukiwania, aby dowiedzieć się z minimalną liczbą SU, który działa w przypadku danego scenariusza.
3.  Jeśli nie można osiągnąć żądany przepływności, spróbuj przerwanie działania zapytania w wielu kroków, jeśli jest to możliwe, nie ma już wiele kroków i przydzielić SU do 6 dla każdego kroku w zapytaniu. Na przykład jeśli masz kroki 3, Przydziel 18 SU w opcji "Skalowanie".
4.  Podczas uruchamiania takie zadania, Stream Analytics umieszcza każdego kroku na swój własny węzeł z dedykowanych zasobów SU 6. 
5.  Jeśli nadal nie został osiągnięty urządzenie docelowe obciążenia, możesz spróbować użyć **PARTITION BY** od bliżej kroki w danych wejściowych. Aby uzyskać **GROUP BY** operator, który może nie być w sposób naturalny partitionable, można używać lokalnych/globalne wzorca agregacji do wykonania podzielonym na partycje **GROUP BY** następuje niepartycjonowana **GROUP BY** . Na przykład jeśli chcesz policzyć ile samochodów skończywszy na stoisku każdy płatny co 3 minuty, a ilość danych jest poza to, co może być obsługiwany przez 6 SU.

Zapytanie:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
W powyższym zapytaniu są zliczanie samochodów na stoisku płatny dla każdej partycji, a następnie dodanie liczby z wszystkie partycje ze sobą.

Po podzielona na partycje, dla każdej partycji w tym kroku należy przydzielić maksymalnie 6 polecenia SU, każda partycja o 6 SU jest maksymalna, więc każda partycja można umieścić we własnej węzeł przetwarzania.

> [!Note]
> Jeśli zapytanie nie może zostać podzielona na partycje, dodanie dodatkowych SU w zapytaniu wielu kroków może nie zawsze poprawia przepływności. Jednym ze sposobów wydajność jest aby zmniejszyć wolumin na początkowe kroki przy użyciu lokalnego/globalne wzorca agregacji, zgodnie z powyższym opisem w kroku 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Przypadek 3 — wiele niezależnych kwerend działają w ramach zadania.
Dla niektórych niezależnego dostawcy oprogramowania zastosowań, gdzie jest bardziej wydajne do przetwarzania danych z wielu dzierżaw w jednym zadaniu, przy użyciu oddzielnych dane wejściowe i wyjściowe dla każdego dzierżawcy, użytkownik może pozostać uruchomiony jeszcze kilka (na przykład 20) niezależnych kwerend w jednym zadaniu. Zakłada się, jest stosunkowo małe obciążenie każdego takiego podzapytania. W takim przypadku możesz wykonać następujące czynności.
1.  W tym przypadku nie używaj **PARTITION BY** w zapytaniu
2.  Zmniejsz liczbę partycji danych wejściowych do najmniejszej możliwej wartości 2, korzystania z Centrum zdarzeń.
3.  Uruchom zapytanie, za pomocą polecenia SU 6. Za pomocą oczekiwanego obciążenia dla każdej podzapytania należy dodać dowolną liczbę takich podzapytań jak to możliwe, dopóki zadanie jest osiągnięcia limitów zasobów systemowych. Zapoznaj się [przypadek 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) dla objawy w takiej sytuacji.
4.  Po w przypadku osiągnięcia limitu podzapytania mierzony powyżej przystąpić do dodawania podzapytanie do nowego zadania. Liczba zadań do uruchomienia w zależności od liczby zapytań niezależnych powinna być dość liniowego, przy założeniu, że masz obciążenie pochylanie. Można następnie prognozy, ile 6 zadania polecenia SU, należy uruchomić w zależności od liczby dzierżawców, którą chcesz obsługiwać.
5.  Jeśli sprzężenia danych odwołania z takich zapytań, union dane wejściowe razem przed dołączeniem o takiej samej odwołują się do danych. Następnie Podziel się zdarzenia, jeśli to konieczne. W przeciwnym razie każdego odwołania sprzężenia danych przechowuje kopie danych referencyjnych w pamięci, prawdopodobnie niepotrzebnie porotwórcze się użycia pamięci.

> [!Note] 
> Ilu dzierżawców do umieszczenia w poszczególnych zadaniach?
> Ten wzorzec zapytania często ma dużą liczbę podzapytania i skutkuje bardzo dużych i złożonych topologii. Kontroler zadania nie można obsłużyć dużą topologii. Jako ogólną regułę można przyjąć pozostanie w obszarze 40 dzierżaw 1 zadanie SU i 60 dzierżaw 3 SU i 6 zadania SU. Gdy przekraczają pojemności kontrolera, zadanie nie zostanie pomyślnie uruchomiona.



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

