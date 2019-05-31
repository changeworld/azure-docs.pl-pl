---
title: Źródła danych w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule opisano dostępne do monitorowania kondycji i wydajności zasobów na platformie Azure i aplikacji działających na nich danych.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: bwren
ms.openlocfilehash: b77fb3ab5651147c59b9f0afd22a2d6d0159c90e
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357487"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Źródła danych monitorowania dla usługi Azure Monitor
Usługa Azure Monitor jest oparty na [wspólną platformę danych monitorowania](data-platform.md) zawierającej [dzienniki](data-platform-logs.md) i [metryki](data-platform-metrics.md). Zbieranie danych na tej platformie umożliwia dane z wielu zasobów do analizy ze sobą przy użyciu wspólnego zestawu narzędzi w usłudze Azure Monitor. Dane monitorowania mogą zostać wysłane do innych lokalizacji w celu obsługi niektórych scenariuszy, a niektóre zasoby mogą zapisywać w innych lokalizacjach, zanim zostaną zebrane w dzienników i metryk.

W tym artykule opisano różne źródła danych zbieranych przez usługi Azure Monitor, oprócz monitorowania danych, utworzonych przez zasoby platformy Azure z monitorowania. Podano linki do szczegółowych informacji na temat konfiguracji wymaganej do zbierania tych danych do innej lokalizacji.

## <a name="application-tiers"></a>Warstwy aplikacji

Źródła danych z aplikacji Azure monitorowania można podzielić na warstwy, najwyższych poziomów trwa samej aplikacji i niższych warstwach, są składniki platformy Azure. Różni się w sposób uzyskiwania dostępu do danych z każdej warstwy. Warstwy aplikacji są podsumowane w poniższej tabeli i źródeł danych w poszczególnych warstwach monitorowania, zostały przedstawione w poniższych sekcjach. Zobacz [monitorowanie lokalizacji danych na platformie Azure](data-locations.md) opis każdej lokalizacji danych i jak mają dostęp do swoich danych.


![Monitorowanie warstw](../media/overview/overview.png)


### <a name="azure"></a>Azure
Poniższa tabela zawiera krótki opis warstwy aplikacji, które są specyficzne dla platformy Azure. Następujące łącze, aby uzyskać dalsze szczegółowe informacje na każdym w poniższych sekcjach.

