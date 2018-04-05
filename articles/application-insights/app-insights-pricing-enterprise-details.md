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
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6e7591ccf0f21099474a08dda088422c377135f6
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="enterprise-plan-details"></a>Szczegóły plan Enterprise

Usługa Application Insights ma dwa planów cenowych. Domyślny plan jest nazywany [podstawowe](app-insights-pricing.md), która obejmuje wszystkie te same funkcje co plan Enterprise na nie dodawania kosztów i rachunków przede wszystkim od ilości danych pozyskanych. Jeśli używasz usługi Operations Management Suite, należy wybrać dla przedsiębiorstw planu, który ma każdego węzła obciążania wraz z codziennie dodatki danych, a następnie obciąży danych pozyskanych powyżej dołączone dodatku.

Zobacz [cennikiem usługi Application Insights](http://azure.microsoft.com/pricing/details/application-insights/) dla bieżącego cen w walucie i regionu.

## <a name="heres-how-the-enterprise-plan-works"></a>Oto, jak działa Enterprise plan

* Płaci się na węzeł, który wysyła dane telemetryczne dla wszystkich aplikacji w planie przedsiębiorstwa.
 * A *węzła* jest maszyną serwerze fizycznym lub wirtualnym lub platforma jako usługa wystąpienia roli, który jest hostem aplikacji.
 * Programowanie maszyny, przeglądarki klienta i urządzeń przenośnych nie są liczone jako węzły.
 * Jeśli aplikacja ma kilka składników, które wysyłają dane telemetryczne, takie jak usługi sieci web i zaplecza pracownika, zostały uwzględnione oddzielnie.
 * [Metryki strumień na żywo](app-insights-live-stream.md) danych nie są one uwzględniane dla cennik purposes.* w ramach subskrypcji, są widoczne obciążenia na węzeł nie każdej aplikacji. Jeśli masz pięć węzłów wysyłania danych telemetrycznych do 12 aplikacji, a następnie opłata jest pięć węzłów.
* Chociaż opłaty są podane na miesiąc, są pobierane tylko w przypadku dowolnego godzinę, w którym węzeł wysyła dane telemetryczne z aplikacji. Co godzinę jest ujętego w cudzysłów opłaty miesięczne / 744 (liczba godzin w miesiącu dzień 31).
* Dla każdego węzła wykrył (z godzinowe) znajduje się alokację wolumin danych 200 MB na dzień. Alokacja nieużywanych danych nie jest przenoszona z jednego dnia do następnego.
 * Jeśli zostanie wybrana opcja cenową przedsiębiorstwa, każda subskrypcja otrzymuje dziennych dodatków danych na podstawie liczby węzłów wysyłania danych telemetrycznych do zasobów usługi Application Insights w tej subskrypcji. Dlatego jeśli masz 5 węzłów wysyłanie danych cały dzień, konieczne będzie puli dodatek 1 GB stosowane do wszystkich zasobów usługi Application Insights w tej subskrypcji. Nie ma znaczenia, jeśli niektóre węzły są wysyłania więcej danych niż inne węzły, ponieważ dołączony danych jest współużytkowana przez wszystkie węzły. Jeśli w danym dniu, zasobów usługi Application Insights otrzymywać więcej danych niż jest uwzględniona w codziennych alokacji danych dla tej subskrypcji,-GB danych nadwyżkowe opłaty. 
 * Codzienne dodatku danych jest obliczany jako liczbę godzin (przy użyciu czasu UTC) dnia czy każdy węzeł wysyła dane telemetryczne podzielona przez 24 godziny 200 MB. Dlatego jeśli 4 węzły wysyłanie danych telemetrycznych w trakcie 15 24 godziny, dnia, będą uwzględniane dane za ten dzień ((4 x 15) / 24) x 200 MB = 500 MB. W cenie 2.30 USD na GB dla danych nadwyżkowe opłata będzie 1,15 USD węzły wysłania 1 GB danych w tym dniu.
 * Przedsiębiorstwa, który planu codzienne dodatku nie są współużytkowane z aplikacji, dla których wybrano opcję podstawowe i nieużywanych dozwolony nie jest przenoszona z typowymi. 

## <a name="here-are-some-examples-of-determining-distinct-node-count"></a>Oto kilka przykładów ustalania liczby unikatowych węzła

| Scenariusz                               | Łączna liczba węzłów dziennie |
|:---------------------------------------|:----------------:|
| 1 aplikacja używa 3 wystąpienia usługi Azure App Service i 1 serwera wirtualnego | 4 |
| 3 aplikacji uruchomionych na maszynach wirtualnych 2 i zasobów usługi Application Insights dla tych aplikacji są w tej samej subskrypcji i Enterprise plan | 2 | 
| 4 aplikacje, których zasoby aplikacji szczegółowe informacje znajdują się w tej samej subskrypcji. Każda aplikacja działa 2 wystąpienia 16 poza godzinami szczytu i wystąpień 4, 8 godzinach szczytu. | 13.33 | 
| Usługi w chmurze z rolą proces roboczy 1 i 1 roli sieci Web, każdy uruchomiony 2 wystąpienia | 4 | 
| Klastra sieci szkieletowej usług węzła 5 usługą 50 micro-services każdego micro-uruchomionych wystąpień 3 | 5|

* Dokładne węzeł zliczania zachowanie zależy od tego, na którym jest przy użyciu zestawu SDK usługi Application Insights aplikacji. 
  * Zestaw SDK w wersji 2.2 i później, zarówno usługi Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) lub [zestawu SDK sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) będzie raportu każdy host aplikacji jako węzeł, na przykład nazwę komputera dla serwera fizycznego i hosty maszyn wirtualnych lub nazwa wystąpienia, w przypadku usługi w chmurze.  Jedynym wyjątkiem jest aplikacji tylko przy użyciu [.NET Core](https://dotnet.github.io/) i SDK Core Insights aplikacji, w którym wielkość tylko jeden węzeł będą zgłaszane dla wszystkich hostów, ponieważ nazwa hosta nie jest dostępna. 
  * W przypadku wcześniejszych wersji zestawu SDK [zestawu SDK sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) będzie działać podobnie jak nowsze wersje zestawu SDK, ale [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) będzie zgłaszać tylko jeden węzeł, niezależnie od liczby hostów rzeczywistej aplikacji. 
  * Jeśli aplikacja używa zestawu SDK ustawioną wartość niestandardową roleInstance, domyślnie tej samej wartości będzie służyć do określenia liczby węzłów. 
  * Jeśli używasz nowej wersji zestawu SDK w aplikacji, która jest uruchamiany z urządzeń przenośnych i komputerów klienckich, jest to możliwe, że liczba węzłów może zwracać numer, który jest bardzo duże (od dużej liczby urządzeń przenośnych i komputerów klienckich). 
