---
title: Monitorowanie zbieranie danych na platformie Azure | Dokumentacja firmy Microsoft
description: Omówienie monitorowania danych, które są zbierane z aplikacji i usług na platformie Azure oraz narzędzia służące do analizowania go.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2018
ms.author: bwren
ms.openlocfilehash: 35580d71aa2592fa94f42cfdbad3c192acc303c5
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363989"
---
# <a name="collect-monitoring-data-in-azure"></a>Zbieranie danych monitorowania na platformie Azure
Ten artykuł zawiera omówienie monitorowania danych, które są zbierane z aplikacji i usług na platformie Azure. Omówiono także narzędzia, które służy do analizowania danych. 

## <a name="types-of-monitoring-data"></a>Typy danych monitorowania
Wszystkie dane monitorowania pasuje do jednej z dwóch podstawowych typów, metryk lub dzienników. Każdy typ ma różne charakterystyki i jest najbardziej odpowiednia dla konkretnych scenariuszach.

### <a name="metrics"></a>Metryki
Metryki są wartości liczbowe, które opisują niektóre aspekty systemu w określonym czasie. Ulepszenia obejmują:

* Różne dane, w tym samą wartość.
* Czas, zebranych wartości.
* Typ miary, która reprezentuje wartość.
* Zasób, który jest skojarzony wartość. 

Metryki są zbierane w regularnych odstępach czasu, czy po zmianie wartości. Na przykład użytkownik może zbierać użycie procesora na maszynie wirtualnej co minutę lub liczby użytkowników zalogował się do aplikacji co 10 minut.

Metryki są uproszczone i zdolność do obsługi scenariuszy w czasie rzeczywistym w pobliżu. Są one przydatne w przypadku alertów, ponieważ często może być próbkowany metryki i alertu można szybko wywoływane z logiką stosunkowo proste. Na przykład wyzwalać alert, gdy Metryka przekracza wartość progową. Lub może wyzwalać alert, kiedy różnica między dwie metryki osiągnie określoną wartość.

Metryki poszczególnych zwykle zapewniają mały wgląd w ich własnych. Zapewniają one pojedynczej wartości bez żadnego kontekstu innych niż porównania prosty próg. Są one przydatne w połączeniu z innymi metrykami w celu zidentyfikowania wzorców i trendów lub w połączeniu z dzienników, które zapewniają kontekstowe wokół konkretnych wartości. 

Na przykład ilu użytkowników w swojej aplikacji w danym momencie mogą wskazać możliwość rozwiązania trochę o kondycji aplikacji. Ale nagły spadek użytkowników, wskazywanym przez wiele wartości tego samego metryki może wskazywać na problem. Nadmierne wyjątków zgłoszonych przez aplikację, a podana przez oddzielne metryki, może zidentyfikować problem aplikacji, która powoduje spadek. Zdarzeń tworzonych przez aplikację do identyfikowania błędów w jej składniki mogą pomóc odkryć ich główną przyczynę.

Alerty oparte na dziennikach nie odpowiadają jako jako alerty na podstawie metryk, ale mogą to być bardziej złożonej logiki. Można utworzyć alert w oparciu o wyniki dowolnego zapytania, który wykonuje złożone analizy danych z wielu źródeł.

### <a name="logs"></a>Dzienniki
Dzienniki zawierają różne rodzaje danych zorganizowanych w rekordy z różnymi zestawami właściwości dla każdego typu. Dzienniki mogą zawierać wartości liczbowe, takie jak metryki, ale zwykle zawierają dane tekstowe za pomocą szczegółowe opisy. Dodatkowo różnią się od metryki, różnią się one w ich struktury i często nie są zbierane w regularnych odstępach czasu.

Typowe typ wpisu dziennika jest zdarzeniem. Zdarzenia są zbierane sporadycznie. One tworzone przez aplikację lub usługę i zazwyczaj zawierają informacje wystarczające do zapewnienia pełnego kontekstu własnych. Na przykład zdarzenie może wskazywać, że określony zasób został utworzony lub zmodyfikowany nowego hosta uruchomionego w odpowiedzi zwiększonego ruchu lub wykryto błąd w aplikacji.

