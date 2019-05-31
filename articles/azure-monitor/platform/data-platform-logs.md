---
title: Dzienniki w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule opisano dzienników w usłudze Azure Monitor, które są używane do zaawansowanej analizy danych monitorowania.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 0203/26/2019
ms.author: bwren
ms.openlocfilehash: 897f2eef0a52838d6190cb85a6a7f4492250935b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244852"
---
# <a name="logs-in-azure-monitor"></a>Dzienniki w usłudze Azure Monitor

> [!NOTE]
> Wszystkie dane zebrane przez usługi Azure Monitor pasuje do jednej z dwóch typów podstawowych, metryk i dzienników. W tym artykule opisano dzienniki. Zapoznaj się [metryk w usłudze Azure Monitor](data-platform-metrics.md) szczegółowy opis metryki i [dane monitorowania zbierane przez usługi Azure Monitor](data-platform.md) porównanie dwóch.

Dzienniki w usłudze Azure Monitor są szczególnie przydatne w przypadku wykonywania złożonych analiz dla danych z różnych źródeł. W tym artykule opisano, jak dzienniki są skonstruowane w usłudze Azure Monitor, co można zrobić z danymi i identyfikuje różnych źródłach danych przechowujących dane w dziennikach.

> [!NOTE]
> Jest ważne rozróżnić dzienników monitora platformy Azure i źródła danych dziennika na platformie Azure. Na przykład zdarzeń na poziomie subskrypcji na platformie Azure są zapisywane w [dziennika aktywności](activity-logs-overview.md) można wyświetlić z menu usługi Azure Monitor. Większość zasobów zapisze informacje operacyjne, aby [dziennik diagnostyczny](diagnostic-logs-overview.md) który może przekazywać do innej lokalizacji. Dzienniki platformy Azure Monitor to platforma danych dziennika, która gromadzi Dzienniki aktywności i dzienników diagnostycznych oraz innych danych monitorowania w celu zapewnienia szczegółowej analizy cały zestaw zasobów.

## <a name="what-are-azure-monitor-logs"></a>Co to są usługi Azure Monitor dzienniki?

Dzienniki w usłudze Azure Monitor zawierają różne rodzaje danych zorganizowanych w rekordy z różnymi zestawami właściwości dla każdego typu. Dzienniki mogą zawierać wartości liczbowe, takie jak metryki monitorowania platformy Azure, ale zwykle zawierają dane tekstowe za pomocą szczegółowe opisy. Dodatkowo różnią się od danych metryk, różnią się w ich struktury, a często nie są zbierane w regularnych odstępach czasu. Dane telemetryczne, takie jak zdarzenia i dane śledzenia są przechowywane dzienniki monitora platformy Azure oprócz dane dotyczące wydajności tak, że jest to możliwe wszystkie kombinovat s do analizy.

Typowe typ wpisu dziennika jest zdarzenie, które są zbierane sporadycznie. Zdarzenia są tworzone przez aplikację lub usługę i zazwyczaj zawierają informacje wystarczające do zapewnienia pełnego kontekstu własnych. Na przykład zdarzenie może wskazywać, że określony zasób został utworzony lub zmodyfikowany nowego hosta uruchomionego w odpowiedzi zwiększonego ruchu lub wykryto błąd w aplikacji.

 Ponieważ format danych może się różnić w aplikacji można utworzyć niestandardowe dzienniki przy użyciu struktury, które potrzebują. Dane metryk nawet mogą być przechowywane w dziennikach, aby połączyć je z innych danych monitorowania umożliwia analizę trendów i innych danych.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Co można zrobić za pomocą usługi Azure Monitor dzienników?
W poniższej tabeli wymieniono różne sposoby dzienników można używać w usłudze Azure Monitor.


