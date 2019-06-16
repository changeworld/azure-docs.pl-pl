---
title: Dziennik i wstępnie zagregowane metryki w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Dlaczego używać dziennika na podstawie w porównaniu z wstępnie zagregowane metryki w usłudze Azure Application Insights
services: application-insights
keywords: ''
author: vgorbenko
ms.author: vitalyg
ms.reviewer: mbullwin
ms.date: 09/18/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9520cbb9973071bf1c52266d7718837607c1d10f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66256127"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Dziennik i wstępnie zagregowane metryki w usłudze Application Insights

W tym artykule opisano różnicę między "tradycyjny" metryk usługi Application Insights, które są oparte na dziennikach i wstępnie zagregowane metryki, które są obecnie dostępne w publicznej wersji zapoznawczej. Oba rodzaje metryki są dostępne dla użytkowników usługi Application Insights, a każda oferuje unikatową wartość w monitorowaniu kondycji aplikacji, Diagnostyka i analiza. Deweloperzy, którzy są Instrumentacja aplikacji można zdecydować, jakiego typu metryka jest najlepsze dostosowane do danego scenariusza, w zależności od rozmiaru aplikacji, oczekiwany wolumin danych telemetrycznych i wymagań biznesowych dokładności metryk i alertów.

## <a name="log-based-metrics"></a>Metryki na podstawie dzienników

Niedawna modelu danych telemetrii w usłudze Application Insights do monitorowania aplikacji wyłącznie oparto na niewielką liczbę wstępnie zdefiniowanych typów zdarzeń, takich jak żądania, wyjątki, wywołania zależności, wyświetleń stron, itp. Deweloperzy mogą używać zestawu SDK można albo wyemitować tych zdarzeń, ręcznie (przez napisanie kodu, który jawnie wywołuje zestaw SDK) lub może opierają się na automatyczne zbieranie zdarzeń z automatycznej instrumentacji. W obu przypadkach wewnętrznej bazy danych usługi Application Insights jako dzienniki są przechowywane wszystkie zebrane zdarzenia, a bloki usługi Application Insights w witrynie Azure portal działają jako analitycznych i diagnostycznych narzędzie do wizualizacji danych zdarzenia z dzienników.

Przy użyciu dzienników, aby zachować kompletny zestaw zdarzeń może przynieść cenną analitycznych i diagnostyki. Na przykład można uzyskać dokładną liczbę żądań do określonego adresu URL z liczbą unikatowych użytkowników, którzy te wywołania. Lub możesz uzyskać szczegółowe dane śledzenia diagnostycznego, łącznie z wyjątkami i wywołania zależności dotyczące każdą sesję użytkownika. Posiadanie informacje tego typu może znacznie zwiększyć wgląd w kondycję aplikacji i użycia, dzięki czemu można wyciąć czas niezbędne do diagnozowania problemów z aplikacją. 

W tym samym czasie zbieranie kompletny zestaw zdarzeń, może być niepraktyczne (lub nawet niemożliwe) dla aplikacji, które generują wiele danych telemetrycznych. W sytuacjach, gdy wolumin zdarzeń jest zbyt wysoka usługi Application Insights implementuje kilka technik redukcji woluminu danych telemetrycznych, takich jak [próbkowania](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) i [filtrowanie](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) , Zmniejsz liczbę zdarzenia zebrane i przechowywane. Niestety zmniejszenie liczby przechowywanych zdarzeń zmniejsza również dokładność metryk, które należy wykonać w tle agregacji czas kwerendy zdarzeń przechowywanych w dziennikach.

> [!NOTE]
> W usłudze Application Insights metryki, które są oparte na agregację czas kwerendy zdarzenia i miar przechowywanych w dziennikach są nazywane metryk opartych na dzienniku. Te metryki zazwyczaj ma wiele wymiarów przy użyciu właściwości zdarzenia, co sprawia, że ich przełożonego analizy, ale negatywny wpływ na dokładność tych metryk przez próbkowanie i filtrowanie.

## <a name="pre-aggregated-metrics"></a>Wstępnie zagregowane metryki

Oprócz metryk opartych na dzienniku w Fall 2018 r. zespół usługi Application Insights wysłane publicznej wersji zapoznawczej metryk, które są przechowywane w repozytorium specjalistyczne, zoptymalizowana pod kątem szeregów czasowych. Nowe metryki nie są przechowywane jako pojedyncze zdarzenia z dużą liczbą właściwości. Zamiast tego są one przechowywane jako szeregów czasowych wstępnie zagregowane i tylko z kluczami wymiarów. To sprawia, że nowe metryki przełożonego w czasie wykonywania zapytania: pobieranie danych się nie dzieje znacznie szybciej i wymaga mniej mocy obliczeniowej. W związku z tym umożliwia to obsługę nowych scenariuszy takich jak [niemal alerty w czasie rzeczywistym w wymiarach metryki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)reakcji [pulpity nawigacyjne](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)i nie tylko.