| Warstwa | Opis | Metoda zbierania |
|:---|:---|:---|
| [Dzierżawa usługi Azure](#azure-tenant) | Dane o poziomie dzierżawy usług platformy Azure, takich jak Azure Active Directory. | Wyświetl dane usługi AAD w portalu lub konfigurowanie kolekcji do usługi Azure Monitor, korzystając z ustawienia diagnostyczne dzierżawy. |
| [Subskrypcja platformy Azure](#azure-subscription) | Dane dotyczące kondycji i zarządzanie usługami obejmujące wiele zasobów w subskrypcji platformy Azure, takich jak usługi Resource Manager i Service Health. | Wyświetl w portalu lub konfigurowanie kolekcji do usługi Azure Monitor korzysta z profilu dziennika. |
| [Zasoby platformy Azure](#azure-resources) |  Dane dotyczące operacji i wydajność poszczególnych zasobów platformy Azure. | Metryki zebrane automatycznie, Wyświetl w Eksploratorze metryk.<br>Konfigurowanie ustawień diagnostycznych do zbierania dzienników w usłudze Azure Monitor.<br>Monitorowanie rozwiązań i szczegółowych informacji dostępnych w przypadku bardziej szczegółowe monitorowania określonych typów zasobów. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, inne chmury lub środowisku lokalnym 
Poniższa tabela zawiera krótki opis warstwy aplikacji, które mogą być na platformie Azure, innej chmurze lub lokalnie. Następujące łącze, aby uzyskać dalsze szczegółowe informacje na każdym w poniższych sekcjach.

| Warstwa | Opis | Metoda zbierania |
|:---|:---|:---|
| [System operacyjny (Gość)](#operating-system-guest) | Dane dotyczące systemu operacyjnego na zasoby obliczeniowe. | Zainstaluj agenta usługi Log Analytics można zbierać źródła danych klienta do usługi Azure Monitor i zależności agentowi zbieranie zależności obsługi usługi Azure Monitor dla maszyn wirtualnych.<br>Dla maszyn wirtualnych platformy Azure należy zainstalować rozszerzenie diagnostyki platformy Azure do gromadzenia dzienników i metryk w usłudze Azure Monitor. |
| [Kod aplikacji](#application-code) | Dane dotyczące wydajności i funkcjonalności rzeczywiste aplikacje i kodu, w tym ślady wydajności, dzienniki aplikacji i danych telemetrycznych użytkownika. | Instrumentować swój kod, aby zbierać dane do usługi Application Insights. |
| [Niestandardowe źródła](#custom-sources) | Dane z usług zewnętrznych lub inne składniki lub urządzeń. | Zbieranie danych dziennika lub metryk do usługi Azure Monitor, za pomocą dowolnego klienta REST. |

## <a name="azure-tenant"></a>Dzierżawa platformy Azure
Dane telemetryczne związane z dzierżawą platformy Azure są zbierane z całej dzierżawie usług, takich jak Azure Active Directory.

![Kolekcja dzierżawy platformy Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Dzienniki inspekcji usługi Azure Active Directory
[Raporty usługi Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) zawiera historię logowania działań i inspekcji dziennik zmian wprowadzonych w określonej dzierżawie. 

| Miejsce docelowe | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Konfigurowanie dzienników usługi Azure AD mają być zbierane w usłudze Azure Monitor, aby analizować je przy użyciu innych danych monitorowania. | [Integrowanie dzienników usługi Azure AD przy użyciu dzienników usługi Azure Monitor (wersja zapoznawcza)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Wyeksportuj dzienniki usługi Azure AD do usługi Azure Storage do archiwizacji. | [Samouczek: Archiwizuj dzienniki usługi Azure AD do konta usługi Azure storage (wersja zapoznawcza)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centrum zdarzeń | Stream dzienniki usługi Azure AD do innych lokalizacji za pomocą usługi Event Hubs. | [Samouczek: Stream dzienniki usługi Azure Active Directory do usługi Azure event hub (wersja zapoznawcza)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Subskrypcja platformy Azure
Dane telemetryczne dotyczące kondycji i działania Twojej subskrypcji platformy Azure.

![Subskrypcja platformy Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure 
[Dziennik aktywności platformy Azure](activity-logs-overview.md) zawiera rekordy kondycji usługi wraz z rekordów na wszelkich zmian konfiguracji wprowadzonych do zasobów w subskrypcji platformy Azure. Dziennik aktywności jest dostępna dla wszystkich zasobów platformy Azure i zapewnia ich _zewnętrznych_ widoku.

| Miejsce docelowe | Opis | Tematy pomocy |
|:---|:---|
| Dziennik aktywności | Dziennik aktywności są zbierane w jej własny magazyn danych, który można wyświetlić z menu usługi Azure Monitor lub umożliwia tworzenie alertów dziennika aktywności. | [Zapytanie dziennika aktywności w witrynie Azure portal](activity-log-view.md#azure-portal) |
| Dzienniki usługi Azure Monitor | Konfigurowanie usługi Azure Monitor dzienniki, aby zebrać dziennik aktywności, aby je przeanalizować za pomocą innych danych monitorowania. | [Zbieranie i analizowanie dzienników aktywności platformy Azure, w obszarze roboczym Log Analytics w usłudze Azure Monitor](activity-log-collect.md) |
| Azure Storage | Eksportuj Dziennik aktywności do usługi Azure Storage, do archiwizacji. | [Archiwizowanie dziennika aktywności](activity-log-export.md#archive-activity-log)  |
| Event Hubs | Stream dziennika aktywności do innych lokalizacji za pomocą usługi Event Hubs | [Stream dzienników aktywności do Centrum zdarzeń](activity-log-export.md#stream-activity-log-to-event-hub). |

### <a name="azure-service-health"></a>Azure Service Health
[Usługa Azure Service Health](../../service-health/service-health-overview.md) zawiera informacje o kondycji usług platformy Azure w ramach subskrypcji, korzystające z aplikacji i zasobów.

| Miejsce docelowe | Opis | Tematy pomocy |
|:---|:---|:---|
| Dziennik aktywności<br>Dzienniki usługi Azure Monitor | Kondycja usługi rekordy są przechowywane w dzienniku aktywności platformy Azure, dzięki czemu można je wyświetlić w witrynie Azure portal lub wykonywać inne działania, które można wykonać przy użyciu dziennika aktywności. | [Wyświetlanie powiadomień dotyczących kondycji usługi za pomocą witryny Azure Portal](service-notifications.md) |


## <a name="azure-resources"></a>Zasoby platformy Azure
Metryki i zasobów poziom dzienniki diagnostyczne zawierają informacje o _wewnętrzny_ operacji zasobów platformy Azure. Są one dostępne dla większości usług platformy Azure i monitorowania rozwiązań i szczegółowych informacji zbierać dodatkowe dane dla konkretnej usługi.

![Kolekcja zasobów platformy Azure](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Metryki platformy 
Większość usług platformy Azure będzie wysyłać [metryk platformy](data-platform-metrics.md) odzwierciedlające ich wydajność i działanie bezpośrednio z bazą danych metryk. Konkretne [metryki będą się różnić dla każdego typu zasobu](metrics-supported.md). 

| Miejsce docelowe | Opis | Tematy pomocy |
|:---|:---|:---|
| Azure Monitor Metrics | Metryki platformy będzie zapisywać do bazy danych metryk usługi Azure Monitor bez konfiguracji. Metryki platformy dostęp z poziomu Eksploratora metryk.  | [Wprowadzenie do Eksploratora metryk platformy Azure](metrics-getting-started.md)<br>[Obsługiwane metryki z usługą Azure Monitor](metrics-supported.md) |
| Dzienniki usługi Azure Monitor | Skopiuj metryk platformy do dzienników na potrzeby analizy trendu i innych za pomocą usługi Log Analytics. | [Narzędzie diagnostyczne systemu Azure bezpośrednio do usługi Log Analytics](collect-azure-metrics-logs.md#azure-diagnostics-direct-to-log-analytics) |
| Event Hubs | Stream metryk do innych lokalizacji za pomocą usługi Event Hubs. |[Stream danych monitorowania platformy Azure do Centrum zdarzeń do użycia przez narzędzie zewnętrzne](stream-monitoring-data-event-hubs.md) |

### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
[Dzienniki diagnostyczne](diagnostic-logs-overview.md) dają wgląd w _wewnętrzny_ operacji zasobu platformy Azure.  Dzienniki diagnostyczne nie są domyślnie włączone. Należy je włączyć i określ lokalizację docelową dla każdego zasobu. 

Wymagania dotyczące konfiguracji i zawartości dzienniki diagnostyczne różnią się zależnie od typu zasobu, a nie wszystkie usługi jeszcze utworzyć dzienników diagnostycznych. Zobacz [obsługiwane usługi, schematów i kategorie dla dzienników diagnostycznych usługi Azure](diagnostic-logs-schema.md) szczegółowe informacje o poszczególnych usług i łącza do szczegółowych procedur. Jeśli usługa nie jest wymieniona w tym artykule, czy usługa obecnie nie zapisu do dzienników diagnostycznych.

| Miejsce docelowe | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Wyślij dzienniki diagnostyczne do usługi Azure Monitor dzienników do analizy na inne zebrane dane dziennika. Niektóre zasoby można napisać bezpośrednio do usługi Azure Monitor, podczas gdy inne zapisać na koncie magazynu przed zostaną zaimportowane do obszaru roboczego usługi Log Analytics. | [Dzienniki diagnostyczne usługi Azure Stream do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor](diagnostic-logs-stream-log-store.md)<br>[Zbieranie dzienników z usługi Azure Storage za pomocą witryny Azure portal](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)  |
| Magazyn | Wysyłanie diagnostycznych dzienników do usługi Azure Storage do archiwizacji. | [Archiwizuj dzienniki diagnostyczne platformy Azure](archive-diagnostic-logs.md) |
| Event Hubs | Stream dzienniki diagnostyczne do innych lokalizacji za pomocą usługi Event Hubs. |[Stream dzienniki diagnostyczne platformy Azure do Centrum zdarzeń](diagnostic-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>System operacyjny (Gość)
Zasoby obliczeniowe na platformie Azure, w innych chmur i rozwiązań lokalnych mają system operacyjny gościa do monitorowania. W instalacji jednego lub więcej agentów można zbierać dane telemetryczne z gościa do usługi Azure Monitor, aby je przeanalizować za pomocą tych samych narzędzi do monitorowania jako samych usług platformy Azure.

![Kolekcja zasobów obliczeniowych platformy Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Rozszerzenie diagnostyki platformy Azure
Włączanie rozszerzenia diagnostyki Azure dla maszyn wirtualnych platformy Azure pozwala zbierać dzienniki i metryki z systemu operacyjnego gościa platformy Azure compute zasobów, w tym usługa w chmurze (klasyczne) w sieci Web i ról procesów roboczych, maszyn wirtualnych, maszyny wirtualnej zestawy skalowania, a usługi Service Fabric.

| Miejsce docelowe | Opis | Tematy pomocy |
|:---|:---|:---|
| Magazyn | Po włączeniu rozszerzenia diagnostyki, domyślnie będzie zapisywać do konta magazynu. | [Przechowywanie i przeglądanie danych diagnostycznych w usłudze Azure Storage](diagnostics-extension-to-storage.md) |
| Azure Monitor Metrics | Podczas konfigurowania rozszerzenia diagnostyki, które można zebrać liczników wydajności, są one zapisywane w bazie danych metryk usługi Azure Monitor. | [Wyślij metryki systemu operacyjnego gościa, aby metryki usługi Azure Monitor przechowywania, przy użyciu szablonu usługi Resource Manager dla maszyny wirtualnej Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Application Insights dzienników | Zbierz liczniki wydajności i dzienników z zasobów obliczeniowych, obsłudze aplikacji mają być analizowane za pomocą innych danych aplikacji. | [Wysyłanie danych diagnostycznych usługi w chmurze, maszyny wirtualnej lub usługi Service Fabric do usługi Application Insights](diagnostics-extension-to-application-insights.md) |
| Event Hubs | Skonfiguruj rozszerzenie diagnostyki przesłać strumień danych do innych lokalizacji za pomocą usługi Event Hubs.  | [Strumieniowe przesyłanie danych diagnostycznych platformy Azure w warstwie gorąca ścieżce za pomocą usługi Event Hubs](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Log Analytics agent 
Zainstaluj agenta usługi Log Analytics, kompleksowego monitorowania i zarządzania maszynami wirtualnymi Windows lub Linux. Maszyna wirtualna może działać na platformie Azure, innej chmurze lub lokalnie.

| Miejsce docelowe | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Łączy agenta usługi Log Analytics, Azure monitorowanie, bezpośrednio lub za pomocą programu System Center Operations Manager i umożliwia zbieranie danych ze źródeł danych, które można skonfigurować lub rozwiązania, które zapewniają dodatkowe szczegółowe informacje dotyczące aplikacji do monitorowania uruchomiona na maszynie wirtualnej. | [Agent źródeł danych w usłudze Azure Monitor](agent-data-sources.md)<br>[Łączenie programu Operations Manager do usługi Azure Monitor](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych 
[Usługa Azure Monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) zapewnia dostosowane środowisko monitorowania dla maszyn wirtualnych, które funkcji wykraczających poza funkcje usługi Azure Monitor, w tym stan usługi i kondycja maszyny Wirtualnej. Wymaga agenta zależności na maszynach wirtualnych Windows i Linux, która integruje się z agentem usługi Log Analytics, aby zbieranie odnalezionych danych dotyczących procesów uruchomionych na maszynę wirtualną i zależności w procesie zewnętrznym.

| Miejsce docelowe | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Przechowuje dane dotyczące procesów i zależności w agencie. | [W przypadku maszyn wirtualnych (wersja zapoznawcza) mapę, aby poznać składniki aplikacji za pomocą usługi Azure Monitor](../insights/vminsights-maps.md) |
| Magazyn maszyny Wirtualnej | Usługa Azure Monitor dla maszyn wirtualnych są przechowywane informacje o stanie kondycji w lokalizacji niestandardowej. To jest dostępna tylko dla usługi Azure Monitor dla maszyn wirtualnych w witrynie Azure portal, oprócz [usłudze Azure Resource health interfejsu API REST](/rest/api/resourcehealth/). | [Poznanie kondycji usługi Azure virtual machines](../insights/vminsights-health.md)<br>[Kondycja zasobów Azure interfejsu API REST](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>Kod aplikacji
Monitorowanie aplikacji szczegółowe w usłudze Azure Monitor jest przeprowadzane za pomocą [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/) który zbiera dane z aplikacji działających na wielu różnych platformach. Aplikacja może być uruchomiony na platformie Azure, innej chmurze lub lokalnie.

![Zbieranie danych aplikacji](media/data-sources/applications.png)


### <a name="application-data"></a>Dane aplikacji
Po włączeniu usługi Application Insights dla aplikacji, instalując pakiet Instrumentacja zbiera metryki i dzienniki dotyczące wydajności i działania aplikacji. Usługi Application Insights są przechowywane dane, które są zbierane w tej samej platformy danych usługi Azure Monitor, używane przez inne źródła danych. Zawiera on rozbudowane narzędzia do analizowania tych danych, ale można również analizować je przy użyciu danych z innych źródeł przy użyciu narzędzi, takich jak Eksplorator metryk i Log Analytics.

| Miejsce docelowe | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Operacyjne dane dotyczące Twojej aplikacji, w tym wyświetleń stron, aplikacji żądań, wyjątków oraz śladów. | [Analizuj dane dzienników w usłudze Azure Monitor](../log-query/log-query-overview.md) |
|                    | Informacje o zależnościach między składnikami aplikacji do obsługi mapy aplikacji i korelacja telemetrii. | [Korelacja telemetrii w usłudze Application Insights](../app/correlation.md) <br> [Mapa aplikacji](../app/app-map.md) |
|            | Wyniki testów dostępności, testu dostępności i czasu odpowiedzi aplikacji w różnych lokalizacjach w publicznej sieci Internet. | [Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web](../app/monitor-web-app-availability.md) |
| Azure Monitor Metrics | Usługa Application Insights zbiera metryki opisujące wydajności i działania aplikacji, oprócz metryki niestandardowe, zdefiniowanej w aplikacji do bazy danych metryk usługi Azure Monitor. | [Dziennik i wstępnie zagregowane metryki w usłudze Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[Interfejs API usługi Application Insights dla niestandardowych zdarzeń i metryk](../app/api-custom-events-metrics.md) |
| Azure Storage | Wyślij dane aplikacji do usługi Azure Storage do archiwizacji. | [Eksportowanie telemetrii z usługi Application Insights](../app/export-telemetry.md) |
|            | Szczegółowe informacje o dostępności testów są przechowywane w usłudze Azure Storage. Użyj usługi Application Insights w witrynie Azure portal, aby pobrać do analizy lokalnego. Wyniki testów dostępności są przechowywane w dziennikach monitora platformy Azure. | [Monitorowanie dostępności i czasu odpowiedzi dowolnej witryny sieci Web](../app/monitor-web-app-availability.md) |
|            | Profiler śledzenia dane są przechowywane w usłudze Azure Storage. Użyj usługi Application Insights w witrynie Azure portal, aby pobrać do analizy lokalnego.  | [Profil aplikacji produkcyjnych na platformie Azure za pomocą usługi Application Insights](../app/profiler-overview.md) 
|            | Debugowanie migawki, w którym dane przechwycone dla podzbioru wyjątków są przechowywane w usłudze Azure Storage. Użyj usługi Application Insights w witrynie Azure portal, aby pobrać do analizy lokalnego.  | [Jak działają migawek](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Rozwiązania do monitorowania i szczegółowe informacje
[Monitorowanie rozwiązań](../insights/solutions.md) i [Insights](../insights/insights-overview.md) zbierania danych do zapewniania dodatkowego wglądu w operacje dotyczące określonej usługi lub aplikacji. Mogą one adresów zasobów w innej aplikacji warstwy i nawet utworzyć wiele warstw.

### <a name="monitoring-solutions"></a>Rozwiązania do monitorowania

| Miejsce docelowe | Opis | Tematy pomocy
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Rozwiązania do monitorowania zbieranie danych do usługi Azure Monitor dzienniki których mogą być analizowane przy użyciu języka zapytań lub [widoków](view-designer.md) najczęściej uwzględnianych w rozwiązaniu. | [Szczegóły zbierania danych monitorowania rozwiązań na platformie Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Usługa Azure Monitor dla kontenerów
[Usługa Azure Monitor dla kontenerów](../insights/container-insights-overview.md) oferuje dostosowane środowisko monitorowania dla [Azure Kubernetes Service (AKS)](/azure/aks/). Zbiera dodatkowe dane dotyczące tych zasobów, które opisano w poniższej tabeli.

| Miejsce docelowe | Opis | Tematy pomocy |
|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Magazyny danych monitorowania dla usługi AKS, łącznie z magazynu, dzienników i zdarzeń. Metryki także dane są przechowywane w dziennikach w celu wykorzystać jej funkcje analizy w portalu. | [Sprawdzanie wydajności klastra usługi AKS w usłudze Azure Monitor dla kontenerów](../insights/container-insights-analyze.md) |
| Azure Monitor Metrics | Dane metryk są przechowywane w bazie metryki, aby dysk Wizualizacja i alerty. | [Wyświetl metryki kontenera w Eksploratorze metryk](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Z kolei prawie doświadczenie w czasie rzeczywistym usługi Azure Monitor dla kontenerów przedstawia dane bezpośrednio z usługi Azure Kubernetes w witrynie Azure portal. | [Jak wyświetlić dzienniki kontenerów w czasie rzeczywistym w usłudze Azure Monitor dla kontenerów (wersja zapoznawcza)](../insights/container-insights-live-logs.md) |

### <a name="azure-monitor-for-vms"></a>Usługa Azure Monitor dla maszyn wirtualnych
[Usługa Azure Monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) udostępnia dostosować usługę do monitorowania maszyn wirtualnych. Opis danych zbieranych przez usługi Azure Monitor dla maszyn wirtualnych znajduje się w [systemu operacyjnego (Gość)](#operating-system-guest) powyższej sekcji.

## <a name="custom-sources"></a>Niestandardowe źródła
Oprócz standardowych warstw aplikacji może być konieczne monitorowanie innych zasobów, które mają dane telemetryczne, które nie mogą być zbierane z innymi źródłami danych. Dla tych zasobów należy zapisać te dane do metryk lub dzienników przy użyciu interfejsu API usługi Azure Monitor.

![Kolekcja niestandardowa](media/data-sources/custom.png)

| Miejsce docelowe | Metoda | Opis | Tematy pomocy |
|:---|:---|:---|:---|
| Dzienniki usługi Azure Monitor | Interfejs API modułu zbierającego dane | Zbieraj dane dzienników z dowolnego klienta REST i Zapisz w obszarze roboczym usługi Log Analytics. | [Wyślij dane dziennika do usługi Azure Monitor za pomocą interfejsu API modułu zbierającego dane HTTP (publiczna wersja zapoznawcza)](data-collector-api.md) |
| Azure Monitor Metrics | Interfejs API metryk niestandardowych | Zbieranie danych metryk z dowolnego klienta REST i zapisać w bazie danych metryk usługi Azure Monitor. | [Wyślij metryki niestandardowe dla zasobów platformy Azure do usługi Azure Monitor metryki magazynu przy użyciu interfejsu API REST](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Inne usługi
Innych usług na platformie Azure zapisu danych na platformę danych usługi Azure Monitor. Dzięki temu można analizować dane zbierane przez te usługi z danymi zbieranymi przez monitorowanie platformy Azure i korzystać z tej samej analizy i narzędzi do wizualizacji.

| Usługa | Miejsce docelowe | Opis | Tematy pomocy |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Dzienniki usługi Azure Monitor | Usługa Azure Security Center są przechowywane dane zabezpieczeń, które są zbierane w obszarze roboczym usługi Log Analytics, co pozwala analizować z innymi danymi dzienników zbieranych przez usługi Azure Monitor.  | [Zbieranie danych w usłudze Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Dzienniki usługi Azure Monitor | Wartownik Azure przechowuje dane, które są zbierane z różnych źródeł danych w obszarze roboczym usługi Log Analytics, co pozwala analizować z innymi danymi dzienników zbieranych przez usługi Azure Monitor.  | [Połącz źródła danych](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [typów danych monitorowania zbieranych przez usługi Azure Monitor](data-platform.md) oraz jak wyświetlać i analizować te dane.
- Lista [różnych lokalizacjach, w której zasoby platformy Azure przechowywać dane](data-locations.md) i jak można do niego dostęp. 
