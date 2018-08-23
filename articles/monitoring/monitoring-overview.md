---
title: Monitorowanie aplikacji platformy Azure i zasobów | Dokumentacja firmy Microsoft
description: Omówienie usług i funkcji Microsoft składających się na pełną strategię monitorowania usług i aplikacji platformy Azure.
author: bwren
editor: ''
services: azure-monitor
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: bwren
ms.openlocfilehash: 878765a1f84e0825e86def2b59310732b22aaacc
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055797"
---
# <a name="monitoring-azure-applications-and-resources"></a>Monitorowanie aplikacji i zasobów platformy Azure

Monitorowanie to proces zbierania i analizowania danych w celu ustalania wydajności, kondycji i dostępności aplikacji biznesowych i zasobów, od których one zależą. Skuteczna strategia monitorowania pomaga w zrozumieniu szczegółów działania składników aplikacji. Pomaga również wydłużyć czas pracy: proaktywnie informuje o problemach krytycznych, aby można je było rozwiązać, zanim spowodują poważniejsze skutki.

Platforma Azure obejmuje wiele usług, które indywidualnie wykonują określoną rolę lub zadanie w obszarze monitorowania. Razem usługi te zapewniają kompleksowe rozwiązanie umożliwiające zbieranie, analizowanie oraz działanie na podstawie danych telemetrycznych z aplikacji użytkownika oraz zasobów platformy Azure, które je obsługują. Mogą one również służyć do monitorowania krytycznych zasobów lokalnych, aby zapewnić hybrydowe środowisko monitorowania. Zapoznanie się z dostępnymi narzędziami i danymi stanowi pierwszy etap tworzenia pełnej strategii monitorowania aplikacji. 

Na poniższym wykresie przedstawiono widok koncepcyjny składników współpracujących w celu zapewnienia monitorowania zasobów platformy Azure. W poniższych sekcjach opisano te składniki i udostępniono linki do szczegółowych informacji technicznych.

![Omówienie monitorowania](media/monitoring-overview/monitoring-products-overview.png)


## <a name="shared-capabilities"></a>Współdzielone możliwości
Usługi monitorowania podstawowego i szczegółowego mają wspólną funkcjonalność, która zapewnia następujące możliwości. 

### <a name="alerts"></a>Alerty
[Alerty platformy Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md) proaktywnie powiadamiają użytkownika o warunkach krytycznych i w razie potrzeby powodują podjęcie akcji naprawczych. Reguły alertów mogą używać danych z wielu źródeł, m.in. metryk i dzienników. Używają [grup akcji](../monitoring-and-diagnostics/monitoring-action-groups.md), które zawierają unikatowe zestawy adresatów i akcji w odpowiedzi na alert. W zależności od wymagań można ustawić alerty w taki sposób, aby uruchamiały akcje zewnętrzne za pomocą elementów webhook oraz integrowały się z narzędziami ITSM.

