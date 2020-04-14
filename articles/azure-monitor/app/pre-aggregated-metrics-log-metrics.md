---
title: Metryki oparte na logach i wstępnie zagregowane w usłudze Azure Application Insights | Dokumenty firmy Microsoft
description: Dlaczego warto używać metryk opartych na logach i wstępnie zagregowanych w usłudze Azure Application Insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 30487eebed361e5b010df023a9b1a44f96590b14
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271084"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Metryki oparte na dzienniku i metryki wstępnie zagregowane w usłudze Application Insights

W tym artykule wyjaśniono różnicę między "tradycyjnych" metryki usługi Application Insights, które są oparte na dziennikach i wstępnie zagregowane metryki, które są obecnie w publicznej wersji zapoznawczej. Oba typy metryk są dostępne dla użytkowników usługi Application Insights, a każdy z nich przynosi unikatową wartość w monitorowaniu kondycji aplikacji, diagnostyki i analizy. Deweloperzy, którzy są instrumentowanie aplikacji można zdecydować, który typ metryki najlepiej nadaje się do określonego scenariusza, w zależności od rozmiaru aplikacji, oczekiwanej ilości danych telemetrycznych i wymagania biznesowe dotyczące dokładności metryki i alertów.

## <a name="log-based-metrics"></a>Metryki oparte na dziennikach

Do niedawna model danych telemetrycznych monitorowania aplikacji w usłudze Application Insights był oparty wyłącznie na niewielkiej liczbie wstępnie zdefiniowanych typów zdarzeń, takich jak żądania, wyjątki, wywołania zależności, widoki strony itp. Deweloperzy mogą używać SDK albo emitować te zdarzenia ręcznie (pisząc kod, który jawnie wywołuje zestaw SDK) lub mogą polegać na automatycznej kolekcji zdarzeń z auto-instrumentacji. W obu przypadkach zaplecza usługi Application Insights przechowuje wszystkie zebrane zdarzenia jako dzienniki, a raporty z usługi Application Insights w portalu Azure działają jako narzędzie analityczne i diagnostyczne do wizualizacji danych opartych na zdarzeniach z dzienników.

Korzystanie z dzienników w celu zachowania pełnego zestawu zdarzeń może przynieść dużą wartość analityczną i diagnostyczną. Na przykład można uzyskać dokładną liczbę żądań do określonego adresu URL z liczbą różnych użytkowników, którzy nawiązyli te połączenia. Lub można uzyskać szczegółowe ślady diagnostyczne, w tym wyjątki i wywołania zależności dla dowolnej sesji użytkownika. Posiadanie tego typu informacji może znacznie poprawić wgląd w kondycję aplikacji i użycie, co pozwala skrócić czas niezbędny do zdiagnozowania problemów z aplikacją.

W tym samym czasie zbieranie pełnego zestawu zdarzeń może być niepraktyczne (lub nawet niemożliwe) dla aplikacji, które generują dużą ilość danych telemetrycznych. W sytuacjach, gdy ilość zdarzeń jest zbyt wysoka, usługa Application Insights implementuje kilka technik redukcji woluminów telemetrycznych, takich jak [próbkowanie](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) i [filtrowanie,](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) które zmniejszają liczbę zebranych i przechowywanych zdarzeń. Niestety, obniżenie liczby przechowywanych zdarzeń zmniejsza również dokładność metryk, które za kulisami muszą wykonywać agregacje zdarzeń przechowywanych w dziennikach w czasie kwerendy.

> [!NOTE]
> W usłudze Application Insights metryki, które są oparte na agregacji zdarzeń i pomiarów w czasie kwerendy przechowywane w dziennikach są nazywane metrykami opartymi na dzienniku. Te metryki zazwyczaj mają wiele wymiarów z właściwości zdarzenia, co czyni je lepsze dla analizy, ale dokładność tych metryk ma negatywny wpływ próbkowania i filtrowania.

## <a name="pre-aggregated-metrics"></a>Wstępnie zagregowane dane

Oprócz metryk opartych na dzienniku pod koniec 2018 r. zespół usługi Application Insights dostarczył publiczną wersję zapoznawczą metryk przechowywanych w wyspecjalizowanym repozytorium zoptymalizowanym pod kątem szeregów czasowych. Nowe metryki nie są już przechowywane jako poszczególne zdarzenia z dużą ilością właściwości. Zamiast tego są one przechowywane jako wstępnie zagregowane szeregi czasowe i tylko z kluczowymi wymiarami. Dzięki temu nowe metryki są lepsze w czasie kwerendy: pobieranie danych odbywa się znacznie szybciej i wymaga mniejszej mocy obliczeniowej. W związku z tym umożliwia to nowe scenariusze, takie jak [niemal w czasie rzeczywistym alerty dotyczące wymiarów metryk,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)bardziej [responsywne pulpity nawigacyjne](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)i inne.

> [!IMPORTANT]
> Zarówno oparte na dzienniku, jak i wstępnie zagregowane metryki współistnieją w usłudze Application Insights. Aby odróżnić te dwa, w UX usługi Application Insights wstępnie zagregowane metryki są teraz nazywane "Metryki standardowe (wersja zapoznawcza)", podczas gdy tradycyjne metryki ze zdarzeń zostały zmienione na "Metryki oparte na dzienniku".

