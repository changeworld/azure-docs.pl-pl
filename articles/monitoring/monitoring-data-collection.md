---
title: Zbieranie danych na platformie Azure monitoring | Dokumentacja firmy Microsoft
description: Omówienie monitorowania danych, które są zbierane z aplikacji i usług na platformie Azure i narzędzia używane do analizując je.
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
ms.openlocfilehash: d3ebd512f8244de74c009ac8a2936ed8e817dad9
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991352"
---
# <a name="collecting-monitoring-data-in-azure"></a>Zbieranie danych monitorowania na platformie Azure
Ten artykuł zawiera omówienie monitorowania danych, które są zbierane z aplikacji i usług na platformie Azure i narzędzia używane do analizując je. 

## <a name="types-of-monitoring-data"></a>Typy danych monitorowania
Wszystkie dane monitorowania pasuje do jednej z dwóch podstawowych typów, metryk lub dzienników. Każdy typ ma różne charakterystyki i najlepiej nadaje się do konkretnych scenariuszach zgodnie z poniższym opisem.

### <a name="metrics"></a>Metryki
Metryki są wartości liczbowe, które opisują niektóre aspekty systemu w określonym punkcie w czasie. Obejmują one różne dane, w tym samą wartość, czas, który został zebrany wartość, typ miary, którą reprezentuje wartość i określonego zasobu, która wartość jest skojarzona z. Metryki są zbierane w regularnych odstępach czasu, czy po zmianie wartości. Na przykład może zbierać użycie procesora na maszynie wirtualnej, co minutę lub liczby użytkowników zalogował się do aplikacji co 10 minut.

Metryki są uproszczone i zdolność do obsługi scenariuszy w czasie rzeczywistym w pobliżu. Są one szczególnie użyteczne w przypadku alertów, ponieważ często może być próbkowany metryki i alertu można szybko wywoływane z logiką stosunkowo proste. Na przykład możesz wyzwalać alert, gdy Metryka przekracza wartość progową lub wyzwalać alert, gdy różnicy między wartością dwie metryki osiągnie określoną wartość.

Metryki poszczególnych zwykle zapewniają mały wgląd w ich własnych. Zapewniają one pojedynczej wartości bez żadnego kontekstu innych niż porównania prosty próg. Są one przydatne mimo że w połączeniu z innymi metrykami w celu zidentyfikowania wzorców i trendów lub w połączeniu z dzienników, które zapewniają kontekstowe wokół konkretnych wartości. Na przykład ilu użytkowników w swojej aplikacji w danym momencie może zorientować się trochę o kondycji aplikacji. Nagły spadek użytkowników, mimo że wskazywanym przez wiele wartości tego samego metryki może wskazywać na problem. Nadmierne wyjątków zgłoszonych przez aplikację i wskazywanym przez oddzielne metryki umożliwiają identyfikowanie problemów aplikacji powoduje spadek. Zdarzenia utworzone przez aplikację, w szczególności identyfikowanie błędów składników aplikacji mogą pomóc w określeniu głównych przyczyn.

Alerty oparte na dziennikach nie odpowiadają jako jako alerty na podstawie metryk, ale mogą to być bardziej złożonej logiki. Można utworzyć alert w oparciu o wyniki dowolnego zapytania, który wykonuje złożone analizy danych z wielu źródeł.

### <a name="logs"></a>Dzienniki
Dzienniki zawierają różne rodzaje danych zorganizowanych w rekordy z różnymi zestawami właściwości dla każdego typu. Dzienniki mogą zawierać wartości liczbowe, takie jak metryki, ale zwykle zawierają dane tekstowe za pomocą szczegółowe opisy. Dodatkowo różnią się od metryki, różnią się one w ich struktury i często nie są zbierane w regularnych odstępach czasu.

Typowe typ wpisu dziennika jest zdarzeniem. Zdarzenia są zbierane sporadycznie tworzonych przez aplikację lub usługę i zazwyczaj zawierają informacje wystarczające do zapewnienia pełnego kontekstu własnych.  Na przykład zdarzenie może wskazywać, że określony zasób został utworzony lub zmodyfikowany, nowy host uruchamiane w odpowiedzi na zwiększonego ruchu lub wykryto błąd w aplikacji.

