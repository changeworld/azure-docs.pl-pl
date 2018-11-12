---
title: Starszej wersji Enterprise, plan szczegóły cennika usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Zarządzanie woluminami danych telemetrycznych i monitorowanie kosztów w usłudze Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: mbullwin
ms.openlocfilehash: b2a93c7d3b512a34ab5d2e4fd020415739466c2e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235296"
---
# <a name="enterprise-plan-details"></a>Szczegóły planu Enterprise

Usługa Azure Application Insights ma dwa plany cenowe: Basic i Enterprise. [Podstawowe](app-insights-pricing.md) plan taryfowy jest domyślny plan. Obejmuje wszystkie funkcje planu Enterprise, bez ponoszenia dodatkowych kosztów. Przede wszystkim od ilości danych, które są pozyskiwane w ramach planu podstawowego. 

Enterprise plan ma opłaty za węzeł i każdy węzeł otrzyma dzienny przydział danych. W przedsiębiorstwie cennikiem, opłaty są naliczane za dane pozyskane powyżej uwzględnione dopuszczalnej wartości. Jeśli używasz pakietu Operations Management Suite, należy wybrać Enterprise plan. 

Dla bieżącego cenach w regionie i waluty, zobacz [cen usługi Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> W kwietniu 2018 r. Firma Microsoft [wprowadzone](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) nowego modelu cenowego monitorowania platformy Azure. Ten model przyjmuje prosty model "zgodnie z rzeczywistym użyciem" w pełnym portfolio monitorowanie usług. Dowiedz się więcej o [nowego modelu cen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), jak do [ocenić wpływ przenoszenia do tego modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) na podstawie Twojej wzorców użycia i [sposób dołączenia do nowego modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

## <a name="how-the-enterprise-plan-works"></a>Jak działa Enterprise plan

* Płacisz za każdy węzeł, który wysyła dane telemetryczne dotyczące wszystkich aplikacji w planie Enterprise.
 * A *węzła* jest komputer fizyczny lub wirtualny serwer lub wystąpienie roli platforma jako usługa, która jest hostem aplikacji.
 * Komputerach deweloperskich, przeglądarek klientów i urządzeń przenośnych nie są traktowane jako węzły.
 * Jeśli aplikacja ma kilka składników, które wysyłają dane telemetryczne, np. usługi sieci web i procesu roboczego zaplecza, składniki są zliczane osobno.
 * [Live Stream metryki](app-insights-live-stream.md) danych nie jest liczony cennik celów. W ramach subskrypcji usługi opłaty są naliczane za węzeł, a nie aplikacji. Jeśli masz pięć węzłów, które wysyłają dane telemetryczne dla 12 aplikacji, Opłata dotyczy pięć węzłów.
* Mimo, że opłaty są podane na miesiąc, opłaty są naliczane tylko za godziny, w których dany węzeł przesyła dane telemetryczne z aplikacji. Opłata za godziny jest cudzysłowie opłata miesięczna podzielona przez 744 (liczba godzin w miesiącu mającym 31 dni).
* Dane woluminu alokacji wynoszącej 200 MB dziennie znajduje się w każdym węźle, który został wykryty (z dokładnością co godzinę). Niewykorzystane dane alokacji nie jest przenoszone od 1 dnia następnego.
 * Jeśli wybierzesz Enterprise, plan taryfowy, każda subskrypcja otrzymuje dzienny przydział danych na podstawie liczby węzłów, które wysyłają dane telemetryczne do zasobów usługi Application Insights w tej subskrypcji. Dlatego jeśli masz pięć węzłów wysyłających dane, cały dzień, będziesz mieć dzienny przydział 1 GB, stosowany do wszystkich zasobów usługi Application Insights w tej subskrypcji. Nie ma znaczenia, jeśli niektóre węzły wysyła więcej danych niż inne węzły, ponieważ uwzględnione dane są dzielone między wszystkie węzły. Jeśli w danym dniu zasoby usługi Application Insights otrzymywać więcej danych niż przewiduje dzienny przydział danych dla tej subskrypcji, na GB nadwyżki danych opłaty. 
 * Dzienny przydział danych jest obliczany jako liczba godzin w ciągu dnia (przy użyciu czasu UTC) czy każdy węzeł wysyła dane telemetryczne, podzielona przez 24 pomnożona przez 200 MB. Dlatego w przypadku czterech węzłów, które wysyłają dane telemetryczne przez 15 godzin z 24 godzin dnia uwzględnione dane na ten dzień będzie ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. W cenie za nadwyżkowe użycie danych 2.30 USD za GB opłata byłaby 1,15 USD węzły wysłać 1 GB danych tego samego dnia.
 * Dzienny limit plan Enterprise nie zostały udostępnione aplikacji, dla których wybrano planu podstawowego. Nieużywane dopuszczalnej wartości nie jest przenoszone z typowymi. 

## <a name="examples-of-how-to-determine-distinct-node-count"></a>Przykłady sposobu ustalania liczby unikatowych węzłów

| Scenariusz                               | Łączna liczba węzeł dziennie |
|:---------------------------------------|:----------------:|
| 1 aplikacja, korzystająca z 3 wystąpień usługi Azure App Service i 1 serwer wirtualny | 4 |
| 3 działających na maszynach wirtualnych 2; zasoby usługi Application Insights dla tych aplikacji są w tej samej subskrypcji i Enterprise plan | 2 | 
| 4 aplikacji, którego zasoby aplikacji szczegółowe informacje znajdują się w tej samej subskrypcji; Każda aplikacja uruchomiona 2 wystąpienia 16 poza godzinami pracy, a 4 wystąpień w godzinach szczytu 8 | 13.33 | 
| Cloud services za pomocą roli procesu roboczego 1 oraz roli sieci Web 1, każdy uruchomiony 2 wystąpienia | 4 | 
| 5 węzłami klastra usługi Azure Service Fabric z systemem mikrousług 50; Każda mikrousługa uruchomionych wystąpień 3 | 5|

* Zliczanie dokładne węzła zależy od tego, w których zestaw SDK usługi Application Insights używa aplikacja. 
  * W wersji zestawu SDK 2,2 lub nowszą wersją, zarówno w usłudze Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) i [zestawu SDK sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) zgłosić każdego hosta aplikacji jako węzeł. Przykłady to nazwa komputera dla serwera fizycznego i hostów maszyn wirtualnych lub nazwę wystąpienia dla usług w chmurze.  Jedynym wyjątkiem jest aplikacja, która używa tylko [platformy .NET Core](https://dotnet.github.io/) i podstawowego zestawu SDK usługi Application Insights. W takim przypadku tylko jeden węzeł jest zgłaszany na wszystkich hostach, ponieważ nazwa hosta nie jest dostępna. 
  * We wcześniejszych wersjach zestawu SDK [zestawu SDK sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) zachowuje się jak nowsze wersje zestawu SDK, ale [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) zgłasza tylko jeden węzeł, niezależnie od liczby hostów aplikacji. 
  * Jeśli aplikacja używa zestawu SDK, aby ustawić **roleInstance** niestandardowej wartości domyślnie tej samej wartości służy do określania liczby węzłów. 
  * Jeśli używasz nowej wersji zestawu SDK z aplikacji, która jest uruchamiana z urządzeń przenośnych i komputerów klienckich, liczba węzłów może zwrócić liczbę, która jest bardzo duża (z powodu dużej liczby urządzeń przenośnych i komputerów klienckich). 
