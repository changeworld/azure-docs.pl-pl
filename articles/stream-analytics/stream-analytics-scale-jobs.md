---
title: Skalowanie w górę i na zewnątrz w zadaniach usługi Azure Stream Analytics
description: W tym artykule opisano sposób skalowania zadania usługi Stream Analytics przez partycjonowanie danych wejściowych, dostrajanie kwerendy i ustawianie jednostek przesyłania strumieniowego zadań.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: d828103bef8e57f5d0cdfe6c243c52e2d0526663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257550"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Skalowanie zadania usługi Azure Stream Analytics w celu zwiększenia przepływności
W tym artykule pokazano, jak dostroić zapytanie usługi Stream Analytics, aby zwiększyć przepływność zadań usługi Streaming Analytics. Poniższy przewodnik służy do skalowania zadania do obsługi większego obciążenia i korzystać z większej ilości zasobów systemowych (takich jak większa przepustowość, więcej zasobów procesora, więcej pamięci).
Warunkiem wstępnym może być przeczytanie następujących artykułów:
-   [Opis i dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md)
-   [Tworzenie równoległych zadań](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Przypadek 1 — Zapytanie jest z natury w pełni równoległe między partycjami wejściowymi
Jeśli zapytanie jest z natury w pełni równoległe między partycjami wejściowymi, można wykonać następujące kroki:
1.  Autor zapytania być żenująco równolegle za pomocą słowa kluczowego **PARTITION BY.** Zobacz więcej szczegółów w sekcji Żenująco równoległe zadania [na tej stronie](stream-analytics-parallelization.md).
2.  W zależności od typów danych wyjściowych używanych w kwerendzie niektóre dane wyjściowe mogą nie być równoległe lub wymagać dalszej konfiguracji, aby być żenująco równoległe. Na przykład wyjście PowerBI nie jest równoległe. Wyjścia są zawsze scalane przed wysłaniem do ujścia wyjściowego. Obiekty BLOB, Tabele, usługi ADLS, Service Bus i Azure Function są automatycznie równoległe. Wyjścia SQL i SQL DW mają opcję równoległości. Centrum zdarzeń musi mieć konfigurację PartitionKey ustawioną tak, aby była zgodna z polem **PARTITION BY** (zwykle Identyfikator partycji). W przypadku usługi Event Hub należy również zwrócić szczególną uwagę na dopasowanie liczby partycji dla wszystkich danych wejściowych i wszystkich wyjść, aby uniknąć przenikania między partycjami. 
3.  Uruchom kwerendę z **6 SU** (co jest pełną pojemność pojedynczego węzła obliczeniowego) do pomiaru maksymalnej osiągalne przepływności, a jeśli używasz **GROUP BY**, zmierzyć, ile grup (kardynalność) zadanie może obsłużyć. Ogólne objawy zadania trafienia limity zasobów systemowych są następujące.
    - Wskaźnik wykorzystania SU % wynosi ponad 80%. Oznacza to, że użycie pamięci jest wysokie. Czynniki przyczyniające się do wzrostu tej metryki są opisane [tutaj](stream-analytics-streaming-unit-consumption.md). 
    -   Sygnatura czasowa wyjścia pozostaje w tyle pod względem czasu zegara ściennego. W zależności od logiki zapytania sygnatura czasowa danych wyjściowych może mieć przesunięcie logiczne od czasu zegara ściennego. Powinny one jednak rozwijać się w mniej więcej takim samym tempie. Jeśli sygnatura czasowa wyjścia spada coraz dalej w tyle, jest to wskaźnik, że system jest przepracowy. Może to być wynikiem ograniczania ujścia wyjściowego poniżej lub wysokiego wykorzystania procesora CPU. W tej chwili nie udostępniamy metryki wykorzystania procesora CPU, więc może być trudno odróżnić te dwa.
        - Jeśli problem jest spowodowane ograniczania sink, może być konieczne zwiększenie liczby partycji wyjściowych (a także partycje wejściowe, aby zachować zadanie w pełni równoległościable) lub zwiększyć ilość zasobów ujścia (na przykład liczba jednostek żądania dla usługi CosmosDB).
    - Na diagramie zadań istnieje metryka zdarzenia zaległości na partycje dla każdego wejścia. Jeśli metryka zdarzenia zaległości stale rośnie, jest to również wskaźnik, że zasób systemowy jest ograniczony (z powodu ograniczania ujścia danych wyjściowych lub wysokiego procesora CPU).
4.  Po określeniu limitów tego, co zadanie 6 SU może osiągnąć, można ekstrapolować liniowo możliwości przetwarzania zadania, jak dodać więcej SUs, zakładając, że nie masz żadnych pochylenia danych, które sprawia, że niektóre partycji "gorąco".

> [!NOTE]
> Wybierz odpowiednią liczbę jednostek przesyłania strumieniowego: Ponieważ usługa Stream Analytics tworzy węzeł przetwarzania dla każdego dodania 6 jednostek SU, najlepiej jest, aby liczba węzłów była dzielnikiem liczby partycji wejściowych, dzięki czemu partycje mogą być równomiernie rozłożone między węzłami.
> Na przykład zostały zmierzone zadanie 6 SU można osiągnąć 4 MB/s szybkość przetwarzania, a liczba partycji wejściowych jest 4. Możesz uruchomić zadanie z 12 SU, aby osiągnąć około 8 MB / s szybkość przetwarzania lub 24 SU, aby osiągnąć 16 MB / s. Następnie można zdecydować, kiedy zwiększyć liczbę SU dla zadania do jakiej wartości, jako funkcja szybkości wprowadzania.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Przypadek 2 - Jeśli zapytanie nie jest żenująco równoległe.
Jeśli zapytanie nie jest żenująco równoległe, można wykonać następujące kroki.
1.  Zacznij od kwerendy bez **partycji przez** pierwszy, aby uniknąć złożoności partycjonowania, i uruchom kwerendę z 6 SU, aby zmierzyć maksymalne obciążenie, jak w [przypadku 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Jeśli można osiągnąć przewidywane obciążenie w okresie przepływności, gotowe. Alternatywnie można zmierzyć to samo zadanie uruchomione w 3 SU i 1 SU, aby dowiedzieć się minimalnej liczby SU, który działa dla twojego scenariusza.
3.  Jeśli nie można osiągnąć żądaną przepływność, spróbuj podzielić zapytanie na wiele kroków, jeśli to możliwe, jeśli nie ma już wiele kroków i przydzielić do 6 SU dla każdego kroku w kwerendzie. Na przykład, jeśli masz 3 kroki, przydziel 18 SU w opcji "Skaluj".
4.  Podczas uruchamiania takiego zadania usługa Stream Analytics umieszcza każdy krok we własnym węźle z dedykowanymi zasobami 6 SU. 
5.  Jeśli nadal nie osiągnięto docelowego obciążenia, można spróbować użyć **partycji przez** uruchamianie kroków bliżej danych wejściowych. W przypadku operatora **GROUP BY,** który może nie być naturalnie podzielony na partycje, można użyć wzorca agregacji lokalnego/globalnego do wykonania podzielonej na partycje **grupy BY,** po której następuje nieobjęta partycjonowaną **grupą WEDŁUG**. Na przykład, jeśli chcesz policzyć, ile samochodów przechodzi przez każdy punkt poboru opłat co 3 minuty, a ilość danych jest poza tym, co może być obsługiwane przez 6 SU.

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
W powyższym pytaniu liczenie samochodów na budkę poboru opłat za partycję, a następnie dodawanie liczby ze wszystkich partycji razem.

Po partycji, dla każdej partycji kroku, przydzielić do 6 SU, każda partycja o 6 SU jest maksymalna, więc każda partycja może być umieszczona na własnym węźle przetwarzania.

> [!Note]
> Jeśli kwerendy nie można podzielić na partycje, dodanie dodatkowych jednostek SU w kwerendzie wieloecetowej nie zawsze może poprawić przepływność. Jednym ze sposobów uzyskania wydajności jest zmniejszenie woluminu w początkowych krokach przy użyciu lokalnego/globalnego wzorca agregacji, jak opisano powyżej w kroku 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Przypadek 3 — w zadaniu jest wiele niezależnych zapytań.
W przypadku niektórych przypadków użycia niezależnego przedsiębiorstwa, w których bardziej opłacalne jest przetwarzanie danych z wielu dzierżaw w jednym zadaniu przy użyciu oddzielnych danych wejściowych i wyjściowych dla każdej dzierżawy, może skończyć się uruchomieniem sporo (na przykład 20) niezależnych zapytań w jednym zadaniu. Założenie jest takie podkwerendy obciążenia jest stosunkowo mały. W takim przypadku można wykonać następujące kroki.
1.  W takim przypadku nie należy używać **partition by** w kwerendzie
2.  Zmniejsz liczbę partycji wejściowych do najniższej możliwej wartości 2, jeśli używasz Centrum zdarzeń.
3.  Uruchom kwerendę z 6 SU. Z oczekiwanym obciążeniem dla każdej podzapytania dodaj jak najwięcej takich podkwerend, dopóki zadanie nie zostanie trafieniem limitów zasobów systemowych. W przypadku wystąpienia objawów należy zapoznać się z przypadkiem [1.](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions)
4.  Po osiągnięciu limitu podzapytań zmierzonych powyżej, zacznij dodawać podkwerendę do nowego zadania. Liczba zadań do uruchomienia jako funkcja liczby niezależnych zapytań powinna być dość liniowa, przy założeniu, że nie masz żadnych pochyleń obciążenia. Następnie można prognozować, ile 6 zadań SU trzeba uruchomić jako funkcję liczby dzierżaw, które chcesz obsługiwać.
5.  Podczas korzystania z danych referencyjnych sprzężenia z takich zapytań, należy połączyć dane wejściowe razem przed łączeniem z tych samych danych referencyjnych. Następnie w razie potrzeby podziel zdarzenia. W przeciwnym razie każde sprzężenie danych referencyjnych przechowuje kopię danych referencyjnych w pamięci, prawdopodobnie wysadzenie użycia pamięci niepotrzebnie.

> [!Note] 
> Ilu najemców umieścić w każdym zadaniu?
> Ten wzorzec kwerendy często ma dużą liczbę podksemie i powoduje bardzo dużą i złożoną topologię. Kontroler zadania może nie być w stanie obsłużyć tak dużej topologii. Zgodnie z ogólną zasadą, pozostań pod 40 najemców dla 1 pracy SU i 60 dzierżawców dla 3 SU i 6 miejsc pracy SU. Po przekroczeniu pojemności kontrolera zadanie nie zostanie pomyślnie uruchomiony.



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

