---
title: Omówienie usługi Azure Monitor | Dokumenty firmy Microsoft
description: Omówienie usług i funkcji Microsoft składających się na pełną strategię monitorowania usług i aplikacji platformy Azure.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: cfdd0beac7d257a424d327df71602b4612c3da3b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79536857"
---
# <a name="azure-monitor-overview"></a>Omówienie usługi Azure Monitor

Usługa Azure Monitor maksymalizuje dostępność i wydajność aplikacji i usług, dostarczając kompleksowe rozwiązanie do zbierania, analizowania i działania na podstawie danych telemetrycznych ze środowisk w chmurze i lokalnie. Pomaga interpretować działanie aplikacji i proaktywnie identyfikuje problemy dotyczące aplikacji i zasobów, od których zależą.

Oto kilka przykładów, co można zrobić za pomocą usługi Azure Monitor:

- Wykrywanie i diagnozowanie problemów między aplikacjami i zależnościami za pomocą [usługi Application Insights](app/app-insights-overview.md).
- Skoreluj problemy z infrastrukturą z [usługą Azure Monitor dla maszyn wirtualnych](insights/vminsights-overview.md) i [usługą Azure Monitor for Containers.](insights/container-insights-overview.md)
- Przejdź do szczegółów danych monitorowania za pomocą [usługi Log Analytics w](log-query/log-query-overview.md) celu rozwiązywania problemów i głębokiej diagnostyki.
- Obsługa operacji na dużą skalę dzięki [inteligentnym alertom](platform/alerts-smartgroups-overview.md) i [zautomatyzowanym działaniom](platform/alerts-action-rules.md).
- Twórz wizualizacje za pomocą [pulpitów nawigacyjnych](learn/tutorial-logs-dashboards.md) i [skoroszytów](app/usage-workbooks.md)platformy Azure .

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Omówienie

Poniższy diagram przedstawia ogólny widok usługi Azure Monitor. W centrum diagramu znajdują się magazyny danych dla metryk i dzienników, które są dwa podstawowe typy użycia danych przez usługę Azure Monitor. Po lewej stronie znajdują się [źródła danych monitorowania,](platform/data-sources.md) które wypełniają te [magazyny danych](platform/data-platform.md). Po prawej stronie znajdują się różne funkcje, które usługa Azure Monitor wykonuje z tymi zebranymi danymi, takie jak analiza, alerty i przesyłanie strumieniowe do systemów zewnętrznych.