> [!IMPORTANT]
> Współistnieć, opartych na dzienniku i wstępnie zagregowane metryki w usłudze Application Insights. Do odróżniania dwóch, w usłudze Application Insights UX wstępnie zagregowane metryki są teraz nazywane "Standardowych metryk (wersja zapoznawcza)", podczas gdy tradycyjnych metryki z zdarzenia zostały zmienione na "opartych na dzienniku metryki".

Nowsze zestawy SDK ([Application Insights w wersji 2.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) zestawu SDK lub nowszej dla platformy .NET) zaczną działać metryki wstępnie agregacji podczas zbierania przed sposobów zmniejszania woluminu danych telemetrycznych. Oznacza to, że dokładność nowe metryki nie ma wpływu na próbkowanie i filtrowanie, korzystając z najnowszych zestawów SDK Application Insights.

Dla zestawów SDK, które nie implementują wstępnego agregowania (oznacza to, że starsze wersje zestawów SDK Application Insights lub przeglądarki Instrumentacji) wewnętrznej bazy danych usługi Application Insights nadal powoduje wypełnienie nowe metryki przez agregowanie zdarzeń odebranych przez aplikację Punkt końcowy zbierania zdarzeń szczegółowe informacje. Oznacza to, że podczas, gdy nie można korzystać z ograniczoną ilość danych przesyłanych przez sieć, można nadal używać wstępnie zagregowane metryki i środowiska lepszą wydajność i obsługę prawie w czasie rzeczywistym wymiarowego zgłaszania alertów za pomocą zestawów SDK, które nie wstępnej agregacji metryk podczas zbierania.

Warto zauważyć, że punkt końcowy zbierania wstępnie zagregowane zdarzenia przed próbkowanie fragmentaryczne, co oznacza, że [próbkowanie fragmentaryczne](https://docs.microsoft.com/azure/application-insights/app-insights-sampling) nigdy nie będzie wpływ na dokładność wstępnie zagregowane metryki, niezależnie od wersji zestawu SDK możesz za pomocą aplikacji.  

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Za pomocą usługi Application Insights, metryki niestandardowe przy użyciu wstępnego agregowania

Za pomocą wstępnego agregowania metryki niestandardowe. Dwie główne korzyści jest możliwość konfigurowania i alerty dotyczące wymiaru metryk niestandardowych i zmniejszenia ilości danych wysyłanych z zestawu SDK do punktu końcowego zbierania usługi Application Insights.

Istnieje kilka [sposoby wysyłania metryki niestandardowe z zestawu SDK usługi Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). Jeśli Twoja wersja zestawu SDK oferuje [GetMetric i TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) metod, jest to preferowany sposób wysyłania metryki niestandardowe, ponieważ w tym przypadku wstępnego agregowania się dzieje w zestawie SDK, nie tylko zmniejszenie ilości danych przechowywanych w Azure, ale także ilość danych przesyłanych z zestawu SDK do usługi Application Insights. W przeciwnym razie użyj [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) metody, która będzie wstępnej agregacji metryk zdarzenia podczas pozyskiwania danych.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Wymiary niestandardowe metryki i wstępnego agregowania

Wszystkie metryki, wysyłane przy użyciu [trackMetric](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackmetric) lub [GetMetric i TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric) wywołań interfejsu API są automatycznie przechowywane w magazynach dzienników i metryk. Gdy wersja opartych na dzienniku swoje niestandardowe metryki zawsze zachowuje wszystkie wymiary, wersja wstępnie zagregowane metryki są przechowywane domyślnie nie wymiarów. Można włączyć na zbiór wymiarów metryki niestandardowe [użycia i szacowanych kosztów](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) kartę, zaznaczając opcję "Włącz alerty na wymiary niestandardowe metryki": 

![Użycie i szacowane koszty](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Dlaczego to zbiór wymiarów metryki niestandardowe domyślnie wyłączona?

Zbiór wymiarów metryki niestandardowe jest domyślnie wyłączone, ponieważ w przyszłości przechowywania metryki niestandardowe przy użyciu wymiarów, zostanie rozliczone osobno z usługi Application Insights podczas przechowywania-wymiarowej metryki niestandardowe pozostaną bezpłatne (maksymalnie limit przydziału) . Możesz dowiedzieć się o nadchodzących zmianach modelu cen na naszej oficjalnej [stronę z cennikiem](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Tworzenie wykresów i przeglądanie dzienników i standard wstępnie zagregowane metryki

Użyj [Eksploratora metryk usługi Azure Monitor](../platform/metrics-getting-started.md) wykreślanie wykresów z wstępnie zagregowane i opartych na dzienniku metryki i autor pulpity nawigacyjne z wykresami. Po wybraniu żądanego zasobu usługi Application Insights, użyj selektora obszaru nazw w celu przełączania się między standard (wersja zapoznawcza) i metryk opartych na dzienniku lub wybrać metryki niestandardowej przestrzeni nazw:

![Metryki przestrzeni nazw](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="next-steps"></a>Kolejne kroki

* [W pobliżu alerty w czasie rzeczywistym](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts)
* [GetMetric i TrackValue](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#getmetric)