Nowsze sdks[(Application Insights 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK lub nowsze dla platformy .NET) wstępnie agregować metryki podczas zbierania przed rozpoczęciem technik redukcji woluminu telemetrycznego. Oznacza to, że dokładność nowych metryk nie ma wpływu na próbkowanie i filtrowanie podczas korzystania z najnowszych sdk usługi Application Insights.

W przypadku zestawów SDK, które nie implementują wstępnej agregacji (czyli starszych wersji zestawów SDK usługi Application Insights lub instrumentacji przeglądarki) zaplecze usługi Application Insights nadal wypełnia nowe metryki, agregując zdarzenia odebrane przez punkt końcowy kolekcji zdarzeń usługi Application Insights. Oznacza to, że chociaż nie korzystają z zmniejszonej ilości danych przesyłanych za pośrednictwem sieci przewodowej, nadal można użyć wstępnie zagregowane metryki i doświadczenie lepszą wydajność i obsługę prawie w czasie rzeczywistym wymiarowe alerty z zestawów SDK, które nie wstępnie agregować metryki podczas zbierania.

Warto wspomnieć, że punkt końcowy kolekcji wstępnie agreguje zdarzenia przed próbkowaniem pozyskiwania, co oznacza, że [próbkowanie pozyskiwania](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) nigdy nie wpłynie na dokładność wstępnie zagregowanych metryk, niezależnie od wersji SDK używanej z aplikacją.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Korzystanie z wstępnej agregacji z metrykami niestandardowymi usługi Application Insights

Można użyć wstępnej agregacji z metrykami niestandardowymi. Dwie główne zalety to możliwość konfigurowania i alertów w wymiarze metryki niestandardowej i zmniejszanie ilości danych wysyłanych z zestawu SDK do punktu końcowego kolekcji usługi Application Insights.

Istnieje kilka [sposobów wysyłania niestandardowych metryk z SDK usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Jeśli twoja wersja zestawu SDK oferuje [metody GetMetric i TrackValue,](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) jest to preferowany sposób wysyłania niestandardowych metryk, ponieważ w tym przypadku wstępna agregacja odbywa się wewnątrz zestawie SDK, nie tylko zmniejszając ilość danych przechowywanych na platformie Azure, ale także ilość danych przesyłanych z zestawu SDK do usługi Application Insights. W przeciwnym razie należy użyć [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) metody, która będzie wstępnie agregować zdarzenia metryki podczas pozyskiwania danych.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Wymiary metryk niestandardowych i wstępna agregacja

Wszystkie metryki wysyłane przy użyciu [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) lub [GetMetric i TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) wywołania interfejsu API są automatycznie przechowywane w dziennikach i metryki sklepów. Jednak podczas gdy wersja metryka niestandardowego oparta na dzienniku zawsze zachowuje wszystkie wymiary, wstępnie zagregowana wersja metryki jest domyślnie przechowywana bez wymiarów. Możesz włączyć zbieranie wymiarów niestandardowych metryk na karcie [użycie i szacowany koszt,](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) zaznaczając "Włącz alerty dotyczące niestandardowych wymiarów metryki": 

![Użycie i szacowany koszt](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Dlaczego zbieranie wymiarów niestandardowych metryk jest domyślnie wyłączone?

Kolekcja wymiarów niestandardowych metryk jest domyślnie wyłączona, ponieważ w przyszłości przechowywanie niestandardowych metryk z wymiarami będzie rozliczane oddzielnie od usługi Application Insights, podczas gdy przechowywanie niewymiarowych metryk niestandardowych pozostanie bezpłatne (do przydziału). Możesz dowiedzieć się o nadchodzących zmianach modelu cenowego na naszej oficjalnej [stronie cenowej.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Tworzenie wykresów i eksplorowanie metryk opartych na logach i standardowych wstępnie zagregowanych

Użyj [Eksploratora metryk usługi Azure Monitor,](../platform/metrics-getting-started.md) aby wykreślić wykresy z wstępnie zagregowanych i opartych na logowaniu metryk oraz do tworzenia pulpitów nawigacyjnych z wykresami. Po wybraniu żądanego zasobu usługi Application Insights użyj selektora obszaru nazw, aby przełączać się między metrykami standardowymi (wersja zapoznawcza) i opartymi na dzienniku, lub wybierz niestandardowy obszar nazw metryki:

![Metryczna przestrzeń nazw](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Modele cenowe dla metryk usługi Application Insights

Pozyskiwanie metryk w usłudze Application Insights, niezależnie od tego, czy jest oparte na dzienniku, czy wstępnie zagregowane, spowoduje wygenerowanie kosztów na podstawie rozmiaru pojąconych danych, zgodnie z opisem [poniżej](https://docs.microsoft.com/azure/azure-monitor/app/pricing#pricing-model). Metryki niestandardowe, w tym wszystkie jego wymiary, są zawsze przechowywane w magazynie dziennika usługi Application Insights; Ponadto wstępnie zagregowana wersja niestandardowych metryk (bez wymiarów) jest domyślnie przekazywała do magazynu metryk.

Wybranie opcji [Włącz alerty w niestandardowych wymiarach metryk](#custom-metrics-dimensions-and-pre-aggregation) do przechowywania wszystkich wymiarów wstępnie zagregowanych metryk w magazynie metryk, może generować **dodatkowe** koszty na podstawie [cen niestandardowych metryk](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="next-steps"></a>Następne kroki

* [Alerty w czasie zbliżonym do rzeczywistego](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric i TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)