![Omówienie usługi Azure Monitor](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Platforma monitorowania danych

Wszystkie dane zebrane przez usługę Azure Monitor są jednym z dwóch podstawowych typów, [metryk i dzienników.](platform/data-platform.md) [Metryki](platform/data-platform-metrics.md) są wartościami liczbowymi, które opisują niektóre aspekty systemu w określonym momencie. Są lekkie i zdolne do obsługi scenariuszy w czasie zbliżonym do rzeczywistego. [Dzienniki](platform/data-platform-logs.md) zawierają różne rodzaje danych zorganizowanych w rekordy z różnymi zestawami właściwości dla każdego typu. Dane telemetryczne, takie jak zdarzenia i ślady są przechowywane jako dzienniki oprócz danych wydajności, dzięki czemu wszystkie mogą być łączone do analizy.

W przypadku wielu zasobów platformy Azure zobaczysz dane zebrane przez usługę Azure Monitor bezpośrednio na ich stronie Przegląd w witrynie Azure portal. Spójrz na przykład na dowolną maszynę wirtualną, a zobaczysz kilka wykresów wyświetlających metryki wydajności. Kliknij dowolny wykres, aby otworzyć dane w [Eksploratorze metryk](platform/metrics-charts.md) w witrynie Azure portal, która umożliwia wykres wartości wielu metryk w czasie.  Wykresy można wyświetlać interaktywnie lub przypinać do pulpitu nawigacyjnego, aby wyświetlić je wraz z innymi wizualizacjami.

![Metryki](media/overview/metrics.png)

Dane dziennika zebrane przez usługę Azure Monitor mogą być analizowane za pomocą [zapytań w](log-query/log-query-overview.md) celu szybkiego pobierania, konsolidowania i analizowania zebranych danych.  Można tworzyć i testować kwerendy przy użyciu [usługi Log Analytics](log-query/portals.md) w witrynie Azure portal, a następnie bezpośrednio analizować dane za pomocą tych narzędzi lub zapisywać zapytania do użycia z wizualizacjami lub [regułami alertów.](visualizations.md) [alert rules](platform/alerts-overview.md)

Usługa Azure Monitor używa wersji [języka zapytań Kusto](/azure/kusto/query/) używanego przez Eksploratora danych platformy Azure, który jest odpowiedni dla prostych zapytań dziennika, ale zawiera również zaawansowane funkcje, takie jak agregacje, sprzężenia i inteligentne analizy. Możesz szybko nauczyć się języka zapytań, korzystając z [wielu lekcji.](log-query/get-started-queries.md)  Specjalne instrukcje udostępniono dla użytkowników, którzy znają już [SQL](log-query/sql-cheatsheet.md) i [Splunk](log-query/splunk-cheatsheet.md).

![Dzienniki](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Jakie dane zbiera usługa Azure Monitor?

Usługa Azure Monitor może zbierać dane z różnych źródeł. Monitorowanie danych aplikacji zachodzi w różnych warstwach — od aplikacji przez system operacyjny i usługi aż do samej platformy. Usługa Azure Monitor zbiera dane z każdej z następujących warstw:

- **Dane monitorowania aplikacji**: Dane dotyczące wydajności i funkcjonalności kodu, który napisałeś, niezależnie od jego platformy.
- **Dane monitorowania systemu operacyjnego gościa:** Dane dotyczące systemu operacyjnego, na którym jest uruchomiona aplikacja. Może się to odbywać na platformie Azure, w innej chmurze lub lokalnie. 
- **Dane monitorowania zasobów platformy Azure:** dane dotyczące działania zasobu platformy Azure.
- **Dane monitorowania subskrypcji platformy Azure:** dane dotyczące obsługi i zarządzania subskrypcją platformy Azure, a także dane dotyczące kondycji i działania samej platformy Azure. 
- **Dane monitorowania dzierżawy platformy Azure:** dane dotyczące działania usług platformy Azure na poziomie dzierżawy, takich jak usługa Azure Active Directory.

Zaraz po utworzeniu subskrypcji platformy Azure i rozpoczęciu dodawania zasobów, takich jak maszyny wirtualne i aplikacje internetowe, usługa Azure Monitor rozpoczyna zbieranie danych.  [Dzienniki aktywności rejestrują,](platform/platform-logs-overview.md) gdy zasoby są tworzone lub modyfikowane. [Metryki](platform/data-platform.md) informują o wydajności zasobu i zasobach, które zużywają. 

Rozszerz gromadzone dane na rzeczywiste działanie zasobów, [włączając diagnostykę](platform/platform-logs-overview.md) i [dodając agenta](platform/agent-windows.md) do zasobów obliczeniowych. Spowoduje to zbieranie danych telemetrycznych dla wewnętrznego działania zasobu i umożliwia skonfigurowanie różnych [źródeł danych](platform/agent-data-sources.md) do zbierania dzienników i metryk z systemu operacyjnego windows i linux gościa. 

Włącz monitorowanie [aplikacji usługi App Services](app/azure-web-apps.md) lub maszyny [wirtualnej i aplikacji zestawu skalowania maszyny wirtualnej,](app/azure-vm-vmss-apps.md)aby umożliwić usługi Application Insights zbieranie szczegółowych informacji o aplikacji, w tym widoków strony, żądań aplikacji i wyjątków. Ponadto sprawdź dostępność aplikacji, konfigurując [test dostępności,](app/monitor-web-app-availability.md) aby symulować ruch użytkowników.

### <a name="custom-sources"></a>Źródła niestandardowe

Usługa Azure Monitor może zbierać dane dziennika od dowolnego klienta REST przy użyciu [interfejsu API modułu zbierającego dane](platform/data-collector-api.md). Dzięki temu można utworzyć niestandardowe scenariusze monitorowania i rozszerzyć monitorowanie zasobów, które nie ujawniają danych telemetrycznych za pośrednictwem innych źródeł.

## <a name="insights"></a>Insights
Monitorowanie danych jest przydatne tylko wtedy, gdy można zwiększyć wgląd w działanie środowiska komputerowego. Usługa Azure Monitor zawiera kilka funkcji i narzędzi, które zapewniają cenny wgląd w aplikacje i inne zasoby, od których zależą. Rozwiązania i [funkcje monitorowania,](insights/solutions.md) takie jak [usługa Application Insights](app/app-insights-overview.md) i usługa Azure Monitor for [containers,](insights/container-insights-overview.md) zapewniają szczegółowy wgląd w różne aspekty aplikacji i określonych usług platformy Azure. 

### <a name="application-insights"></a>Application Insights
[Usługa Application Insights](app/app-insights-overview.md) monitoruje dostępność, wydajność i użycie aplikacji sieci web, niezależnie od tego, czy są one hostowane w chmurze, czy lokalnie. Wykorzystuje zaawansowana platforma analizy danych w usłudze Azure Monitor, aby zapewnić ci dogłębny wgląd w operacje aplikacji i diagnozowanie błędów bez oczekiwania na ich zgłoszenie przez użytkownika. Usługa Application Insights zawiera punkty połączenia z różnymi narzędziami programistycznymi i integruje się z programem Visual Studio w celu obsługi procesów DevOps.

![Wgląd w aplikacje](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów
[Usługa Azure Monitor dla kontenerów](insights/container-insights-overview.md) to funkcja przeznaczona do monitorowania wydajności obciążeń kontenerów wdrożonych w zarządzanych klastrach kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS). Zapewnia wgląd w wydajność, zbierając metryki pamięci i procesora z kontrolerów, węzłów i kontenerów, które są dostępne w usłudze Kubernetes za pośrednictwem interfejsu API metryk. Gromadzone są też dzienniki kontenerów.  Po włączeniu monitorowania z klastrów Kubernetes te metryki i dzienniki są automatycznie zbierane za pośrednictwem konteneryzowanej wersji agenta usługi Log Analytics dla systemu Linux.

![Kondycja kontenera](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych
[Usługa Azure Monitor dla maszyn wirtualnych](insights/vminsights-overview.md) monitoruje maszyny wirtualne platformy Azure na dużą skalę, analizując wydajność i kondycję maszyn wirtualnych z systemem Windows i Linux, w tym ich różne procesy i wzajemnie powiązane zależności od innych zasobów i procesów zewnętrznych. Rozwiązanie obejmuje obsługę monitorowania zależności wydajności i aplikacji dla maszyn wirtualnych hostowanych lokalnie lub innego dostawcy chmury.  


![Szczegółowe informacje o maszynie wirtualnej](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Rozwiązania do monitorowania
[Rozwiązania do monitorowania](insights/solutions.md) w usłudze Azure Monitor to spakowane zestawy logiki, które zapewniają szczegółowe informacje dotyczące określonej aplikacji lub usługi. Obejmują one logikę zbierania danych monitorowania dla aplikacji lub usługi, [zapytania](log-query/log-query-overview.md) do analizowania tych danych i [widoki](../log-analytics/log-analytics-view-designer.md) do wizualizacji. Rozwiązania do monitorowania są [dostępne od firmy Microsoft](insights/solutions-inventory.md) i partnerów w celu monitorowania różnych usług platformy Azure i innych aplikacji.

![Rozwiązania do monitorowania](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Reagowanie na sytuacje krytyczne
Oprócz umożliwienia interaktywnej analizy danych monitorowania, skuteczne rozwiązanie do monitorowania musi być w stanie aktywnie reagować na krytyczne warunki określone w zbieranych danych. Może to być wysyłanie wiadomości tekstowych lub poczty do administratora odpowiedzialnego za zbadanie problemu. Możesz też uruchomić zautomatyzowany proces, który próbuje poprawić warunek błędu.


### <a name="alerts"></a>Alerty
[Alerty w usłudze Azure Monitor](platform/alerts-overview.md) proaktywnie powiadamiają o krytycznych warunkach i potencjalnie próbują podjąć działania naprawcze. Reguły alertów oparte na metrykach zapewniają alerty w czasie zbliżonym do rzeczywistego na podstawie wartości liczbowych, podczas gdy reguły oparte na dziennikach umożliwiają złożoną logikę między danymi z wielu źródeł.

Reguły alertów w usłudze Azure Monitor używają [grup akcji,](platform/action-groups.md)które zawierają unikatowe zestawy adresatów i akcje, które mogą być współużytkowane przez wiele reguł. W zależności od wymagań grupy akcji mogą wykonywać takie czynności, jak używanie zestawów webhook, aby alerty uruchamiały akcje zewnętrzne lub integrowały się z narzędziami ITSM.

![Alerty](media/overview/alerts.png)

### <a name="autoscale"></a>Automatyczne skalowanie
Skalowanie automatyczne umożliwia odpowiednią ilość zasobów uruchomionych do obsługi obciążenia aplikacji. Umożliwia tworzenie reguł, które używają metryk zebranych przez usługę Azure Monitor, aby określić, kiedy automatycznie dodać zasoby do obsługi wzrost obciążenia, a także zaoszczędzić pieniądze, usuwając zasoby, które siedzą bezczynnie. Należy określić minimalną i maksymalną liczbę wystąpień i logikę, kiedy zwiększyć lub zmniejszyć zasoby.

![Automatyczne skalowanie](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Wizualizacja danych monitorowania
[Wizualizacje,](visualizations.md) takie jak wykresy i tabele, są skutecznymi narzędziami do podsumowywania danych monitorowania i prezentowania ich różnym odbiorcom. Usługa Azure Monitor ma własne funkcje do wizualizacji danych monitorowania i wykorzystuje inne usługi platformy Azure do publikowania ich w różnych grupach odbiorców.

### <a name="dashboards"></a>Pulpity nawigacyjne
[Pulpity nawigacyjne platformy Azure](../azure-portal/azure-portal-dashboards.md) umożliwiają łączenie różnych rodzajów danych, w tym metryk i dzienników, w jednym okienku w [witrynie Azure portal.](https://portal.azure.com) Opcjonalnie można udostępnić pulpit nawigacyjny innym użytkownikom platformy Azure. Elementy w usłudze Azure Monitor można dodać do pulpitu nawigacyjnego platformy Azure oprócz danych wyjściowych dowolnej kwerendy dziennika lub wykresu metryk. Na przykład można utworzyć pulpit nawigacyjny, który łączy kafelki, które pokazują wykres metryk, tabelę dzienników aktywności, wykres użycia z usługi Application Insights i dane wyjściowe kwerendy dziennika.

![Pulpit nawigacyjny](media/overview/dashboard.png)

### <a name="views"></a>Widoki
[Widoki](../log-analytics/log-analytics-view-designer.md) wizualnie prezentują dane dziennika w usłudze Azure Monitor.  Każdy widok zawiera pojedynczy kafelek, który przechodzi do szczegółów do kombinacji wizualizacji, takich jak wykresy słupkowe i liniowe, oprócz list podsumowujących dane krytyczne.  Rozwiązania do monitorowania obejmują widoki, które podsumowują dane dla określonej aplikacji i można utworzyć własne widoki do prezentowania danych z dowolnej kwerendy dziennika. Podobnie jak inne elementy w usłudze Azure Monitor widoki można dodawać do pulpitów nawigacyjnych platformy Azure.

![Widok](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Usługa Power BI](https://powerbi.microsoft.com) to usługa analizy biznesowej, która zapewnia interaktywne wizualizacje w różnych źródłach danych i jest skutecznym sposobem udostępniania danych innym osobom w organizacji i poza nią. Usługę Power BI można skonfigurować tak, aby [automatycznie importować dane dziennika z usługi Azure Monitor w](../log-analytics/log-analytics-powerbi.md) celu skorzystania z tych dodatkowych wizualizacji.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integracja i eksportowanie danych
Często będziesz mieć obowiązek integracji usługi Azure Monitor z innymi systemami i tworzenia niestandardowych rozwiązań, które używają danych monitorowania. Inne usługi platformy Azure współpracują z usługą Azure Monitor, aby zapewnić tę integrację.

### <a name="event-hub"></a>Centrum zdarzeń
[Usługa Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) to platforma przesyłania strumieniowego i usługa pozyskiwania zdarzeń, która może przekształcać i przechowywać dane przy użyciu dowolnego dostawcy analizy w czasie rzeczywistym lub kart przetwarzania wsadowego/magazynu. Usługi Event Hubs umożliwia [przesyłanie strumieniowe danych usługi Azure Monitor](platform/stream-monitoring-data-event-hubs.md) do partnerskiego narzędzia SIEM i monitorowania.


### <a name="logic-apps"></a>Logic Apps
[Logic Apps](https://azure.microsoft.com/services/logic-apps) to usługa, która umożliwia automatyzację zadań i procesów biznesowych przy użyciu przepływów pracy, które integrują się z różnymi systemami i usługami. Dostępne są działania, które odczytują i zapisują metryki i dzienniki w usłudze Azure Monitor, która umożliwia tworzenie przepływów pracy integrujących się z różnymi innymi systemami.


### <a name="api"></a>interfejs API
Wiele interfejsów API są dostępne do odczytu i zapisu metryki i dzienniki do i z usługi Azure Monitor oprócz uzyskiwania dostępu do wygenerowanych alertów. Można również skonfigurować i pobrać alerty. Zapewnia to zasadniczo nieograniczone możliwości tworzenia niestandardowych rozwiązań, które integrują się z usługą Azure Monitor.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o usługach:

* [Metryki i dzienniki](platform/data-platform.md) danych zebranych przez usługę Azure Monitor.
* [Źródła danych](platform/data-sources.md) dotyczące sposobu, w jaki różne składniki aplikacji wysyłają dane telemetryczne.
* [Rejestrowanie zapytań](log-query/log-query-overview.md) do analizowania zebranych danych.
* [Najważniejsze wskazówki](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) dotyczące monitorowania aplikacji i usług w chmurze.
