---
title: Źródła danych w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano dostępne dane do monitorowania kondycji i wydajności zasobów platformy Azure i aplikacji działających na nich.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: d1c0652844556b545cf0617032d21b80dd67d198
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479845"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Źródła danych monitorowania dla usługi Azure Monitor
Usługa Azure Monitor jest oparta na [wspólnej platformie danych monitorowania,](data-platform.md) która zawiera [dzienniki](data-platform-logs.md) i [metryki.](data-platform-metrics.md) Zbieranie danych na tej platformie umożliwia analizowanie danych z wielu zasobów przy użyciu wspólnego zestawu narzędzi w usłudze Azure Monitor. Dane monitorowania mogą być również wysyłane do innych lokalizacji do obsługi niektórych scenariuszy, a niektóre zasoby mogą zapisywać w innych lokalizacjach, zanim będą mogły być zbierane w dziennikach lub metrykach.

W tym artykule opisano różne źródła monitorowania danych zebranych przez usługę Azure Monitor oprócz danych monitorowania utworzonych przez zasoby platformy Azure. Łącza są dostarczane do szczegółowych informacji na temat konfiguracji wymaganej do zbierania tych danych do różnych lokalizacji.

## <a name="application-tiers"></a>Warstwy aplikacji

Źródła monitorowania danych z aplikacji platformy Azure można podzielić na warstwy, najwyższe warstwy są same aplikacje i niższych warstw są składniki platformy Azure. Metoda uzyskiwania dostępu do danych z każdej warstwy jest różna. Warstwy aplikacji są podsumowane w poniższej tabeli, a źródła danych monitorowania w każdej warstwie są prezentowane w poniższych sekcjach. Zobacz Monitorowanie lokalizacji danych na [platformie Azure,](data-locations.md) aby uzyskać opis każdej lokalizacji danych i jak uzyskać dostęp do jej danych.


![Poziomy monitorowania](../media/overview/overview.png)


### <a name="azure"></a>Azure
W poniższej tabeli pokrótce opisano warstwy aplikacji, które są specyficzne dla platformy Azure. Po linku, aby uzyskać więcej informacji na temat każdego z poniższych sekcji.

