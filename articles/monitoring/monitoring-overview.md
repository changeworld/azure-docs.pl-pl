---
title: Monitorowanie aplikacji platformy Azure i zasobów | Dokumentacja firmy Microsoft
description: Omówienie usługi i funkcje, składających się na pełną strategię monitorowania usług systemu Azure i aplikacji.
author: bwren
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1b962c74-8d36-4778-b816-a893f738f92d
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: robb,bwren
ms.openlocfilehash: 00ec9364a900510aeadcb68b19b57be528fb9c50
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
ms.locfileid: "30267213"
---
# <a name="monitoring-azure-applications-and-resources"></a>Monitorowanie aplikacji platformy Azure i zasobów

Monitorowanie jest czynnością zbierania i analizowania danych w celu ustalenia wydajności, kondycji i dostępności aplikacji biznesowych i zasobów, których ona zależy. Strategia monitorowania skuteczne pomaga w zrozumieniu szczegółowe działaniem składników aplikacji. Pomaga również zwiększenia Twojej pracy aktywnego informujący poważnych problemów, dzięki czemu można je rozwiązać, zanim wystąpią problemy.

Azure zawiera wiele usług, wykonujących indywidualnie określonych ról i zadań w obszarze monitorowania. Te usługi dostarczania ze sobą, kompleksowe rozwiązanie umożliwiające obsługę zbierania, analizowanie i działające na podstawie danych telemetrycznych z aplikacji i zasobów platformy Azure, które je obsługują. Może również współpracować do monitorowania zasobów lokalnych krytyczne zapewnić monitorowanie środowiska hybrydowego. Opis narzędzi i dane, które są dostępne jest pierwszym etapem opracowanie pełnej strategii monitorowania aplikacji. 

Na poniższym diagramie przedstawiono koncepcję składników, które współpracują, aby zapewnić monitorowanie zasobów platformy Azure. W poniższych sekcjach opisano te składniki i udostępniają linki do szczegółowych informacji technicznych.

![Omówienie monitorowania](media/monitoring-overview/monitoring-products-overview.png)


## <a name="shared-capabilities"></a>Współdzielone możliwości
Podstawowe i usługi monitorowania dokładnego korzystają z funkcji, która zapewnia następujące możliwości. 

### <a name="alerts"></a>Alerty
[Alerty Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md) proaktywnie powiadamia użytkownika o krytycznych warunków i potencjalnie podjęcia działań naprawczych. Reguły alertów można użyć danych z wielu źródeł, takich jak dzienniki i metryki. Używają [grupy akcji](../monitoring-and-diagnostics/monitoring-action-groups.md), które zawierają zestawy unikatowy adresatów i akcji w odpowiedzi na alert. W zależności od wymagań może mieć alertów, Uruchamianie zewnętrznego akcji za pomocą elementów webhook i integracja z narzędziami Zarządzanie usługami IT —.

