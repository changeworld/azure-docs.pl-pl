---
title: Omówienie usługi Azure Monitor | Dokumentacja firmy Microsoft
description: Omówienie usług i funkcji Microsoft składających się na pełną strategię monitorowania usług i aplikacji platformy Azure.
author: bwren
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: bwren
ms.openlocfilehash: 0485f8e3b377ce94ec23a4a1a94eb7e189b0232b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784963"
---
# <a name="azure-monitor-overview"></a>Omówienie usługi Azure Monitor

Usługa Azure Monitor maksymalizuje dostępność i wydajność aplikacji, zapewniając kompleksowe rozwiązanie umożliwiające obsługę zbieranie, analizowanie i przetwarzanie danych telemetrycznych z chmury i środowiskach lokalnych. Pomaga interpretować działanie aplikacji i proaktywnie identyfikuje problemy dotyczące aplikacji i zasobów, od których zależą.

> [!VIDEO https://www.youtube.com/embed/_hGff5bVtkM]

## <a name="overview"></a>Omówienie
Poniższy diagram zawiera widok wysokiego poziomu usługi Azure Monitor. W środku diagramu są magazynów danych dla metryki i dzienniki, które są dwa podstawowe typy danych używany przez usługi Azure Monitor. Po lewej stronie są [źródła danych monitorowania](platform/data-sources.md) , wypełnić te [magazyny danych](platform/data-platform.md). Po prawej stronie są różne funkcje, które usługi Azure Monitor wykonuje się za pomocą tego zebranych danych, takich jak analiza, alertów i przesyłania strumieniowego z systemami zewnętrznymi.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

![Omówienie usługi Azure Monitor](media/overview/overview.png)


## <a name="monitoring-data-platform"></a>Platforma danych monitorowania
Wszystkie dane zebrane przez usługi Azure Monitor pasuje do jednej z dwóch typów podstawowych, [metryk i dzienników](platform/data-platform.md). [Metryki](platform/data-platform-metrics.md) są wartości liczbowe, które opisują niektóre aspekty systemu w określonym punkcie w czasie. Są one uproszczone i zdolność do obsługi scenariuszy w czasie rzeczywistym w pobliżu. [Dzienniki](platform/data-platform-logs.md) zawierają różne rodzaje danych zorganizowanych w rekordy z różnymi zestawami właściwości dla każdego typu. Dane telemetryczne, np. zdarzeniami i śladami są przechowywane jako dzienniki dodatkowo na dane wydajności, aby wszystkie można zbudować do analizy.

Dla wielu zasobów platformy Azure zostaną wyświetlone dane zebrane przez usługi Azure Monitor po prawej stronie na ich stronie Przegląd w witrynie Azure portal. Mają się z dowolną maszyną wirtualną, na przykład, a zobaczysz kilka wykresów, wyświetlanie metryk wydajności. Kliknij dowolne wykresy, aby otworzyć je w [Eksploratora metryk](platform/metrics-charts.md) w witrynie Azure portal, co pozwala na wykresie wartości wielu metryk wraz z upływem czasu.  Można wyświetlać wykresy interakcyjnie lub przypiąć je do pulpitu nawigacyjnego, aby wyświetlić je z innymi wizualizacjami.

![Metryki](media/overview/metrics.png)

Dane dzienników zbieranych przez usługi Azure Monitor mogą być analizowane za pomocą [zapytania](log-query/log-query-overview.md) do szybkiego pobierania, konsolidacji i analizowania zebranych danych.  Można tworzyć i testować przy użyciu [usługi Log Analytics](log-query/portals.md) w witrynie Azure portal, a następnie bezpośrednio analizować dane za pomocą tych narzędzi lub zapisać zapytania do użycia z [wizualizacje](visualizations.md) lub [alertu reguły](platform/alerts-overview.md).

Usługa Azure Monitor korzysta z wersji [język zapytania Kusto](/azure/kusto/query/) wykorzystywane przez Eksploratora danych platformy Azure, który jest odpowiedni dla prostego dziennika zapytań, ale oferuje także zaawansowane funkcje, takie jak agregacje, sprzężenia i analiza inteligentna. Możesz szybko dowiedzieć się przy użyciu języka zapytań [wielu lekcje](log-query/get-started-queries.md).  Specjalne instrukcje udostępniono dla użytkowników, którzy znają już [SQL](log-query/sql-cheatsheet.md) i [Splunk](log-query/splunk-cheatsheet.md).

![Dzienniki](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Jakie dane są zbierane w usłudze Azure Monitor
Usługa Azure Monitor umożliwia zbieranie danych z różnych źródeł. Można traktować danych monitorowania dla aplikacji w warstwach — od aplikacji, dowolnego systemu operacyjnego i usług, który opiera się na dół samą platformę. Usługa Azure Monitor zbiera dane z każdej z następujących warstwach:

- **Monitorowanie danych aplikacji**: Dane dotyczące wydajności i funkcjonalności, kodu, które zostały napisane, niezależnie od platformy.
- **Dane monitorowania systemu operacyjnego gościa**: Dane dotyczące systemu operacyjnego, na którym aplikacja jest uruchomiona. Może to być uruchomiony w platformie Azure, innej chmurze lub lokalnie. 
- **Dane monitorowania zasobów platformy Azure**: Dane dotyczące operacji zasobu platformy Azure.
- **Subskrypcja platformy Azure, danych monitorowania**: Dane dotyczące operacji i zarządzania subskrypcją platformy Azure, a także dane dotyczące kondycji i działanie systemu Azure sam. 
- **Dzierżawa usługi Azure monitoring danych**: Dane o poziomie dzierżawy usług platformy Azure, takich jak Azure Active Directory.

Zaraz po utworzeniu subskrypcji platformy Azure i zacznij dodawać zasoby, takie jak maszyny wirtualne i aplikacje sieci web usługi Azure Monitor rozpoczyna zbieranie danych.  [Dzienniki aktywności](platform/activity-logs-overview.md) rejestrowania po utworzeniu lub zmodyfikowaniu zasobów. [Metryki](platform/data-platform.md) informujące, jaka jest wydajność zasobów i zasoby, które go używa. 

Rozszerzanie danych, Trwa zbieranie danych do bieżącej operacji zasobów przez [włączenie diagnostyki](platform/diagnostic-logs-overview.md) i [dodanie agenta](platform/agent-windows.md) zasobów obliczeniowych. Spowoduje to zbieranie danych telemetrycznych dla operacji wewnętrznej zasobu i umożliwiają skonfigurowanie różnych [źródeł danych](platform/agent-data-sources.md) do gromadzenia dzienników i metryk w systemie operacyjnym gościa Windows i Linux. 

[Dodawanie pakietu instrumentacji aplikacji](app/azure-web-apps.md), aby włączyć usługę Application Insights zbierać szczegółowe informacje na temat aplikacji w tym wyświetleń stron, aplikacji żądań i wyjątków. Dodatkowo sprawdź dostępność aplikacji, konfigurując [testu dostępności](app/monitor-web-app-availability.md) aby symulować ruch użytkownika.

### <a name="custom-sources"></a>Niestandardowe źródła
Usługa Azure Monitor może zbierać dane dzienników z dowolnego klienta REST przy użyciu [interfejsu API modułu zbierającego dane](platform/data-collector-api.md). Dzięki temu można tworzyć niestandardowe scenariusze monitorowania i rozszerzania monitorowania do zasobów, które nie udostępniają danych telemetrycznych za pośrednictwem innych źródeł.



## <a name="insights"></a>Insights
Dane monitorowania jest przydatna, jeśli można zwiększyć wgląd w działanie środowiska obliczeniowego. Usługa Azure Monitor obejmuje kilka funkcji i narzędzi, które zapewniają cenne informacje dotyczące aplikacji i innych zasobów, których one zależą. [Monitorowanie rozwiązań](insights/solutions.md) i funkcje, takie jak [usługi Application Insights](app/app-insights-overview.md) i [usługi Azure Monitor dla kontenerów](insights/container-insights-overview.md) umożliwiają uzyskiwanie szczegółowych do różnych aspektów aplikacji i określone platformy Azure usługi. 

### <a name="application-insights"></a>Application Insights
[Usługa Application Insights](app/app-insights-overview.md) monitoruje dostępność, wydajność i użycie aplikacji sieci web, czy są one hostowane w chmurze lub lokalnie. Wykorzystuje platformę analiz zaawansowanych danych w usłudze Azure Monitor, aby udostępnić niepowtarzalna okazja operacji Twojej aplikacji i diagnozować błędy bez oczekiwania na zgłoszenie ich przez użytkownika. Usługa Application Insights obejmuje punkty połączenia z szeroką gamą narzędzi programistycznych i integruje się z programem Visual Studio w celu lepszego wspierania procesów metodyki DevOps.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów
[Usługa Azure Monitor dla kontenerów](insights/container-insights-overview.md) to funkcja przeznaczona do monitorowania wydajności obciążeń kontenerów wdrożonych na zarządzane klastry Kubernetes hostowanych na platformie Azure Kubernetes Service (AKS). Zapewnia widoczność wydajności na zbieranie pamięci i procesora metryk z kontrolerów, węzły i kontenerów, które są dostępne w usłudze Kubernetes za pomocą interfejsu API metryki. Gromadzone są też dzienniki kontenerów.  Po włączeniu monitorowania z klastrów Kubernetes, te metryki i dzienniki są automatycznie zbierane dla Ciebie za pośrednictwem konteneryzowanych wersję agenta usługi Log Analytics dla systemu Linux.

![Kondycji kontenera](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych
[Usługa Azure Monitor dla maszyn wirtualnych](insights/vminsights-overview.md) monitoruje usługi Azure virtual machines (VM) na dużą skalę, analizując wydajności i kondycji Windows i maszyn wirtualnych systemu Linux, łącznie z różnych procesów i wzajemnie połączonych zależności od innych zasobów i zewnętrzne procesy. Rozwiązanie obejmuje obsługę monitorowania wydajności i zależności aplikacji w przypadku maszyn wirtualnych hostowanych lokalnie lub innego dostawcy chmury.  


![VM Insights](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Rozwiązania do monitorowania
[Monitorowanie rozwiązań](insights/solutions.md) w usłudze Azure Monitor są spakowane zestawy logiki ze szczegółowymi informacjami dla określonej aplikacji lub usługi. Obejmują one logikę do zbierania danych monitorowania dla aplikacji lub usługi, [zapytania](log-query/log-query-overview.md) można analizować te dane i [widoków](../log-analytics/log-analytics-view-designer.md) wizualizacji. Rozwiązania do monitorowania są [udostępnianych przez firmę Microsoft](insights/solutions-inventory.md) i jej partnerów, aby zapewnić monitorowanie dla różnych usług platformy Azure i innych aplikacji.

![Rozwiązania do monitorowania](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Reagowanie w sytuacjach krytycznych
Oprócz umożliwienia interakcyjne analizowanie danych monitorowania, to efektywne rozwiązanie monitorowania musi być w stanie aktywnie reagować na krytyczne warunki określone w danych, które są zbierane. To może to być wysłanie tekst lub wiadomości e-mail do administratora odpowiedzialnego za badanie problemu. Lub można uruchomić zautomatyzowany proces, który próbuje rozwiązać warunek błędu.


### <a name="alerts"></a>Alerty
[Alerty w usłudze Azure Monitor](platform/alerts-overview.md) proaktywnego powiadamiania o krytycznych warunków i potencjalnie próba akcje naprawcze. Podaj reguły alertów na podstawie metryk niemal w czasie rzeczywistym alerty oparte na wartości liczbowe, podczas gdy zasady oparte na dziennikach przesłanie do złożonej logiki przez dane z wielu źródeł.

Reguły usługi Azure Monitor używany alertów [grup akcji](platform/action-groups.md), które zawierają unikatowych zestawów odbiorców i akcje, które mogą być współużytkowane przez wiele reguł. Zgodnie z wymaganiami, grupy akcji można wykonywać takie zadania jak przy użyciu elementów webhook, mają alerty start działań zewnętrznych lub integracji z narzędziami ITSM.

![Alerty](media/overview/alerts.png)

### <a name="autoscale"></a>Automatyczne skalowanie
Automatyczne skalowanie pozwala mieć odpowiednią ilość zasobów do obsługi obciążenia w swojej aplikacji. Umożliwia tworzenie reguł na podstawie metryki zebrane przez usługi Azure Monitor do określenia, kiedy do automatycznego dodawania zasobów do obsługi zwiększenia obciążenia, a także Oszczędzaj pieniądze, usuwając zasoby, które są obecne bezczynności. Możesz określić minimalną i maksymalną liczbę wystąpień i logikę kiedy zwiększanie lub zmniejszanie zasobów.

![Automatyczne skalowanie](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Wizualizowanie danych monitorowania
[Wizualizacje](visualizations.md) takich jak wykresy i tabele są skutecznymi narzędziami dla podsumowania danych monitorowania i zaprezentowanie jej do różnych grup odbiorców. Usługa Azure Monitor ma swoje własne funkcje do wizualizacji danych monitorowania i korzysta z innymi usługami platformy Azure, umożliwiające publikowanie go do różnych grup odbiorców.

### <a name="dashboards"></a>Pulpity nawigacyjne
[Pulpitów nawigacyjnych platformy Azure](../azure-portal/azure-portal-dashboards.md) pozwalają połączyć różne rodzaje danych, w tym dzienniki i metryki w jednym okienku w [witryny Azure portal](https://portal.azure.com). Pulpit nawigacyjny może opcjonalnie udostępniać innym użytkownikom usługi Azure. Do pulpitu nawigacyjnego platformy Azure oprócz danych wyjściowych dowolnego wykresu zapytania lub metryk dzienników można dodać elementów w usłudze Azure Monitor. Na przykład można utworzyć pulpit nawigacyjny, który łączy Kafelki przedstawiające wykresu metryki, tabeli dzienników aktywności, wykres użycia za pomocą usługi Application Insights i wyniki kwerendy dziennika.

![Pulpit nawigacyjny](media/overview/dashboard.png)

### <a name="views"></a>Widoki
[Widoki](../log-analytics/log-analytics-view-designer.md) prezentują wizualnie dane dzienników w usłudze Azure Monitor.  Każdy widok zawiera pojedynczy fragment, który przejście do kombinację wizualizacji, takich jak pasek i liniowy oprócz listy podsumowujące kluczowych danych.  Rozwiązania do monitorowania zawierają widoki, które podsumowują dane dla konkretnej aplikacji, i mogą tworzyć własne widoki, aby prezentować dane pochodzące z dowolnego zapytania dziennika. Podobnie jak inne elementy w usłudze Azure Monitor można dodać widoków do pulpitów nawigacyjnych platformy Azure.

![Widok](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[Usługa Power BI](https://powerbi.microsoft.com) to usługa analizy biznesowej, która zapewnia Interaktywne wizualizacje na podstawie wielu źródeł danych i jest skutecznym sposobem udostępniania danych innym osobom w organizacji i poza nią. Można skonfigurować usługi Power BI [automatycznie zaimportować dane dzienników z usługi Azure Monitor](../log-analytics/log-analytics-powerbi.md) może korzystać z tych dodatkowych wizualizacji.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrowanie i eksportowanie danych
Często będziesz mieć wymagań integracji usługi Azure Monitor z innymi systemami i tworzyć niestandardowe rozwiązania, które używają danych monitorowania. Innych usług platformy Azure działają z usługą Azure Monitor w celu zapewnienia tej integracji.

### <a name="event-hub"></a>Centrum zdarzeń
[Usługa Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) to przesyłania strumieniowego platformy i zdarzeń pozyskiwania usługa, która można przetworzyć i zapisać dane przy użyciu dowolnego dostawcy Analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Usługa Event Hubs do [przesyłanie strumieniowe danych usługi Azure Monitor](platform/stream-monitoring-data-event-hubs.md) do partnerów SIEM i narzędzi do monitorowania.


### <a name="logic-apps"></a>Logic Apps
[Usługa Logic Apps](https://azure.microsoft.com/services/logic-apps) to usługa, która pozwala na zautomatyzowanie zadań i procesów biznesowych przy użyciu przepływów pracy, które integrują się z różnych systemów i usług. Działania są dostępne, Odczyt i zapis metryk i dzienników w usłudze Azure Monitor, który umożliwia tworzenie przepływów pracy integracji z wieloma innymi systemami.


### <a name="api"></a>Interfejs API
Wiele interfejsów API dostępnych do odczytu i zapisu metryk i dzienników z usługi Azure Monitor i oprócz uzyskiwania dostępu do alerty generowane przez monitory. Można również skonfigurować i pobrać alertów. Zapewnia to zasadniczo nieograniczone możliwości do tworzenia niestandardowych rozwiązań, które integrują się z usługą Azure Monitor.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o usługach:

* [Metryki i dzienniki](platform/data-platform.md) dla danych zbieranych przez usługi Azure Monitor.
* [Źródła danych](platform/data-sources.md) dla jak wysyłanie danych telemetrycznych w różnych składników aplikacji.
* [Zaloguj się zapytania](log-query/log-query-overview.md) do analizowania zebranych danych.