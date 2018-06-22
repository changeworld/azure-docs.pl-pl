---
title: Starszej wersji Enterprise planu szczegóły cennika dla usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Zarządzanie woluminami dane telemetryczne i monitorować kosztów w usłudze Application Insights.
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
ms.openlocfilehash: 65307eab0bf1b5f502f11c14c369826cd12e0966
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309834"
---
# <a name="enterprise-plan-details"></a>Szczegóły planu Enterprise

Azure Application Insights ma dwa planów cenowych: Basic i Enterprise. [Podstawowe](app-insights-pricing.md) cennik plan jest domyślny plan. Zawiera wszystkie funkcje plan Enterprise, bez ponoszenia dodatkowych kosztów. Podstawowy plan BOM przede wszystkim od ilości danych, która jest pozyskanych. 

Enterprise plan ma opłat każdego węzła i każdego węzła otrzyma dziennych dodatków danych. W przedsiębiorstwie cenową planu, naliczane są opłaty danych pozyskanych powyżej dołączone dodatku. Jeśli używasz usługi Operations Management Suite należy wybrać Enterprise plan. 

Dla bieżącego cen w walucie i regionu, zobacz [usługi Application Insights wyceny](http://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> W kwietniu 2018 firma Microsoft [wprowadzone](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) nowego modelu cenowego monitorowania Azure. Ten model przyjmuje prostego modelu "z" między pełną gamę monitorowanie usług. Dowiedz się więcej o [nowy model cenowy](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), jak do [ocenić wpływ przenoszenia do tego modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) oparte na Twoich wzorców użycia i [jak zgłosić do nowego modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

## <a name="how-the-enterprise-plan-works"></a>Jak działa Enterprise plan

* Płacisz za każdym węźle, który wysyła dane telemetryczne dla wszystkich aplikacji w planie przedsiębiorstwa.
 * A *węzła* jest maszyną serwerze fizycznym lub wirtualnym lub wystąpienia platforma jako usługa roli, który jest hostem aplikacji.
 * Programowanie maszyny, przeglądarki klienta i urządzeń przenośnych nie są traktowane jako węzły.
 * Gdy aplikacja ma kilka składników, które wysyłają dane telemetryczne, takie jak usługi sieci web i zaplecza pracownika, składniki są zliczane osobno.
 * [Metryki strumień na żywo](app-insights-live-stream.md) danych nie są one uwzględniane dla celów cennik. W ramach subskrypcji z opłaty są na węzeł nie każdej aplikacji. Jeśli masz pięć węzłów, które wysyłają dane telemetryczne dotyczące 12 aplikacji jest pięć węzłów.
* Chociaż opłaty są podane na miesiąc, są pobierane tylko w przypadku dowolnego godzinę, w którym węzeł wysyła dane telemetryczne z aplikacji. Co godzinę jest ujętego w cudzysłów opłaty miesięczne rozdzielonych 744 (liczba godzin w miesiącu dzień 31).
* Alokację wolumin danych 200 MB na dzień znajduje się na każdym węźle, który zostanie wykryty (z godzinowe). Alokacja nieużywanych danych nie jest przenoszone z jednego dnia do następnego.
 * Jeśli jednostka ceny planu, każda subskrypcja otrzymuje dziennych dodatków danych na podstawie liczby węzłów, które wysyłania danych telemetrycznych do zasobów usługi Application Insights w tej subskrypcji. Tak Jeśli masz pięć węzłów, które wysyłają dane cały dzień, będziesz mieć puli dodatek 1 GB stosowane do wszystkich zasobów usługi Application Insights w tej subskrypcji. Nie ma znaczenia, jeśli niektóre węzły wysłać więcej danych niż inne węzły, ponieważ dołączony danych jest współużytkowana przez wszystkie węzły. Jeśli w danym dniu, zasobów usługi Application Insights otrzymywać więcej danych niż jest uwzględniona w codziennych alokacji danych dla tej subskrypcji,-GB danych nadwyżkowe opłaty. 
 * Codzienne dodatku danych jest obliczany jako liczbę godzin (przy użyciu czasu UTC) dnia każdego węzła i wysyła dane telemetryczne podzielona przez 24 pomnożona przez 200 MB. Tak, jeśli masz czterech węzłów, które wysłać dane telemetryczne w okresie 15 24 godziny, dnia, uwzględnione dane za ten dzień byłyby ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. W cenie 2.30 USD na GB dla danych nadwyżkowe opłata będzie 1,15 USD węzły wysłania 1 GB danych w tym dniu.
 * Dodatek codzienne plan przedsiębiorstwa nie jest udostępniony z aplikacjami, dla których wybrano podstawowy plan. Nieużywane dodatku nie jest przenoszone z typowymi. 

## <a name="examples-of-how-to-determine-distinct-node-count"></a>Przykłady sposobu ustalania liczby unikatowych węzła

| Scenariusz                               | Łączna liczba węzłów dziennie |
|:---------------------------------------|:----------------:|
| 1 aplikacja za pomocą 3 wystąpienia usługi Azure App Service i 1 serwera wirtualnego | 4 |
| 3 aplikacji uruchomionych na maszynach wirtualnych 2; zasoby usługi Application Insights dla te aplikacje są w tej samej subskrypcji i Enterprise plan | 2 | 
| 4 aplikacji, którego zasoby aplikacji szczegółowe informacje znajdują się w tej samej subskrypcji; Każda aplikacja systemem 2 wystąpienia 16 poza godzinami szczytu i wystąpień 4, 8 godzinach szczytu | 13.33 | 
| Usługi w chmurze z rolą proces roboczy 1 i 1 roli sieci Web, każdy uruchomiony 2 wystąpienia | 4 | 
| 5 węzłów klastra sieci szkieletowej usług Azure z systemem mikrousług 50; Każdy mikrousługi uruchomione wystąpienia 3 | 5|

* Zliczanie dokładne węzła zależy od tego, w których zestaw SDK usługi Application Insights jest przy użyciu aplikacji. 
  * W wersjach zestawu SDK 2,2 i nowsze, zarówno usługi Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) i [zestawu SDK sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) raport każdy host aplikacji jako węzeł. Przykłady są nazwy komputera dla serwera fizycznego i hosty maszyn wirtualnych lub nazwę wystąpienia dla usługi w chmurze.  Jedynym wyjątkiem jest aplikacja, która używa tylko [.NET Core](https://dotnet.github.io/) i zestaw SDK usługi Application Insights Core. W takim przypadku tylko jeden węzeł został zgłoszony dla wszystkich hostów, ponieważ nazwa hosta nie jest dostępna. 
  * W przypadku wcześniejszych wersji zestawu SDK [zestawu SDK sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) zachowuje się jak nowsze wersje zestawu SDK, ale [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) raporty tylko jeden węzeł, niezależnie od liczby hostów aplikacji. 
  * Jeśli aplikacja korzysta z zestawu SDK, aby ustawić **roleInstance** wartości niestandardowych, domyślnie tej samej wartości służy do określania liczba węzłów. 
  * Jeśli używasz nowej wersji zestawu SDK w aplikacji, która jest uruchamiana z urządzeń przenośnych i komputerów klienckich, liczba węzłów może zwrócić jest bardzo duże (z powodu dużej liczby urządzeń przenośnych i komputerów klienckich). 