|  |  |
|:---|:---|
| Analiza | Użyj [usługi Log Analytics](../log-query/get-started-portal.md) w witrynie Azure portal, aby zapisać [rejestrowania zapytań](../log-query/log-query-overview.md) i interakcyjnie analizować dane dzienników przy użyciu zaawansowany aparat analizy Eksploratora danych.<br>Użyj [Konsola analizy usługi Application Insights](../app/analytics.md) w witrynie Azure portal do zapisywania dziennika zapytań i interakcyjnie analizować dane dzienników z usługi Application Insights. |
| Wizualizuj | Wyniki zapytania renderowane jako tabele lub wykresy, aby przypiąć [pulpitu nawigacyjnego platformy Azure](../../azure-portal/azure-portal-dashboards.md).<br>Tworzenie [skoroszytu](../app/usage-workbooks.md) połączyć z wieloma zestawami danych w interaktywnych raportów. <br>Eksportowanie wyników kwerendy, aby [usługi Power BI](powerbi.md) różne wizualizacje i udostępniać użytkownikom spoza platformy Azure.<br>Eksportowanie wyników kwerendy, aby [Grafana](grafana-plugin.md) wykorzystać jej dashboarding i łączyć z innymi źródłami danych.|
| Alerty | Konfigurowanie [reguł alertów dzienników](alerts-log.md) która wyśle powiadomienie, lub trwa [zautomatyzowanej akcji](action-groups.md) po wyniki zapytania pasują do konkretnego wyniku.<br>Konfigurowanie [regułę alertu metryki](alerts-metric-logs.md) na niektórych dzienników danych dziennika, wyodrębnić jako metryki. |
| Pobieranie | Dostęp do dziennika wyników zapytania z wiersza polecenia przy użyciu [wiersza polecenia platformy Azure](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Dostęp do dziennika wyników zapytania z wiersza polecenia przy użyciu [poleceń cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Dostęp do dziennika wyników zapytania z niestandardowych aplikacji przy użyciu [interfejsu API REST](https://dev.loganalytics.io/). |
| Eksportowanie | Tworzenie przepływu pracy można pobrać danych dziennika i skopiuj go do lokalizacji zewnętrznej za pomocą [Logic Apps](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>W jaki sposób dane w dziennikach monitora platformy Azure, ze strukturą?
Dane zbierane przez usługi Azure Monitor dzienniki są przechowywane w [obszaru roboczego usługi Log Analytics](../platform/manage-access.md). Możesz [utworzyć wiele obszarów roboczych](manage-access.md#determine-the-number-of-workspaces-you-need) w ramach subskrypcji, aby zarządzać różnymi zestawami danych dziennika. Każdy obszar roboczy zawiera wiele tabel, że każdy przechowywania danych z określonego źródła. Gdy wszystkie tabele udostępnianie [niektóre typowe właściwości](log-standard-properties.md), każdy z nich ma unikatowe zbiór właściwości, w zależności od rodzaju danych, które są przechowywane. Nowy obszar roboczy będzie mieć standardowy zestaw tabel i więcej tabel, zostanie on dodany przez innego rozwiązania do monitorowania i innych usług, które zapisać do obszaru roboczego.

Dane dzienników z usługi Application Insights używa tego samego aparatu usługi Log Analytics jako obszarów roboczych, ale były przechowywane osobno dla każdej monitorowanej aplikacji. Każda aplikacja ma standardowy zestaw tabel do przechowywania danych, takich jak żądania aplikacji, wyjątki i wyświetlenia stron.

Dziennik zapytań będzie albo użyj danych z obszaru roboczego usługi Log Analytics lub aplikacji usługa Application Insights. Możesz użyć [zapytania obejmujące wiele zasobów](../log-query/cross-workspace-query.md) do przeanalizowania danych aplikacji wraz z innymi danymi dziennika lub tworzyć zapytania, łącznie z wieloma obszarami roboczymi lub aplikacji.

![Obszary robocze](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Rejestrowanie zapytań
Dane w dziennikach monitora platformy Azure są pobierane przy użyciu [zapytanie dziennika](../log-query/log-query-overview.md) napisane przy użyciu [język zapytania Kusto](../log-query/get-started-queries.md), co pozwala na szybkie pobierania, konsolidacji i analizowania zebranych danych. Użyj [usługi Log Analytics](../log-query/portals.md) pisania i testowania zapytań dzienników w witrynie Azure portal. Umożliwia ona interaktywnej pracy z wynikami, lub Przypnij je do pulpitu nawigacyjnego, aby wyświetlić je z innymi wizualizacjami.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Otwórz [usługi Log Analytics z usługi Application Insights](../app/analytics.md) do analizowania danych usługi Application Insights.

![Application Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

Możesz również pobrać dane dziennika, używając [interfejsu API usługi Log Analytics](https://dev.loganalytics.io/documentation/overview) i [interfejsu API REST usługi Application Insights](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Źródła dzienników usługi Azure Monitor
Usługa Azure Monitor może zbierać dane dzienników z różnych źródeł, zarówno w obrębie platformy Azure i z zasobami lokalnymi. W poniższej tabeli wymieniono różnych źródeł danych pochodzących z różnych zasobów, które zapisu danych do usługi Azure Monitor dzienników. Każda ma łącze do szczegółów na każdej wymaganej konfiguracji.

### <a name="azure-tenant-and-subscription"></a>Dzierżawy platformy Azure i subskrypcji

| Dane | Opis |
|:---|:---|
| Dzienniki inspekcji w usłudze Azure Active Directory | Skonfigurować za pomocą ustawień diagnostycznych dla każdego katalogu. Zobacz [dzienniki Integracja usługi Azure AD przy użyciu dzienników usługi Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Dzienniki aktywności | Domyślnie przechowywane oddzielnie i mogą służyć do niemal alerty w czasie rzeczywistym. Zainstaluj dziennika aktywności rozwiązania do analizy do zapisu do obszaru roboczego usługi Log Analytics. Zobacz [zbierać i analizować Dzienniki aktywności platformy Azure w usłudze Log Analytics](activity-log-collect.md). |

### <a name="azure-resources"></a>Zasoby platformy Azure

| Dane | Opis |
|:---|:---|
| Diagnostyki zasobów | Konfigurowanie ustawień diagnostycznych do zapisywania danych diagnostycznych, w tym metryki do obszaru roboczego usługi Log Analytics. Zobacz [Stream dzienniki diagnostyczne platformy Azure do usługi Log Analytics](diagnostic-logs-stream-log-store.md). |
| Rozwiązania do monitorowania | Rozwiązania do monitorowania zapisywać danych zbieranych do swojego obszaru roboczego usługi Log Analytics. Zobacz [szczegóły zbierania danych dla rozwiązań do zarządzania na platformie Azure](../insights/solutions-inventory.md) listę rozwiązań. Zobacz [rozwiązania w usłudze Azure Monitor do monitorowania](../insights/solutions.md) szczegółowe informacje o instalowaniu i używaniu rozwiązania. |
| Metryki | Wysyłanie metryk platformy dla zasobów usługi Azure Monitor do obszaru roboczego usługi Log Analytics do przechowywania danych dziennika przez dłuższy czas i przeprowadzania analizy złożonych z innymi typami danych przy użyciu [język zapytania Kusto](/azure/kusto/query/). Zobacz [Stream dzienniki diagnostyczne platformy Azure do usługi Log Analytics](diagnostic-logs-stream-log-store.md). |
| Azure Table Storage | Zbieranie danych z usługi Azure storage, gdzie zapisać niektórych zasobów platformy Azure danych monitorowania. Zobacz [użycia w usłudze Azure blob storage, usługi IIS i platformą Azure table Storage dla zdarzeń za pomocą usługi Log Analytics](azure-storage-iis-table.md). |

### <a name="virtual-machines"></a>Maszyny wirtualne

| Dane | Opis |
|:---|:---|
|  Źródła danych agentów | Źródła danych zebranych z [Windows](agent-windows.md) i [Linux](../learn/quick-collect-linux-computer.md) agenci obejmują zdarzenia, dane dotyczące wydajności i dzienników niestandardowych. Zobacz [agenta źródeł danych w usłudze Azure Monitor](data-sources.md) listę źródeł danych i szczegółowe informacje na temat konfiguracji. |
| Rozwiązania do monitorowania | Rozwiązania do monitorowania zapisywać danych zbieranych z agentów do swojego obszaru roboczego usługi Log Analytics. Zobacz [szczegóły zbierania danych dla rozwiązań do zarządzania na platformie Azure](../insights/solutions-inventory.md) listę rozwiązań. Zobacz [rozwiązania w usłudze Azure Monitor do monitorowania](../insights/solutions.md) szczegółowe informacje o instalowaniu i używaniu rozwiązania. |
| System Center Operations Manager | Połącz grupę zarządzania programu Operations Manager do usługi Azure Monitor do zbierania danych zdarzeń i wydajności z agentów dla środowiska lokalnego do dzienników. Zobacz [łączenie programu Operations Manager do usługi Log Analytics](om-agents.md) szczegółowe informacje na temat tej konfiguracji. |


### <a name="applications"></a>Aplikacje

| Dane | Opis |
|:---|:---|
| Żądań i wyjątków | Szczegółowe dane dotyczące aplikacji żądań i wyjątków są w _żądań_, _pageViews_, i _wyjątki_ tabel. Wywołania [składników zewnętrznych](../app/asp-net-dependencies.md) znajdują się w _zależności_ tabeli. |
| Użycie i wydajność | Wydajność aplikacji jest dostępna w _żądań_, _browserTimings_ i _liczniki wydajności_ tabel. Dane dotyczące [metryki niestandardowe](../app/api-custom-events-metrics.md#trackevent) znajduje się w _customMetrics_ tabeli.|
| Dane śledzenia | Powstały na skutek [rozproszonego śledzenia](../app/distributed-tracing.md) są przechowywane w _ślady_ tabeli. |
| Testy dostępności | Podsumowanie danych z [testy dostępności](../app/monitor-web-app-availability.md) są przechowywane w _availabilityResults_ tabeli. Szczegółowe dane z tych testów są oddzielnie i uzyskać dostęp z usługi Application Insights w witrynie Azure portal. |

### <a name="insights"></a>Insights

| Dane | Opis |
|:---|:---|
| Usługa Azure Monitor dla kontenerów | Dane spisu i wydajności zebrane przez [usługi Azure Monitor dla kontenerów](../insights/container-insights-overview.md). Zobacz [szczegóły zbierania danych kontenera](../insights/container-insights-log-search.md#container-records) listę tabel. |
| Usługa Azure Monitor dla maszyn wirtualnych | Dane mapy i wydajności zebrane przez [usługi Azure Monitor dla maszyn wirtualnych](../insights/vminsights-overview.md). Zobacz [sposób wykonywania zapytań dzienników z usługi Azure Monitor dla maszyn wirtualnych](../insights/vminsights-log-search.md) szczegółowe informacje dotyczące zapytań na tych danych. |

### <a name="custom"></a>Niestandardowy 

| Dane | Opis |
|:---|:---|
| Interfejs API REST | Zapisywanie danych do obszaru roboczego usługi Log Analytics z dowolnego klienta REST. Zobacz [wysyłania dzienników danych do usługi Azure Monitor przy użyciu interfejsu API modułu zbierającego dane HTTP](data-collector-api.md) Aby uzyskać szczegółowe informacje.
| Aplikacja logiki | Zapis żadnych danych do obszaru roboczego usługi Log Analytics z przepływu pracy aplikacji logiki z **Azure Log Analytics Data Collector** akcji. |

### <a name="security"></a>Bezpieczeństwo

| Dane | Opis |
|:---|:---|
| Azure Security Center | [Usługa Azure Security Center](/azure/security-center/) przechowuje dane, które są zbierane w obszarze roboczym usługi Log Analytics, gdzie mogą być analizowane za pomocą innych danych dziennika. Zobacz [zbierania danych w usłudze Azure Security Center](../../security-center/security-center-enable-data-collection.md) szczegółowe informacje dotyczące konfiguracji obszaru roboczego. |
| Usługa Azure Sentinel | [Azure wartownik](/azure/sentinel/) przechowuje dane ze źródeł danych do obszaru roboczego usługi Log Analytics. Zobacz [połączyć źródeł danych](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [platforma danych usługi Azure Monitor](data-platform.md).
- Dowiedz się więcej o [metryk w usłudze Azure Monitor](data-platform-metrics.md).
- Dowiedz się więcej o [dostępne dane monitorowania](data-sources.md) dla różnych zasobów na platformie Azure.