Dzienniki są szczególnie przydatne podczas łączenia danych z wielu źródeł do kompleksowej analizy i określania trendów wraz z upływem czasu. Ponieważ format danych może się różnić w aplikacji można utworzyć niestandardowe dzienniki przy użyciu struktury, które potrzebują. Metryki nawet mogą być replikowane w dziennikach, aby połączyć je z innych danych monitorowania umożliwia analizę trendów i innych danych.


## <a name="monitoring-tools-in-azure"></a>Narzędzia do monitorowania na platformie Azure
Monitorowanie danych na platformie Azure są zbierane i analizowane za pomocą następujących źródeł.

### <a name="azure-monitor"></a>Azure Monitor
Metryki z zasobów platformy Azure i aplikacje są zbierane w usłudze Azure Monitor. Dane metryk jest integrowany strony w witrynie Azure portal dla zasobów platformy Azure. Wykresy metryk, takich jak użycie procesora CPU i sieci dla maszyn wirtualnych są wyświetlane dla wybranej maszyny. 

Ponadto można analizować dane przy użyciu [Eksploratora metryk](../monitoring-and-diagnostics/monitoring-metric-charts.md), którym wykresy wartości wielu metryk wraz z upływem czasu. Można wyświetlać wykresy interakcyjnie lub przypiąć je do pulpitu nawigacyjnego, aby wyświetlić je z innymi wizualizacjami. Możesz również pobrać za pomocą metryk [platformy Azure, interfejsu API REST monitorowania](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

Aby uzyskać więcej informacji o różnych rodzajów zasobów platformy Azure zbieranie danych metryk, zobacz [źródła danych na platformie Azure monitorowania](monitoring-data-sources.md). 

![Eksplorator metryk](media/monitoring-data-collection/metrics-explorer.png)


### <a name="activity-log"></a>Dziennik aktywności 
[Dziennika aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) są przechowywane dzienniki dotyczące konfiguracji i kondycji usług platformy Azure. Eksplorator dziennika aktywności można użyć do wyświetlenia tych dzienników w witrynie Azure portal, ale są one często [kopiowane do usługi Azure Log Analytics](../log-analytics/log-analytics-activity.md) analizowanej z innymi danymi dziennika.

Eksplorator dziennika aktywności umożliwia wyświetlanie dziennika aktywności, filtrowana w celu spełniających określone kryteria. Większość zasobów również ma **dziennika aktywności** opcji menu ich w witrynie Azure portal. Wyświetla Explorer dziennika aktywności przefiltrowana pod kątem tego zasobu. Dzienniki aktywności można również pobrać za pomocą [interfejsu API REST usługi Azure Monitoring](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Eksplorator dziennika aktywności](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Usługa log Analytics udostępnia wspólną platformę danych zarządzania na platformie Azure. To usługi podstawowej, która jest używana do przechowywania i analizy dzienników na platformie Azure. Zbiera dane z różnych źródeł, takich jak agentów na maszynach wirtualnych, rozwiązań do zarządzania i zasobów platformy Azure. Możesz skopiować dane z innych źródeł, w tym metryki i dziennika aktywności, aby utworzyć pełną centralne repozytorium danych monitorowania.

Log Analytics zawiera zaawansowany język zapytań analizy danych, które są zbierane. Możesz użyć [portale do przeszukiwania dzienników](../log-analytics/log-analytics-log-search-portals.md) interaktywnego pisania i testowaniu zapytań i analizowania ich wyniki. Możesz również [tworzenia widoków](../log-analytics/log-analytics-view-designer.md) wizualizować wyniki przeszukiwania dzienników lub Wklej wyniki zapytania bezpośrednio do pulpitu nawigacyjnego platformy Azure.  

Rozwiązania do zarządzania zawierają wyszukiwań w dziennikach i widoków w usłudze Log Analytics do analizowania zbieranych danych. Innych usług, takich jak Azure Application Insights, przechowuj dane w usłudze Log Analytics oraz oferuje dodatkowe narzędzia do analizy.  

![Dzienniki](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Usługa Application Insights gromadzi dane telemetryczne dla aplikacji sieci web zainstalowany na wielu różnych platformach. Przechowuje swoje dane w usłudze Azure Monitor i Log Analytics. I zapewnia rozbudowany zestaw narzędzi do analizowania i wizualizowania danych. Te funkcje umożliwiają korzystają ze wspólnego zestawu usług, takich jak alerty, wyszukiwań w dziennikach i pulpity nawigacyjne, które można używać na potrzeby innych monitorowania.


![Application Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>Mapa usługi
Mapa usługi zawiera wizualną reprezentację maszyn wirtualnych przy użyciu zależności i procesami. Przechowuje większość z tych danych w usłudze Log Analytics, dzięki czemu można analizować je z innymi danymi zarządzania. Konsola mapy usługi również pobiera dane z usługi Log Analytics, aby przedstawić je w kontekście maszyny wirtualnej, która jest analizowana.

![Mapa usługi](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>Transfer danych monitorowania

### <a name="metrics-to-logs"></a>Metryki do dzienników
Teraz można replikować metryk w usłudze Log Analytics, aby wykonać złożone analizy z innymi typami danych przy użyciu jego zaawansowany język zapytań. Ponadto można przechowywać dane dziennika przez dłuższy czas niż metryk, które umożliwia przeprowadzanie analizy trendów wraz z upływem czasu. Gdy metryk lub inne dane dotyczące wydajności są przechowywane w usłudze Log Analytics, która danych działa jako dziennik. Metryki do obsługi niemal w czasie rzeczywistym analiz i alertów podczas korzystania z dzienników do wyznaczania trendów i analiz z innymi danymi.

Możesz uzyskać wskazówki dotyczące zbieranie metryk z zasobów platformy Azure w [zbieranie dzienników platformy Azure usługi i metryk do użycia w usłudze Log Analytics](../log-analytics/log-analytics-azure-storage.md). Uzyskaj wskazówki dotyczące zbieranie metryk zasobów z zasobów PaaS platformy Azure w [skonfigurować zbieranie metryk zasobów PaaS platformy Azure z usługą Log Analytics](../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Dzienniki, metryki
Zgodnie z wcześniejszym opisem metryki są bardziej elastyczny niż dzienniki, aby można było utworzyć alerty z mniejszymi opóźnieniami i przy niskich kosztach. Usługa log Analytics zbiera znacznej ilości danych liczbowych, który będzie odpowiedni dla metryk, ale nie są przechowywane w usłudze Azure Monitor. 

Typowym przykładem są dane dotyczące wydajności zbierane z agentów i rozwiązań do zarządzania. Niektóre z tych wartości mogły zostać skopiowane do usługi Azure Monitor, jeśli są one dostępne dla alertów i analizy przy użyciu Eksploratora metryk.

WYJAŚNIENIE Ta funkcja jest dostępna na [szybciej metryki alertów dotyczących dzienników, obecnie dostępna w ograniczonej publicznej wersji zapoznawczej](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/). Lista wartości pomocy technicznej znajduje się w temacie [obsługiwanych metod metryki i tworzenia nowych alertów metryk](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md).

### <a name="event-hubs"></a>Event Hubs
Oprócz używania narzędzi na platformie Azure do analizowania danych monitorowania, można ją przesłać do zewnętrznego narzędzia, takie jak informacje o zabezpieczeniach i produktów do zarządzania (SIEM) zdarzenia. Przekazywanie to zwykle odbywa się za pośrednictwem [usługi Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/). 

Możesz też uzyskać wskazówki dotyczące różnych rodzajów danych monitorowania na [Stream danych monitorowania platformy Azure do Centrum zdarzeń do użycia przez narzędzie zewnętrzne](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [dostępne dane monitorowania](monitoring-data-sources.md) dla różnych zasobów na platformie Azure. 