### <a name="dashboards"></a>Pulpity nawigacyjne
Można użyć [Azure pulpity nawigacyjne](../azure-portal/azure-portal-dashboards.md) do łączenia różnych typów danych w jednego okienka w [portalu Azure](https://portal.azure.com). Pulpit nawigacyjny można następnie udostępniać innym użytkownikom Azure. 

Na przykład można utworzyć pulpit nawigacyjny, który łączy:
- Kafelki, które Pokaż wykres metryk
- Spis Dzienniki aktywności
- Wykres użycia za pomocą usługi Application Insights
- Dane wyjściowe przeszukiwania dziennika w analizy dzienników

Można również eksportować dane analizy dzienników do [usługi Power BI](https://docs.microsoft.com/power-bi/). Istnieje można korzystać z dodatkowych wizualizacji. Można również udostępnić dane do innych użytkowników w organizacji i poza nią.

### <a name="metrics-explorer"></a>Eksplorator metryk
[Metryki](../monitoring-and-diagnostics/monitoring-overview-metrics.md) są generowane przez zasobów platformy Azure, aby lepiej zrozumieć działanie i wydajności zasobu wartości liczbowe. Za pomocą Eksploratora metryk, możesz wysłać metryk do analizy dzienników dla analizy danych z innych źródeł.


## <a name="core-monitoring"></a>Monitorowanie podstawowe
Podstawowe funkcje monitorowania udostępnia podstawowe, wymagane monitorowanie z przekraczaniem zasobów platformy Azure. Te usługi wymagają minimalnej konfiguracji i zebrać dane telemetryczne podstawowe usługi monitorowania premium używały.    

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) umożliwia monitorowanie podstawowe dla usług Azure, zezwalając kolekcję [metryki](../monitoring-and-diagnostics/monitoring-overview-metrics.md), [Dzienniki aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), i [dzienników diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Na przykład dziennika aktywności informuje po utworzeniu lub zmodyfikowaniu nowych zasobów. 

Metryki są dostępne statystyki które zapewniają różne zasoby i nawet systemu operacyjnego wewnątrz maszyny wirtualnej. Możesz wyświetlić te dane z jednego z eksploratorów w portalu Azure i tworzyć alerty oparte na tych metryk. Azure Monitor udostępnia najszybszą metryki potoku (5 minut do 1 minutę), dlatego powinien być używany dla czasu krytyczne alerty i powiadomienia. 

Można również wysyłać te metryki i analizy trendów i szczegółowe dzienniki usługi Analiza dzienników Azure lub tworzyć dodatkowe reguły alertów do aktywnego powiadomienia poważnych problemów, w wyniku tego analizy.  

### <a name="azure-advisor"></a>Azure Advisor
[Klasyfikator Azure](../advisor/advisor-overview.md) monitoruje telemetrii konfiguracji i użycia zasobów. Następnie udostępnia spersonalizowane zalecenia na podstawie najlepszych rozwiązań. Po te zalecenia pomaga zwiększyć wydajność, zabezpieczeń i dostępności zasobów, które obsługują aplikacje.

### <a name="service-health"></a>Service Health
Kondycji aplikacji korzysta z usług Azure, których ona zależy. [Kondycja usługi Azure](../service-health/service-health-overview.md) identyfikuje wszelkie problemy z usługami Azure, które mogą wpływać na aplikację. Kondycja usługi pomaga zaplanować zaplanowanej konserwacji.

### <a name="activity-log"></a>Dziennik aktywności
[Dziennik aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) zapewnia dane dotyczące działania zasobów platformy Azure. Informacje te obejmują:
- Zmiany konfiguracji do zasobu.
- Zdarzenia kondycji usługi.
- Zalecenia dotyczące lepsze wykorzystanie zasobów.
- Informacje dotyczące operacji skalowania automatycznego. 

Dzienniki dla określonego zasobu można wyświetlać na stronie w portalu Azure. Lub dzienników pochodzących od wielu zasobów można wyświetlić w Eksploratorze dziennika aktywności. 

Można również wysłać wpisy dziennika aktywności do analizy dzienników. Istnieje można analizować dzienniki przy użyciu danych zbieranych przez rozwiązania do zarządzania, agentów na maszynach wirtualnych i innych źródeł.

## <a name="deep-monitoring-services"></a>Dokładnego monitorowania usług
Następujących usług platformy Azure zapewniają bogate możliwości do zbierania i analizowania danych monitorowania jest dokładniejsze. Te usługi kompilacji na podstawowe funkcje monitorowania i móc korzystać z funkcji wspólnej platformie Azure. Udostępniają one zaawansowane analizy zebrane dane, aby zapewnić unikatowy wgląd w aplikacji i infrastruktury. Stanowią one danych w ramach scenariuszy, które są przeznaczone do różnych grup odbiorców.

### <a name="deep-application-monitoring"></a>Monitorowania dokładnego aplikacji
#### <a name="application-insights"></a>Application Insights
Można użyć [Azure Application Insights](http://azure.microsoft.com/documentation/services/application-insights) do monitorowania dostępności, wydajności i użycia aplikacji, czy jest ona hostowana w chmurze lub lokalnie. 

Instrumentacji aplikacji do pracy z usługą Application Insights, można osiągnąć szczegółowych informacji i implementowanie scenariuszy opracowywania oprogramowania. Można szybko zidentyfikować i diagnozowanie błędów bez oczekiwania na użytkownika, aby zgłosić je. Informacje zbierane możesz wprowadzić świadomych wyborów na konserwację aplikacji i ulepszenia. 

Usługa Application Insights zawiera szeroką gamę narzędzi do interakcji z danymi, które zbiera. Usługa Application Insights przechowuje dane w typowych repozytorium. Może korzystać z udostępnionego funkcji, takich jak alerty, pulpity nawigacyjne i szczegółowa analiza przy użyciu języka zapytań usługi Analiza dzienników.

### <a name="deep-infrastructure-monitoring"></a>Monitorowanie infrastruktury bezpośrednich
#### <a name="log-analytics"></a>Log Analytics
[Zaloguj się Analytics](http://azure.microsoft.com/documentation/services/log-analytics) odgrywa kluczową rolę w Azure monitorowanie, zbieranie danych z różnych zasobów (w tym narzędzi firmy Microsoft) w jednym repozytorium. Mogą analizować dane przy użyciu języka zaawansowanych zapytań. 

Usługa Application Insights i Centrum zabezpieczeń Azure są przechowywane w analizy dzienników danych przechowywać i używać jej aparat analizy danych. Dane zbierane są również z monitora Azure, rozwiązania do zarządzania i agentów zainstalowanych na maszynach wirtualnych w chmurze lub lokalnie. Ta funkcja udostępnionego pomaga tworzą pełnego obrazu środowiska.

#### <a name="management-solutions"></a>Rozwiązania do zarządzania
[Rozwiązania do zarządzania](../log-analytics/log-analytics-add-solutions.md) są utworzone zestawy logiki zawierające szczegółowe informacje dla określonej aplikacji lub usługi. Opierają się na analizy dzienników do przechowywania i analizowania danych monitorowania, które pobierają. 

Zarządzania są dostępne rozwiązania firmy Microsoft i partnerów, aby zapewnić monitorowanie różnych Azure i usług innych firm. Monitorowanie rozwiązań należą:
* [Monitorowanie kontenera](../log-analytics/log-analytics-containers.md), który pomaga Wyświetlaj i zarządzaj nimi hosty kontenera.
* [Analiza SQL Azure](../log-analytics/log-analytics-azure-sql.md), który zbiera i wizualizuje metryki wydajności dla baz danych Azure SQL.

Można wyświetlić wszystkich rozwiązań zarządzania dostępnych w portalu Azure w obszarze *Monitor* ekranu. 

#### <a name="network-monitoring"></a>Monitorowanie sieci
Istnieje kilka narzędzi, które współpracują ze sobą, aby monitorować różne aspekty sieci, w usłudze Azure lub lokalnie.  

[Monitor sieci](../network-watcher/network-watcher-monitoring-overview.md) udostępnia oparta na scenariuszu monitorowania i diagnostyki dla różnych scenariuszach sieciowych na platformie Azure. Przechowuje dane w Azure metryki i informacji diagnostycznych do dalszej analizy. Działa on z następujących rozwiązań w zakresie monitorowania różnych aspektów sieci. 

[Sieci monitora wydajności (NPM)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) monitorowania rozwiązania, który monitoruje łączność między chmur publicznych centrów danych i lokalnych środowisk sieci opartej na chmurze.

[Monitor usługi ExpressRoute](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) jest możliwość NPM, która monitoruje łączność end-to-end i wydajności za pośrednictwem obwody usługi Azure ExpressRoute.

[Analiza DNS](../log-analytics/log-analytics-dns.md) to rozwiązanie zapewnia zabezpieczeń, wydajności i operacji związanych insights oparte na serwerach DNS.

[Monitor punktu końcowego usługi](../networking/network-monitoring-overview.md) testów wysyłające aplikacji i wykrywa wąskich gardeł wydajności między lokalnymi, sieci i centrów danych w chmurze i prywatnego.


#### <a name="service-map"></a>Mapa usługi
[Mapa usług](../operations-management-suite/operations-management-suite-service-map.md) zapewnia wgląd w środowisku IaaS, analizując maszyny wirtualne z różnych procesów i zależności na innych komputerach i procesów zewnętrznych. Integruje zdarzenia, dane dotyczące wydajności i rozwiązań do zarządzania w analizy dzienników. Następnie można wyświetlić te dane w kontekście każdego komputera, a jego powiązanie z resztą środowiska. 

Mapa usługi jest podobny do [mapowanie aplikacji w usłudze Application Insights](../application-insights/app-insights-app-map.md). Dotyczy on tych składników infrastruktury, które obsługują aplikacje.


## <a name="example-scenarios"></a>Przykładowe scenariusze
Poniżej przedstawiono przykłady wysokiego poziomu, które przedstawiają, jak można użyć różnych narzędzi do monitorowania na platformie Azure dla różnych scenariuszy.

### <a name="monitoring-a-web-application"></a>Monitorowanie aplikacji sieci web
Należy wziąć pod uwagę aplikacji sieci web wdrożona na platformie Azure za pomocą usługi Azure App Service, usługi Azure Storage i bazy danych SQL. Możesz uruchomić podczas uzyskiwania dostępu do [metryki](../monitoring-and-diagnostics/monitoring-overview-metrics.md) i [Dzienniki aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) dla tych zasobów na swoich stronach w portalu Azure. Możesz wyszukiwać krytyczne informacje, takie jak liczba żądań do aplikacji i Średni czas odpowiedzi. Należy także zidentyfikować zmiany konfiguracji.

Możesz następnie przejdź do monitora w portalu Aby wyświetlić dzienniki dla różnych zasobów i metryk ze sobą. W trakcie określania parametrów standardowe na podstawie metryk, możesz [tworzyć reguły alertów](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md). Te reguły aktywnego powiadamiają użytkownika, na przykład, Średni czas odpowiedzi przekroczy próg. Aby uzyskać szybki przegląd codzienne wydajności aplikacji, należy utworzyć Azure pulpit nawigacyjny, aby pokazać wykresy metryki, które przedstawiają wskaźniki KPI krytyczne.

Aby wykonać dokładniejsze monitorowanie aplikacji, możesz [skonfigurować go do usługi Application Insights](../application-insights/quick-monitor-portal.md). Teraz można zbierać dodatkowe dane, które dodatkowo zapewnia wgląd w działanie i wydajność aplikacji. Usługa Application Insights wykrywa podstawowej relacji między składnikami aplikacji. Umożliwia wizualnej reprezentacji za pośrednictwem [mapowanie aplikacji](../application-insights/app-insights-app-map.md) połączeniu z [śledzenia end-to-end](../application-insights/app-insights-transaction-diagnostics.md) do diagnozowania dokładnie składnika, zależności lub wyjątek, w którym wystąpił problem. 

Możesz utworzyć [testów dostępności](../application-insights/app-insights-monitor-web-app-availability.md) do aktywnego testowania aplikacji w różnych regionach. Aby ułatwić deweloperów, możesz [włączyć profilera](../application-insights/enable-profiler-compute.md) co pozwala na śledzenie żądań i wszelkie wyjątki do określonego wiersza kodu. Dodatkowo wgląd we usług używanych w aplikacji, możesz dodać [rozwiązania analizy SQL](../log-analytics/log-analytics-azure-sql.md) Aby zbierać dodatkowe dane w analizy dzienników. 

Po pewnym czasie zdecydujesz się Znajdź jego główną przyczynę w okresach, gdy wydajność w witrynie spadła poniżej progu. Możesz napisać zapytanie przy użyciu analizy dzienników. Pomaga skorelowania danych użycia i wydajności zbieranych przez usługę Application Insights o konfiguracji i danych wydajności przez zasobów platformy Azure, które obsługują aplikację.


### <a name="monitoring-virtual-machines"></a>Monitorowanie maszyn wirtualnych
Masz kombinację systemu Windows i Linux maszyn wirtualnych działających na platformie Azure. Azure Monitor umożliwia wyświetlanie [Dzienniki aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) i [metryki na poziomie hosta](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Możesz dodać [rozszerzenia diagnostyki Azure](../virtual-machines/linux/tutorial-monitoring.md#install-diagnostics-extension) do maszyn wirtualnych, aby zbierać metryki z systemu operacyjnego gościa. Następnie można utworzyć [reguły alertów](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) do aktywnego powiadomienie, gdy progi dla wielu podstawowych metryk, takich jak użycie procesora i pamięci.

Zbierać więcej szczegółów dotyczących maszyn wirtualnych uruchomionych aplikacji biznesowej, można [utworzyć obszaru roboczego analizy dzienników i włączyć rozszerzenia maszyny Wirtualnej](../log-analytics/log-analytics-quick-collect-azurevm.md) na każdym komputerze. Możesz skonfigurować [kolekcji różnych źródeł danych](../log-analytics/log-analytics-data-sources.md) aplikacji i [tworzyć widoki](../log-analytics/log-analytics-view-designer.md) do raport o jego codziennych operacji i wydajności. Następnie możesz [tworzyć reguły alertów](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) informujący, po odebraniu zdarzenia określonego błędu. 

Do ciągłego monitorowania kondycji agenta, możesz dodać [rozwiązania do zarządzania agenta kondycji](../operations-management-suite/oms-solution-agenthealth.md). Do dalszego uzyskiwać wgląd w aplikacji, możesz [dodać agenta zależności](../operations-management-suite/operations-management-suite-service-map-configure.md) do maszyn wirtualnych, aby dodać je do [mapy usługi](../operations-management-suite/operations-management-suite-service-map.md). Mapa usług umożliwia odnalezienie krytyczne procesów i identyfikuje połączenia między komputerami z innymi usługami. 

Po awarii zgłoszone można użyć mapy usługi do wykonania przechowanie do identyfikowania maszynach z konkretnego wystąpienia problemu. Następnie można utworzyć [zapytania na danych analizy dzienników](../log-analytics/log-analytics-log-search-new.md) do identyfikowanie problemu w przyszłości. I Utwórz regułę alertu do aktywnego powiadomienia po wykryciu warunku.



## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o:

* [Azure Monitor](https://azure.microsoft.com/services/monitor/) rozpocząć pracę z podstawowej platformy monitorowania metryki i alerty.
* [Usługa Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) Jeśli próbujesz diagnozować problemy w aplikacji sieci web usługi aplikacji.
* [Zaloguj się Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) do analizowania dzienników i zebranych danych monitorowania.