### <a name="dashboards"></a>Pulpity nawigacyjne
[Pulpitów nawigacyjnych platformy Azure](../azure-portal/azure-portal-dashboards.md) można użyć do łączenia różnych typów danych w jednym okienku w witrynie [Azure Portal](https://portal.azure.com). Następnie można udostępnić pulpit nawigacyjny innym użytkownikom platformy Azure. 

Na przykład można utworzyć pulpit nawigacyjny, który łączy:
- Kafelki przedstawiające wykres metryk
- Tabelę dzienników aktywności
- Wykres użycia z usługi Application Insights
- Dane wyjściowe przeszukiwania dzienników w usłudze Log Analytics

Można również eksportować dane usługi Log Analytics do rozwiązania [Power BI](https://docs.microsoft.com/power-bi/). W jego ramach można korzystać z dodatkowych wizualizacji. Można również udostępnić dane innym użytkownikom w swojej organizacji i poza nią.

### <a name="metrics-explorer"></a>Eksplorator metryk
[Metryki](../monitoring-and-diagnostics/monitoring-overview-metrics.md) to wartości numeryczne generowane przez zasób platformy Azure, dzięki którym można lepiej zrozumieć działanie i wydajność zasobu. Za pomocą Eksploratora metryk można wysłać metryki do usługi Log Analytics do analizy z użyciem danych z innych źródeł.


## <a name="core-monitoring"></a>Monitorowanie podstawowe
Monitorowanie podstawowe zapewnia niezbędne, wymagane monitorowanie w zasobach platformy Azure. Te usługi wymagają minimalnej konfiguracji i zbierają podstawowe dane telemetryczne, z których korzystają usługi monitorowania Premium.    

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) umożliwia monitorowanie podstawowe dla usług Azure, pozwalając na zbieranie [metryk](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [dzienników aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) i [dzienników diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Na przykład dziennik aktywności informuje o utworzeniu lub zmodyfikowaniu nowych zasobów. 

Są dostępne metryki, które dostarczają statystyki wydajności dla różnych zasobów, a nawet systemu operacyjnego w maszynie wirtualnej. Można wyświetlić te dane za pomocą jednego z eksploratorów w witrynie Azure Portal i utworzyć alerty na podstawie tych metryk. Usługa Azure Monitor dostarcza najszybszy potok metryk (5 minut na 1 minutę), dlatego należy jej używać do krytycznych czasowo alertów i powiadomień. 

Można również wysyłać te metryki i dzienniki usługi Azure Log Analytics do analizy trendów i szczegółowych lub utworzyć dodatkowe reguły alertów w celu proaktywnego powiadamiania o istotnych kwestiach w wyniku analizy.  

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../advisor/advisor-overview.md) stale monitoruje konfigurację zasobów i dane telemetryczne użycia. Następnie udostępnia spersonalizowane rekomendacje na podstawie najlepszych rozwiązań. Stosowanie się do tych rekomendacji pomaga zwiększyć wydajność, bezpieczeństwo i dostępność zasobów, które obsługują aplikacje.

### <a name="service-health"></a>Service Health
Kondycja aplikacji zależy od usług platformy Azure, na których się opiera. Usługa [Azure Service Health](../service-health/service-health-overview.md) identyfikuje wszelkie problemy z usługami Azure, które mogą wpływać na aplikację. Usługa Service Health pomaga również organizować zaplanowaną konserwację.

### <a name="activity-log"></a>Dziennik aktywności
[Dziennik aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) zapewnia dane dotyczące działania zasobów platformy Azure. Te informacje obejmują:
- Zmiany konfiguracji zasobu.
- Zdarzenia dotyczące kondycji usługi.
- Rekomendacje dotyczące lepszego wykorzystania zasobu.
- Informacje dotyczące operacji skalowania automatycznego. 

Dzienniki określonego zasobu można wyświetlać na jego stronie w witrynie Azure Portal. Można też wyświetlać dzienniki z wielu zasobów w Eksploratorze dzienników aktywności. 

Można również wysłać wpisy dziennika aktywności do usługi Log Analytics. W tej usłudze można analizować dzienniki z wykorzystaniem danych zbieranych przez rozwiązania do zarządzania, agentów na maszynach wirtualnych oraz inne zasoby.

## <a name="deep-monitoring-services"></a>Usługi monitorowania szczegółowego
Następujące usługi platformy Azure zapewniają bogate możliwości dokładniejszego zbierania i analizowania danych monitorowania. Te usługi opierają się na monitorowaniu podstawowym i wykorzystują wspólną funkcjonalność platformy Azure. Umożliwiają zaawansowaną analizę z użyciem zebranych danych, dostarczając unikatowe szczegółowe informacje dotyczące aplikacji i infrastruktury. Przedstawiają dane w ramach scenariuszy przeznaczonych dla różnych grup odbiorców.

### <a name="deep-application-monitoring"></a>Szczegółowe monitorowanie aplikacji
#### <a name="application-insights"></a>Application Insights
Usługa [Azure Application Insights](http://azure.microsoft.com/documentation/services/application-insights) służy do monitorowania dostępności, wydajności i użycia aplikacji, niezależnie od tego, czy jest obsługiwana w chmurze czy lokalnie. 

Przez instrumentowanie aplikacji do współpracy z usługą Application Insights można uzyskać szczegółowe informacje i wdrożyć scenariusze DevOps. Można szybko identyfikować i diagnozować błędy, zamiast czekać, aż zgłosi je użytkownik. Dzięki zbieranym informacjom można podejmować świadome decyzje w zakresie konserwacji i ulepszeń aplikacji. 

Usługa Application Insights ma szeroką gamę narzędzi do interakcji z danymi, które zbiera. Usługa Application Insights przechowuje dane we wspólnym repozytorium. Może korzystać ze wspólnej funkcjonalności, np. alertów, pulpitów nawigacyjnych, a także szczegółowej analizy przy użyciu języka zapytań usługi Log Analytics.

### <a name="deep-infrastructure-monitoring"></a>Szczegółowe monitorowanie infrastruktury
#### <a name="log-analytics"></a>Log Analytics
Usługa [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) odgrywa kluczową rolę w monitorowaniu Azure, zbierając dane z różnych źródeł (np. narzędzi firm innych niż Microsoft) do jednego repozytorium. Można w niej analizować dane przy użyciu zaawansowanego języka zapytań. 

Usługi Application Insights i Azure Security Center przechowują swoje dane bezpośrednio w magazynie danych usługi Log Analytics i korzystają z jego aparatu analizy. Dane są również zbierane z platformy Azure Monitor, rozwiązań do zarządzania oraz agentów zainstalowanych na maszynach wirtualnych w chmurze lub lokalnie. Ta wspólna funkcjonalność pomaga utworzyć pełny obraz środowiska.

#### <a name="management-solutions"></a>Rozwiązania do zarządzania
[Rozwiązania do zarządzania](../log-analytics/log-analytics-add-solutions.md) to utworzone zestawy logiki zawierające szczegółowe informacje dotyczące określonej aplikacji lub usługi. Korzystają one z usługi Log Analytics, aby przechowywać i analizować zbieranie dane monitorowania. 

Firma Microsoft oraz jej partnerzy oferują rozwiązania do zarządzania, które zapewniają monitorowanie różnych usług Azure i innych firm. Przykłady rozwiązań do monitorowania obejmują:
* [Monitorowanie kontenera](../log-analytics/log-analytics-containers.md) ułatwiające wyświetlanie hostów kontenerów oraz zarządzanie nimi.
* Usługa [Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md), która zbiera i wizualizuje metryki wydajności baz danych SQL Azure.

Wszystkie dostępne rozwiązania do zarządzania można wyświetlić w witrynie Azure Portal w obszarze ekranu *Monitor*. 

#### <a name="network-monitoring"></a>Monitorowanie sieci
Istnieje kilka narzędzi, które współpracują ze sobą, aby monitorować różne aspekty sieci, na platformie Azure lub lokalnie.  

Usługa [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) umożliwia oparte na scenariuszach monitorowanie i diagnostykę różnych scenariuszy sieciowych na platformie Azure. Przechowuje ona dane w metrykach i diagnostyce Azure do dalszej analizy. Współpracuje z następującymi rozwiązaniami do monitorowania różnych aspektów sieci użytkownika. 

[Network Performance Monitor (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) to rozwiązanie do monitorowania sieci w chmurze, które monitoruje łączność między chmurami publicznymi, centrami danych oraz środowiskami lokalnymi.

[Monitor usługi ExpressRoute](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) jest funkcją NPM, która monitoruje łączność end-to-end i wydajność za pośrednictwem obwodów usługi Azure ExpressRoute.

[DNS Analytics](../log-analytics/log-analytics-dns.md) to rozwiązanie, które zapewnia bezpieczeństwo, wydajność i szczegółowe informacje dotyczące operacji w oparciu o serwery DNS.

[Monitor punktu końcowego usługi](../networking/network-monitoring-overview.md) testuje dostępność aplikacji i wykrywa wąskie gardła wydajności między zasobami lokalnymi, sieciami operatora a centami danych w chmurze/prywatnymi.


#### <a name="service-map"></a>Mapa usługi
Usługa [Service Map](../operations-management-suite/operations-management-suite-service-map.md) zapewnia szczegółowe informacje na temat środowiska IaaS, analizując maszyny wirtualne z różnymi procesami i zależnościami dotyczącymi innych komputerów i procesów zewnętrznych. Integruje zdarzenia, dane dotyczące wydajności i rozwiązania do zarządzania w usłudze Log Analytics. Następnie można wyświetlić te dane w kontekście poszczególnych komputerów oraz ich powiązań z resztą środowiska. 

Usługa Service Map przypomina [mapę aplikacji w usłudze Application Insights](../application-insights/app-insights-app-map.md). Koncentruje się na składnikach infrastruktury, które obsługują aplikacje.


## <a name="example-scenarios"></a>Przykładowe scenariusze
Poniżej znajdują się przykłady wysokiego poziomu, które przedstawiają, jak można używać różnych narzędzi do monitorowania na platformie Azure w różnych scenariuszach.

### <a name="monitoring-a-web-application"></a>Monitorowanie aplikacji sieci Web
Weźmy pod uwagę aplikację sieci Web wdrożoną na platformie Azure za pomocą usługi Azure App Service, Azure Storage i bazy danych SQL. Zacznij od uzyskania dostępu do [metryk](../monitoring-and-diagnostics/monitoring-overview-metrics.md) i [dzienników aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) dla tych zasobów na ich stronach w witrynie Azure Portal. Zwróć uwagę na krytyczne informacje, takie jak liczba żądań do aplikacji oraz średni czas odpowiedzi. Ponadto zidentyfikuj zmiany w konfiguracji.

Następnie przejdź do Monitora w witrynie, aby wyświetlić razem metryki i dzienniki dotyczące różnych zasobów. W trakcie określania standardowych parametrów dla metryk [utwórz reguły alertów](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md). Te reguły proaktywnie powiadamiają, kiedy na przykład średni czas odpowiedzi przekroczy próg. Aby uzyskać szybki podgląd codziennej wydajności aplikacji, utwórz pulpit nawigacyjny Azure, aby pokazać wykresy metryk, które przedstawiają krytyczne wskaźniki KPI.

Aby dokładniej monitorować aplikację, [dokonaj konfiguracji dla usługi Application Insights](../application-insights/quick-monitor-portal.md). Teraz możesz zbierać dodatkowe dane, które dostarczają dalszych szczegółowych informacji na temat działania i wydajności aplikacji. Usługa Application Insights wykrywa podstawowe relacje między składnikami aplikacji. Uwzględnia wizualne przedstawienie za pośrednictwem [mapy aplikacji](../application-insights/app-insights-app-map.md) razem ze [śledzeniem end-to-end](../application-insights/app-insights-transaction-diagnostics.md), aby dokładnie zdiagnozować składnik, zależność lub wyjątek, którego dotyczy problem. 

Utwórz [Testy dostępności](../application-insights/app-insights-monitor-web-app-availability.md), aby proaktywnie testować aplikację z różnych regionów. Aby pomóc deweloperom, można [włączyć Profiler](../application-insights/enable-profiler-compute.md), co pozwoli śledzić żądania i wszelkie wyjątki do określonego wiersza kodu. Aby mieć lepszą widoczność w zakresie usług używanych w swojej aplikacji, dodaj [rozwiązanie SQL Analytics](../log-analytics/log-analytics-azure-sql.md), aby zebrać dodatkowe dane w usłudze Log Analytics. 

Po pewnym czasie możesz podjąć decyzję o zbadaniu podstawowej przyczyny występowania okresów, gdy wydajność witryny spada poniżej progu. W tym celu napisz zapytanie przy użyciu usługi Log Analytics. Pomoże to powiązać dane dotyczące użycia i wydajności zebrane przez usługę Application Insights z danymi dotyczącymi konfiguracji i wydajności w zasobach Azure, które obsługują aplikację.


### <a name="monitoring-virtual-machines"></a>Monitorowanie maszyn wirtualnych
Na platformie Azure działają różne maszyny wirtualne z systemami Windows i Linux. Usługa Azure Monitor służy do wyświetlania [dzienników aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) oraz [metryk na poziomie hosta](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Dodaj [rozszerzenie Diagnostyka Azure](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension) do maszyn wirtualnych, aby zbierać metryki z systemu operacyjnego gościa. Następnie utwórz [reguły alertów](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) do proaktywnego powiadamiania, gdy podstawowe metryki, takie jak wykorzystanie procesora i pamięć, przekroczą progi.

Aby zebrać więcej szczegółowych informacji na temat maszyn wirtualnych z uruchomioną aplikacją biznesową, [utwórz obszar roboczy usługi Log Analytics i włącz rozszerzenie maszyny wirtualnej](../log-analytics/log-analytics-quick-collect-azurevm.md) na każdej maszynie. Skonfiguruj [zbieranie różnych źródeł danych](../log-analytics/log-analytics-data-sources.md) dla aplikacji i [utwórz widoki](../log-analytics/log-analytics-view-designer.md), aby wysyłać raporty dotyczące codziennego działania i wydajności. Następnie [utwórz reguły alertów](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md), które będą informować po odebraniu zdarzeń z określonymi błędami. 

Aby stale monitorować kondycję zainstalowanego agenta, dodaj [rozwiązanie do zarządzania usługą Agent Health](../operations-management-suite/oms-solution-agenthealth.md). Aby uzyskać więcej szczegółowych informacji dotyczących aplikacji, [dodaj agenta zależności](../operations-management-suite/operations-management-suite-service-map-configure.md) do maszyn wirtualnych, aby dodać je do usługi [Service Map](../operations-management-suite/operations-management-suite-service-map.md). Usługa Service Map umożliwia odnalezienie krytycznych procesów i identyfikuje połączenia między maszynami za pomocą innych usług. 

Po zgłoszonej awarii użyj usługi Service Map do wykonania analizy, aby zidentyfikować określone maszyny, na których wystąpił problem. Następnie utwórz [zapytanie na danych usługi Log Analytics](../log-analytics/log-analytics-log-search-new.md), aby zidentyfikować problem w przyszłości. Oraz utwórz regułę alertu w celu proaktywnego powiadamiania po wykryciu warunku.



## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o usługach:

* [Azure Monitor](https://azure.microsoft.com/services/monitor/), aby rozpocząć pracę z metrykami i alertami monitorowania podstawowego.
* [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/), jeśli próbujesz diagnozować problemy w aplikacji sieci Web usługi App Service.
* [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) służącej do analizowania zebranych danych i dzienników monitorowania.