| Warstwa | Opis | Metoda zbierania |
|:---|:---|:---|
| [Dzierżawa platformy Azure](#azure-tenant) | Dane dotyczące działania usług na poziomie dzierżawy platformy Azure, takich jak Azure Active Directory. | Wyświetlanie danych usługi AAD w portalu lub konfigurowanie kolekcji w usłudze Azure Monitor przy użyciu ustawienia diagnostycznego dzierżawy. |
| [Subskrypcja platformy Azure](#azure-subscription) | Dane związane z kondycją i zarządzanie usługami między zasobami w ramach subskrypcji platformy Azure, takimi jak Menedżer zasobów i kondycja usługi. | Wyświetl w portalu lub skonfiguruj kolekcję do usługi Azure Monitor przy użyciu profilu dziennika. |
| [Zasoby platformy Azure](#azure-resources) |  Dane dotyczące działania i wydajności każdego zasobu platformy Azure. | Metryki zbierane automatycznie, wyświetl w Eksploratorze metryk.<br>Skonfiguruj ustawienia diagnostyczne do zbierania dzienników w usłudze Azure Monitor.<br>Rozwiązania do monitorowania i szczegółowe informacje dostępne w celu bardziej szczegółowego monitorowania określonych typów zasobów. |

### <a name="azure-other-cloud-or-on-premises"></a>Platforma Azure, inna chmura lub lokalnie 
W poniższej tabeli pokrótce opisano warstwy aplikacji, które mogą znajdować się na platformie Azure, innej chmurze lub lokalnie. Po linku, aby uzyskać więcej informacji na temat każdego z poniższych sekcji.

| Warstwa | Opis | Metoda zbierania |
|:---|:---|:---|
| [System operacyjny (gość)](#operating-system-guest) | Dane dotyczące systemu operacyjnego na zasobach obliczeniowych. | Zainstaluj agenta usługi Log Analytics, aby zbierać źródła danych klienta do usługi Azure Monitor i agent zależności w celu zbierania zależności obsługujących usługę Azure Monitor dla maszyn wirtualnych.<br>W przypadku maszyn wirtualnych platformy Azure zainstaluj rozszerzenie diagnostyczne platformy Azure, aby zbierać dzienniki i metryki w usłudze Azure Monitor. |
| [Kod aplikacji](#application-code) | Dane dotyczące wydajności i funkcjonalności rzeczywistej aplikacji i kodu, w tym ślady wydajności, dzienniki aplikacji i dane telemetryczne użytkownika. | Instrument kodu do zbierania danych w usłudze Application Insights. |
| [Źródła niestandardowe](#custom-sources) | Dane z usług zewnętrznych lub innych składników lub urządzeń. | Zbieranie danych dziennika lub metryk do usługi Azure Monitor z dowolnego klienta REST. |

## <a name="azure-tenant"></a>Dzierżawa platformy Azure
Dane telemetryczne związane z dzierżawą platformy Azure są zbierane z usług dla całej dzierżawy, takich jak usługa Azure Active Directory.

![Kolekcja dzierżawy platformy Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Dzienniki inspekcji usługi Azure Active Directory
[Raportowanie usługi Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) zawiera historię działania logowania i dziennik inspekcji zmian wprowadzonych w obrębie określonej dzierżawy. 

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Skonfiguruj dzienniki usługi Azure AD do zbierania w usłudze Azure Monitor, aby analizować je przy użyciu innych danych monitorowania. | [Integruj dzienniki usługi Azure AD z dziennikami usługi Azure Monitor (wersja zapoznawcza)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Eksportuj dzienniki usługi Azure AD do usługi Azure Storage w celu archiwizacji. | [Samouczek: archiwizowanie dzienników usługi Azure AD na koncie usługi Azure Storage (wersja zapoznawcza)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centrum zdarzeń | Przesyłaj strumieniowo dzienniki usługi Azure AD do innych lokalizacji przy użyciu centrów zdarzeń. | [Samouczek: Przesyłanie strumieniowe dzienników usługi Azure Active Directory do centrum zdarzeń platformy Azure (wersja zapoznawcza)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Subskrypcja platformy Azure
Telemetria związane ze zdrowiem i działaniem subskrypcji platformy Azure.

![Subskrypcja platformy Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure 
[Dziennik aktywności platformy Azure](platform-logs-overview.md) zawiera rekordy kondycji usługi wraz z rekordami wszelkich zmian konfiguracji wprowadzonych do zasobów w ramach subskrypcji platformy Azure. Dziennik aktywności jest dostępny dla wszystkich zasobów platformy Azure i reprezentuje ich widok _zewnętrzny._

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|
| Dziennik aktywności | Dziennik aktywności jest zbierany do własnego magazynu danych, który można wyświetlić z menu Usługi Azure Monitor lub służy do tworzenia alertów dziennika aktywności. | [Zapytanie o dziennik aktywności w witrynie Azure portal](activity-log-view.md#azure-portal) |
| Dzienniki usługi Azure Monitor | Skonfiguruj dzienniki usługi Azure Monitor, aby zbierać dziennik aktywności, aby analizować go z innymi danymi monitorowania. | [Zbieranie i analizowanie dzienników aktywności platformy Azure w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor](activity-log-collect.md) |
| Azure Storage | Eksportuj dziennik działań do usługi Azure Storage w celu archiwizacji. | [Dziennik aktywności archiwum](resource-logs-collect-storage.md)  |
| Usługa Event Hubs | Przesyłanie strumieniowe dziennika aktywności do innych lokalizacji przy użyciu Centrów zdarzeń | [Dziennik aktywności strumienia do Centrum zdarzeń](resource-logs-stream-event-hubs.md). |

### <a name="azure-service-health"></a>Azure Service Health
[Usługa Azure Service Health](../../service-health/service-health-overview.md) zawiera informacje o kondycji usług platformy Azure w ramach subskrypcji, na których opiera się aplikacja i zasoby.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dziennik aktywności<br>Dzienniki usługi Azure Monitor | Rekordy kondycji usługi są przechowywane w dzienniku aktywności platformy Azure, dzięki czemu można je wyświetlić w witrynie Azure portal lub wykonać inne działania, które można wykonać za pomocą dziennika aktywności. | [Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal](service-notifications.md) |


## <a name="azure-resources"></a>Zasoby platformy Azure
Metryki i dzienniki zasobów zawierają informacje o _wewnętrznej_ operacji zasobów platformy Azure. Są one dostępne dla większości usług platformy Azure, a rozwiązania do monitorowania i szczegółowe informacje zbierają dodatkowe dane dla określonych usług.

![Zbieranie zasobów platformy Azure](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Dane dotyczące platformy 
Większość usług platformy Azure wyśle [metryki platformy,](data-platform-metrics.md) które odzwierciedlają ich wydajność i działanie bezpośrednio do bazy danych metryk. Określone [metryki będą się różnić dla każdego typu zasobu](metrics-supported.md). 

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Metryki usługi Azure Monitor | Metryki platformy będą zapisywać w bazie danych metryk usługi Azure Monitor bez konfiguracji. Uzyskaj dostęp do metryk platformy z Eksploratora metryk.  | [Wprowadzenie do Eksploratora metryk platformy Azure](metrics-getting-started.md)<br>[Obsługiwane metryki za pomocą usługi Azure Monitor](metrics-supported.md) |
| Dzienniki usługi Azure Monitor | Kopiuj metryki platformy do dzienników dla trendów i innych analiz przy użyciu usługi Log Analytics. | [Diagnostyka platformy Azure bezpośrednio do usługi Log Analytics](resource-logs-collect-workspace.md) |
| Usługa Event Hubs | Przesyłaj dane do innych lokalizacji za pomocą Centrów zdarzeń. |[Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń w celu użycia za pomocą narzędzia zewnętrznego](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Dzienniki zasobów
[Dzienniki zasobów](platform-logs-overview.md) zapewniają wgląd w _wewnętrzne_ działanie zasobu platformy Azure.  Dzienniki zasobów są tworzone automatycznie, ale należy utworzyć ustawienie diagnostyczne, aby określić miejsce docelowe dla nich do zebrania dla każdego zasobu.

Wymagania dotyczące konfiguracji i zawartość dzienników zasobów różnią się w zależności od typu zasobu, a nie wszystkie usługi jeszcze je tworzą. Zobacz [obsługiwane usługi, schematy i kategorie dzienników zasobów platformy Azure,](diagnostic-logs-schema.md) aby uzyskać szczegółowe informacje na temat każdej usługi i łącza do szczegółowych procedur konfiguracji. Jeśli usługa nie jest wymieniona w tym artykule, ta usługa nie tworzy obecnie dzienników zasobów.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Wysyłaj dzienniki zasobów do dzienników usługi Azure Monitor w celu analizy z innymi zebranymi danymi dziennika. | [Zbieranie dzienników zasobów platformy Azure w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor](resource-logs-collect-storage.md) |
| Magazyn | Wysyłanie dzienników zasobów do usługi Azure Storage w celu archiwizacji. | [Archiwizuj dzienniki zasobów platformy Azure](resource-logs-collect-workspace.md) |
| Usługa Event Hubs | Przesyłaj strumieniowo dzienniki zasobów do innych lokalizacji przy użyciu centrów zdarzeń. |[Strumieniowanie dzienników zasobów platformy Azure do centrum zdarzeń](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>System operacyjny (gość)
Zasoby obliczeniowe na platformie Azure, w innych chmurach i lokalnie mają system operacyjny gościa do monitorowania. Po zainstalowaniu jednego lub więcej agentów można zbierać dane telemetryczne od gościa do usługi Azure Monitor, aby przeanalizować go za pomocą tych samych narzędzi monitorowania, co same usługi platformy Azure.

![Zbieranie zasobów obliczeniowych platformy Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Rozszerzenie diagnostyki platformy Azure
Włączenie rozszerzenia diagnostyki platformy Azure dla maszyn wirtualnych platformy Azure umożliwia zbieranie dzienników i metryk z systemu operacyjnego gościa zasobów obliczeniowych platformy Azure, w tym ról sieci Web i procesu roboczego usługi Azure (klasycznej), maszyn wirtualnych, maszyny wirtualnej i sieci szkieletowej usług.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Magazyn | Rozszerzenie diagnostyki platformy Azure zawsze zapisuje na koncie usługi Azure Storage. | [Instalowanie i konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (WAD)](diagnostics-extension-windows-install.md)<br>[Używanie rozszerzenia diagnostycznego systemu Linux do monitorowania metryk i dzienników](../../virtual-machines/extensions/diagnostics-linux.md) |
| Metryki usługi Azure Monitor | Po skonfigurowaniu rozszerzenia diagnostyki do zbierania liczników wydajności są one zapisywane w bazie danych metryk usługi Azure Monitor. | [Wysyłanie metryk systemu operacyjnego gościa do magazynu metryk Usługi Azure Monitor przy użyciu szablonu Menedżera zasobów dla maszyny wirtualnej systemu Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Usługa Event Hubs | Skonfiguruj rozszerzenie diagnostyki, aby przesyłać strumieniowo dane do innych lokalizacji przy użyciu centrów zdarzeń.  | [Przesyłanie strumieniowe danych diagnostyki platformy Azure przy użyciu centrów zdarzeń](diagnostics-extension-stream-event-hubs.md)<br>[Używanie rozszerzenia diagnostycznego systemu Linux do monitorowania metryk i dzienników](../../virtual-machines/extensions/diagnostics-linux.md) |
| Dzienniki usługi Application Insights | Zbieranie dzienników i liczników wydajności z zasobu obliczeniowego obsługującego aplikację, które mają być analizowane z innymi danymi aplikacji. | [Wysyłanie danych diagnostycznych usługi w chmurze, maszyny wirtualnej lub sieci szkieletowej usług do usługi Application Insights](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Agent usługi Log Analytics 
Zainstaluj agenta usługi Log Analytics w celu kompleksowego monitorowania i zarządzania maszynami wirtualnymi systemu Windows lub Linux. Maszyna wirtualna może być uruchomiona na platformie Azure, innej chmurze lub lokalnie.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Agent usługi Log Analytics łączy się z usługą Azure Monitor bezpośrednio lub za pośrednictwem programu System Center Operations Manager i umożliwia zbieranie danych ze źródeł danych, które można skonfigurować, lub z rozwiązań do monitorowania, które zapewniają dodatkowe informacje na temat aplikacji na maszynie wirtualnej. | [Źródła danych agenta w usłudze Azure Monitor](agent-data-sources.md)<br>[Łączenie programu Operations Manager z monitorem platformy Azure](om-agents.md) |
| Magazyn maszyn wirtualnych | Usługa Azure Monitor dla maszyn wirtualnych używa agenta usługi Log Analytics do przechowywania informacji o stanie zdrowia w lokalizacji niestandardowej. Aby uzyskać więcej informacji, zobacz następną sekcję.  |


### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych 
[Usługa Azure Monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) zapewnia dostosowane środowisko monitorowania maszyn wirtualnych udostępniających funkcje wykraczające poza podstawową funkcję usługi Azure Monitor. Wymaga agenta zależności na maszynach wirtualnych systemu Windows i Linux, który integruje się z agentem usługi Log Analytics do zbierania odnalezionych danych o procesach uruchomionych na maszynie wirtualnej i zależnościach procesów zewnętrznych.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Przechowuje dane o procesach i zależnościach od agenta. | [Korzystanie z usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) Mapa do zrozumienia składników aplikacji](../insights/vminsights-maps.md) |



## <a name="application-code"></a>Kod aplikacji
Szczegółowe monitorowanie aplikacji w usłudze Azure Monitor odbywa się za pomocą [usługi Application Insights,](https://docs.microsoft.com/azure/application-insights/) która zbiera dane z aplikacji uruchomionych na różnych platformach. Aplikacja może być uruchomiona na platformie Azure, innej chmurze lub lokalnie.

![Zbieranie danych aplikacji](media/data-sources/applications.png)


### <a name="application-data"></a>Dane aplikacji
Po włączeniu usługi Application Insights dla aplikacji przez zainstalowanie pakietu instrumentacji, zbiera metryki i dzienniki związane z wydajnością i działaniem aplikacji. Usługa Application Insights przechowuje dane, które zbiera na tej samej platformie danych usługi Azure Monitor, używanej przez inne źródła danych. Zawiera rozbudowane narzędzia do analizowania tych danych, ale można również analizować je za pomocą danych z innych źródeł przy użyciu narzędzi, takich jak Metrics Explorer i Log Analytics.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Dane operacyjne dotyczące aplikacji, w tym widoki strony, żądania aplikacji, wyjątki i ślady. | [Analizowanie danych dziennika w usłudze Azure Monitor](../log-query/log-query-overview.md) |
|                    | Informacje o zależności między składnikami aplikacji do obsługi mapy aplikacji i korelacji telemetrii. | [Korelacja telemetrii w usłudze Application Insights](../app/correlation.md) <br> [Mapa aplikacji](../app/app-map.md) |
|            | Wyniki testów dostępności, które testują dostępność i szybkość reakcji aplikacji z różnych lokalizacji w publicznym Internecie. | [Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web](../app/monitor-web-app-availability.md) |
| Metryki usługi Azure Monitor | Usługa Application Insights zbiera metryki opisujące wydajność i działanie aplikacji oprócz niestandardowych metryk zdefiniowanych w aplikacji do bazy danych metryk usługi Azure Monitor. | [Metryki oparte na dzienniku i metryki wstępnie zagregowane w usłudze Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[Interfejs API usługi Application Insights dla niestandardowych zdarzeń i metryk](../app/api-custom-events-metrics.md) |
| Azure Storage | Wysyłanie danych aplikacji do usługi Azure Storage w celu archiwizacji. | [Eksportowanie telemetrii z usługi Application Insights](../app/export-telemetry.md) |
|            | Szczegóły testów dostępności są przechowywane w usłudze Azure Storage. Użyj usługi Application Insights w witrynie Azure portal do pobierania do analizy lokalnej. Wyniki testów dostępności są przechowywane w dziennikach usługi Azure Monitor. | [Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web](../app/monitor-web-app-availability.md) |
|            | Dane śledzenia profilera są przechowywane w usłudze Azure Storage. Użyj usługi Application Insights w witrynie Azure portal do pobierania do analizy lokalnej.  | [Aplikacje produkcyjne profilu na platformie Azure za pomocą usługi Application Insights](../app/profiler-overview.md) 
|            | Debugowanie danych migawki, który jest przechwytywany dla podzbioru wyjątków jest przechowywany w usłudze Azure Storage. Użyj usługi Application Insights w witrynie Azure portal do pobierania do analizy lokalnej.  | [Jak działają migawki](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Rozwiązania do monitorowania i analizy
[Rozwiązania do monitorowania](../insights/solutions.md) i [aplikacja Insights](../insights/insights-overview.md) zbierają dane, aby zapewnić dodatkowe informacje na temat działania określonej usługi lub aplikacji. Mogą one adres zasobów w różnych warstwach aplikacji, a nawet wiele warstw.

### <a name="monitoring-solutions"></a>Rozwiązania do monitorowania

| Element docelowy | Opis | Tematy pomocy
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Rozwiązania do monitorowania zbierają dane do dzienników usługi Azure Monitor, gdzie mogą być analizowane przy użyciu języka zapytań lub [widoków,](view-designer.md) które są zazwyczaj zawarte w rozwiązaniu. | [Szczegóły zbierania danych dotyczące monitorowania rozwiązań na platformie Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów
[Usługa Azure Monitor dla kontenerów](../insights/container-insights-overview.md) zapewnia dostosowane środowisko monitorowania usługi [Azure Kubernetes Service (AKS).](/azure/aks/) Zbiera dodatkowe dane dotyczące tych zasobów opisane w poniższej tabeli.

| Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Przechowuje dane monitorowania dla usługi AKS, w tym zapasy, dzienniki i zdarzenia. Dane metryki są również przechowywane w dziennikach w celu wykorzystania jego funkcji analizy w portalu. | [Sprawdzanie wydajności klastra usługi AKS w usłudze Azure Monitor dla kontenerów](../insights/container-insights-analyze.md) |
| Metryki usługi Azure Monitor | Dane metryki są przechowywane w bazie danych metryk, aby zwiększyć wizualizację i alerty. | [Wyświetlanie metryk kontenerów w Eksploratorze metryk](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Zapewnia bezpośredni dostęp do dzienników kontenerów usługi Azure Kubernetes (AKS) (stdout/stderror), zdarzeń i metryk zasobników w portalu. | [Jak wyświetlać dzienniki, zdarzenia i dane zasobników kubernetes w czasie rzeczywistym](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych
[Usługa Azure Monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) zapewnia dostosowane środowisko do monitorowania maszyn wirtualnych. Opis danych zebranych przez usługę Azure Monitor dla maszyn wirtualnych znajduje się w sekcji [System operacyjny (gość)](#operating-system-guest) powyżej.

## <a name="custom-sources"></a>Źródła niestandardowe
Oprócz standardowych warstw aplikacji może być konieczne monitorowanie innych zasobów, które mają dane telemetryczne, które nie mogą być zbierane z innymi źródłami danych. W przypadku tych zasobów należy zapisać te dane w metrykach lub dziennikach przy użyciu interfejsu API usługi Azure Monitor.

![Kolekcja niestandardowa](media/data-sources/custom.png)

| Element docelowy | Metoda | Opis | Tematy pomocy |
|:---|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Interfejs API modułu zbierającego dane | Zbieranie danych dziennika z dowolnego klienta REST i przechowywać w obszarze roboczym usługi Log Analytics. | [Wysyłanie danych dziennika do usługi Azure Monitor za pomocą interfejsu API modułu zbierającego dane HTTP (publiczna wersja zapoznawcza)](data-collector-api.md) |
| Metryki usługi Azure Monitor | Interfejs API metryk niestandardowych | Zbieranie danych metryk z dowolnego klienta REST i przechowywać w bazie danych metryk usługi Azure Monitor. | [Wysyłanie niestandardowych metryk zasobu platformy Azure do magazynu metryk Usługi Azure Monitor przy użyciu interfejsu API REST](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Inne usługi
Inne usługi na platformie Azure zapisują dane na platformie danych usługi Azure Monitor. Dzięki temu można analizować dane zebrane przez te usługi z danymi zebranymi przez usługę Azure Monitor i korzystać z tych samych narzędzi analizy i wizualizacji.

| Usługa | Element docelowy | Opis | Tematy pomocy |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Dzienniki usługi Azure Monitor | Usługa Azure Security Center przechowuje dane zabezpieczeń, które zbiera w obszarze roboczym usługi Log Analytics, co umożliwia ich analizę z innymi danymi dziennika zebranymi przez usługę Azure Monitor.  | [Zbieranie danych w usłudze Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Usługa Azure Sentinel](/azure/sentinel/) | Dzienniki usługi Azure Monitor | Usługa Azure Sentinel przechowuje dane, które zbiera z różnych źródeł danych w obszarze roboczym usługi Log Analytics, co umożliwia ich analizę z innymi danymi dziennika zebranymi przez usługę Azure Monitor.  | [Łączenie ze źródłami danych](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [typach danych monitorowania gromadzonych przez usługę Azure Monitor](data-platform.md) oraz o sposobie wyświetlania i analizowania tych danych.
- Wyświetl [listę różnych lokalizacji,](data-locations.md) w których zasoby platformy Azure przechowują dane i jak można uzyskać do nich dostęp. 