Dzienniki są szczególnie przydatne w przypadku łączenia danych z wielu źródeł do kompleksowej analizy i określania trendów wraz z upływem czasu. Ponieważ format danych może się różnić w aplikacji można utworzyć niestandardowe dzienniki przy użyciu struktury, których potrzebują. Metryki nawet mogą być replikowane w dziennikach połączyć ją z innych danych monitorowania umożliwia analizę trendów i innych danych.


## <a name="monitoring-tools-in-azure"></a>Narzędzia do monitorowania na platformie Azure
Monitorowanie danych na platformie Azure są zbierane i analizować za pomocą wielu źródeł, które są opisane w poniższych sekcjach.

### <a name="azure-metrics"></a>Azure Metrics
Metryki z zasobów platformy Azure i aplikacje są zbierane w metryk usługi Azure. Dane metryk jest integrowany strony w witrynie Azure portal do określonych zasobów platformy Azure, takie jak maszyny wirtualne, które obejmują wykresów metryki, takie jak wykorzystanie procesora CPU i sieci dla wybranej maszyny. Ponadto mogą być analizowane za pomocą [Eksploratora metryk](../monitoring-and-diagnostics/monitoring-metric-charts.md) którego wykresu wartości wielu metryk wraz z upływem czasu.  Można wyświetlać wykresy interakcyjnie lub przypiąć je do pulpitu nawigacyjnego, aby wyświetlić je z innymi wizualizacjami. Możesz również pobrać metryki za pomocą polecenia [platformy Azure, interfejsu API REST monitorowania](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

Szczegóły można uzyskać danych metryki, które są zbierane przez różnych rodzajów zasobów platformy Azure w [źródła danych na platformie Azure monitorowania](monitoring-data-sources.md). 

![Eksplorator metryk](media/monitoring-data-collection/metrics-explorer.png)


### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure 
[Dziennika aktywności platformy Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) są przechowywane dzienniki dotyczące konfiguracji i kondycji usług platformy Azure. Eksplorator dziennika aktywności można użyć do wyświetlenia tych dzienników w witrynie Azure portal, ale są one często [kopiowane do usługi Log Analytics](../log-analytics/log-analytics-activity.md) analizowanej z innymi danymi dziennika.

Eksplorator dziennika aktywności umożliwia wyświetlanie dziennika aktywności, filtrowana w celu spełniających określone kryteria.  Większość zasobów będzie miał dziennikiem aktywności opcji w menu, ich w witrynie Azure portal, wyświetlającego Explorer dziennika aktywności przefiltrowana pod kątem tego zasobu. Możesz również pobrać Dzienniki aktywności z [platformy Azure, interfejsu API REST monitorowania](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md).

![Eksplorator dziennika aktywności](media/monitoring-data-collection/activity-log-explorer.png)


### <a name="log-analytics"></a>Log Analytics
Usługa log Analytics udostępnia wspólną platformę danych zarządzania na platformie Azure. Jest podstawowej usługi używane do przechowywania i analizy dzienników na platformie Azure, zbieranie danych z różnych źródeł, w tym agenci na maszynach wirtualnych, rozwiązań do zarządzania i zasobami platformy Azure. Dane z innych źródeł, takich jak metryki i dziennika działań mogą być kopiowane do usługi Log Analytics, aby można było utworzyć pełną centralne repozytorium danych monitorowania.

Log Analytics zawiera zaawansowany język zapytań do analizowania zebranych danych.  Możesz użyć [portale do przeszukiwania dzienników](../log-analytics/log-analytics-log-search-portals.md) interaktywnego pisania i testowaniu zapytań i analizowania ich wyniki. Możesz również [tworzenia widoków](../log-analytics/log-analytics-view-designer.md) wizualizować wyniki przeszukiwania dzienników lub Wklej wyniki zapytania bezpośrednio do pulpitu nawigacyjnego platformy Azure.  Rozwiązania do zarządzania zawierają wyszukiwań w dziennikach i widoków w usłudze Log Analytics do analizowania danych, które pobierają. Innych usług, takich jak usługi Application Insights przechowują dane w usłudze Log Analytics i obejmują dodatkowe narzędzia do analizy.  

![Dzienniki](media/monitoring-data-collection/logs.png)

### <a name="application-insights"></a>Application Insights
Usługa Application Insights gromadzi dane telemetryczne dla aplikacji sieci web zainstalowany na wielu różnych platformach. Jego przechowuje dane w metryki usługi Azure i usługi Log Analytics i udostępnia rozbudowany zestaw zaawansowanych narzędzi, na podstawie istniejących narzędzi do analizowania tych danych, do analizowania i wizualizowania danych. Umożliwia to korzystanie ze wspólnego zestawu usług, takich jak alerty, wyszukiwań w dziennikach i pulpity nawigacyjne, które można używać na potrzeby innych monitorowania.


![App Insights](media/monitoring-data-collection/app-insights.png)

### <a name="service-map"></a>Mapa usługi
Mapa usługi zawiera wizualną reprezentację maszyn wirtualnych przy użyciu zależności i procesami. Przechowuje większość z tych danych w usłudze Log Analytics, dzięki czemu można analizować je z innymi danymi zarządzania. Konsola mapy usługi również pobiera dane z usługi Log Analytics, aby przedstawić je w kontekście maszyny wirtualnej, analizowane.

![Mapa usługi](media/monitoring-data-collection/service-map.png)


## <a name="transferring-monitoring-data"></a>Transfer danych monitorowania

### <a name="metrics-to-logs"></a>Metryki do dzienników
Metryki mogą być również replikowane do usługi Log Analytics, aby przeprowadzić analizę złożonych z innymi typami danych przy użyciu jego zaawansowany język zapytań. Ponadto można przechowywać dane dziennika przez dłuższy czas niż metryk, które można wykonać analizy trendów wraz z upływem czasu. Gdy metryk lub inne dane dotyczące wydajności są przechowywane w usłudze Log Analytics, która danych działa jako dziennik. Metryki do obsługi niemal w czasie rzeczywistym analiz i alertów podczas korzystania z dzienników do wyznaczania trendów i analiz z innymi danymi.

Możesz uzyskać wskazówki dotyczące zbieranie metryk z zasobów platformy Azure w [zbieranie dzienników platformy Azure usługi i metryk do użycia w usłudze Log Analytics](../log-analytics/log-analytics-azure-storage.md). Uzyskaj wskazówki dotyczące zbieranie metryk zasobów z zasobów PaaS platformy Azure w [skonfigurować zbieranie metryk zasobów PaaS platformy Azure z usługą Log Analytics](../log-analytics/log-analytics-collect-azurepass-posh.md).

### <a name="logs-to-metrics"></a>Dzienniki, metryki
Zgodnie z powyższym opisem metryki są bardziej elastyczny niż dzienników, dzięki czemu możesz tworzyć alerty z mniejszymi opóźnieniami i przy niskich kosztach. Usługa log Analytics zbiera znacznej ilości danych liczbowych, który będzie odpowiedni dla metryk, ale nie jest przechowywany w metryk usługi Azure. Typowym przykładem są dane dotyczące wydajności zbierane z agentów i rozwiązań do zarządzania. Niektóre z tych wartości mogły zostać skopiowane do metryk platformy Azure, jeśli są one dostępne dla alertów i analizy przy użyciu Eksploratora metryk.

WYJAŚNIENIE Ta funkcja jest dostępna na [szybciej metryki alertów dotyczących dzienników, obecnie dostępna w ograniczonej publicznej wersji zapoznawczej](https://azure.microsoft.com/blog/faster-metric-alerts-for-logs-now-in-limited-public-preview/). Lista wartości pomocy technicznej znajduje się w temacie [obsługiwanych metod metryki i tworzenia nowych alertów metryk](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md).

### <a name="event-hub"></a>Centrum zdarzeń
Oprócz używania narzędzi na platformie Azure do analizowania danych monitorowania, można przekazywać do zewnętrznego narzędzia Informacje zabezpieczeń i produktów do zarządzania (SIEM) zdarzenia. Zazwyczaj jest to wykonywane przy użyciu [usługi Azure Event Hub](https://docs.microsoft.com/azure/event-hubs/). 

Możesz też uzyskać wskazówki dotyczące różnych rodzajów danych monitorowania na [Stream danych monitorowania platformy Azure do Centrum zdarzeń do użycia przez narzędzie zewnętrzne](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [dostępne dane monitorowania](monitoring-data-sources.md) dla różnych zasobów na platformie Azure. 