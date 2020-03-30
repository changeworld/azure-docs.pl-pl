---
title: Monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób zbierania i analizowania danych monitorowania z zasobów na platformie Azure przy użyciu usługi Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249271"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor
Jeśli masz krytyczne aplikacje i procesy biznesowe korzystające z zasobów platformy Azure, chcesz monitorować te zasoby pod kątem ich dostępności, wydajności i działania. W tym artykule opisano dane monitorowania generowane przez zasoby platformy Azure i jak można używać funkcji usługi Azure Monitor do analizowania i ostrzegania o tych danych.

> [!IMPORTANT]
> Ten artykuł dotyczy wszystkich usług na platformie Azure korzystających z usługi Azure Monitor. Zasoby obliczeniowe, w tym maszyny wirtualne i usługa app service, generują te same dane monitorowania opisane w tym miejscu, ale mają również system operacyjny gościa, który może również generować dzienniki i metryki. Szczegółowe informacje na temat zbierania i analizowania tych danych można znaleźć w dokumentacji monitorowania tych usług.

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?
Usługa Azure Monitor to usługa monitorowania pełnego stosu na platformie Azure, która zapewnia pełny zestaw funkcji do monitorowania zasobów platformy Azure oprócz zasobów w innych chmurach i lokalnie. [Platforma danych usługi Azure Monitor](../platform/data-platform.md) zbiera dane do [dzienników](../platform/data-platform-logs.md) i [metryk,](../platform/data-platform-metrics.md) gdzie można je analizować razem przy użyciu pełnego zestawu narzędzi do monitorowania, zgodnie z opisem w poniższych sekcjach.

- [Co można zrobić z metryk usługi Azure Monitor?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Co można zrobić z dziennikami monitora platformy Azure?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Po utworzeniu zasobu platformy Azure usługa Azure Monitor jest włączona i rozpoczyna zbieranie metryk i dzienników aktywności, które można [wyświetlać i analizować w witrynie Azure portal.](#monitoring-in-the-azure-portal) W przypadku konfiguracji można zebrać dodatkowe dane monitorowania i włączyć dodatkowe funkcje. Zobacz [dane monitorowania](#monitoring-data) poniżej, aby uzyskać szczegółowe informacje na temat wszelkich wymagań dotyczących konfiguracji.


## <a name="costs-associated-with-monitoring"></a>Koszty związane z monitorowaniem
Nie ma żadnych kosztów analizy danych monitorowania, które są zbierane domyślnie. Uwzględnione są następujące elementy:

- Zbieranie metryk platformy i analizowanie ich za pomocą Eksploratora metryk.
- Zbieranie dziennika aktywności i analizowanie go w witrynie Azure portal.
- Tworzenie reguły alertu dziennika aktywności.

Nie ma żadnych kosztów usługi Azure Monitor do zbierania i eksportowania dzienników i metryk, ale mogą być związane koszty związane z miejscem docelowym:

- Koszty związane z pozyskiwaniem i przechowywaniem danych podczas zbierania dzienników i metryk w obszarze roboczym usługi Log Analytics. Zobacz [ceny usługi Azure Monitor dla usługi Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Koszty związane z magazynem danych podczas zbierania dzienników i metryk na koncie magazynu platformy Azure. Zobacz [Cennik usługi Azure Storage dla magazynu obiektów blob](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Koszty związane z przesyłaniem strumieniowym centrum zdarzeń podczas przesyłania dalej dzienników i metryk do usługi Azure Event Hubs. Zobacz [ceny usługi Azure Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Mogą istnieć koszty usługi Azure Monitor skojarzone z następującymi. Zobacz [cennik usługi Azure Monitor:](https://azure.microsoft.com/pricing/details/monitor/)

- Uruchamianie kwerendy dziennika.
- Tworzenie reguły alertu metryki lub kwerendy dziennika.
- Wysyłanie powiadomień z dowolnej reguły alertu.
- Uzyskiwanie dostępu do metryk za pośrednictwem interfejsu API.

## <a name="monitoring-data"></a>Dane monitorowania
Zasoby na platformie Azure generują [dzienniki](../platform/data-platform-logs.md) i [metryki](../platform/data-platform-metrics.md) pokazane na poniższym diagramie. Zapoznaj się z dokumentacją dla każdej usługi platformy Azure dla określonych danych, które generują i wszelkie dodatkowe rozwiązania lub szczegółowe informacje, które dostarczają.

![Omówienie](media/monitor-azure-resource/logs-metrics.png)



- [Metryki platformy](../platform/data-platform-metrics.md) — wartości liczbowe, które są automatycznie zbierane w regularnych odstępach czasu i opisują niektóre aspekty zasobu w określonym czasie. 
- [Dzienniki zasobów](../platform/platform-logs-overview.md) — zapewnij wgląd w operacje, które zostały wykonane w zasobie platformy Azure (na płaszczyźnie danych), na przykład uzyskiwanie klucza tajnego z usługi Key Vault lub składanie żądania do bazy danych. Zawartość i struktura dzienników zasobów zależy od usługi platformy Azure i typu zasobu.
- [Dziennik aktywności](../platform/platform-logs-overview.md) — zapewnia wgląd w operacje na każdym zasobie platformy Azure w subskrypcji z zewnątrz (płaszczyzny zarządzania), na przykład tworzenie nowego zasobu lub uruchamianie maszyny wirtualnej. Jest to informacja o tym, co, kto i kiedy dla wszelkich operacji zapisu (PUT, POST, DELETE) podjęte na zasoby w ramach subskrypcji.


## <a name="configuration-requirements"></a>Wymagania konfiguracyjne

### <a name="configure-monitoring"></a>Konfigurowanie monitorowania
Niektóre dane monitorowania są zbierane automatycznie, ale może być konieczne wykonanie konfiguracji w zależności od wymagań. Szczegółowe informacje dotyczące każdego typu danych monitorowania można znaleźć poniżej.

- [Metryki platformy](../platform/data-platform-metrics.md) — metryki platformy są zbierane automatycznie do [metryk usługi Azure Monitor](../platform/data-platform-metrics.md) bez konieczności konfiguracji. Utwórz ustawienie diagnostyczne, aby wysyłać wpisy do dzienników usługi Azure Monitor lub przesyłać je dalej poza platformę Azure.
- [Dzienniki zasobów](../platform/platform-logs-overview.md) — dzienniki zasobów są generowane automatycznie przez zasoby platformy Azure, ale nie są zbierane bez ustawienia diagnostycznego.  Utwórz ustawienie diagnostyczne, aby wysyłać wpisy do dzienników usługi Azure Monitor lub przesyłać je dalej poza platformę Azure.
- [Dziennik aktywności](../platform/platform-logs-overview.md) — dziennik aktywności jest zbierany automatycznie bez konieczności konfiguracji i można go wyświetlić w witrynie Azure portal. Utwórz ustawienie diagnostyczne, aby skopiować je do dzienników usługi Azure Monitor lub przesłać je dalej poza platformą Azure.

### <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Zbieranie danych do dzienników monitora platformy Azure wymaga obszaru roboczego analizy dzienników. Można szybko rozpocząć monitorowanie usługi, tworząc nowy obszar roboczy, ale może być wartość przy użyciu obszaru roboczego, który zbiera dane z innych usług. Zobacz [Tworzenie obszaru roboczego usługi Log Analytics w portalu Azure,](../learn/quick-create-workspace.md) aby uzyskać szczegółowe informacje na temat tworzenia obszaru roboczego i [projektowania wdrożenia dzienników usługi Azure Monitor,](../platform/design-logs-deployment.md) aby określić najlepszy projekt obszaru roboczego dla twoich wymagań. Jeśli używasz istniejącego obszaru roboczego w organizacji, będziesz potrzebować odpowiednich uprawnień, zgodnie z opisem w [sekcji Zarządzanie dostępem do rejestrowania danych i obszarów roboczych w usłudze Azure Monitor](../platform/manage-access.md). 





## <a name="diagnostic-settings"></a>Ustawienia diagnostyczne
Ustawienia diagnostyczne definiują, gdzie należy wysłać dzienniki zasobów i metryki dla określonego zasobu. Możliwe miejsca docelowe to:

- [Obszar roboczy usługi Log Analytics,](../platform/resource-logs-collect-workspace.md) który umożliwia analizowanie danych przy użyciu innych danych monitorowania zebranych przez usługę Azure Monitor przy użyciu zaawansowanych zapytań dziennika, a także korzystanie z innych funkcji usługi Azure Monitor, takich jak alerty dziennika i wizualizacje. 
- [Centra zdarzeń](../platform/resource-logs-stream-event-hubs.md) do przesyłania strumieniowego danych do systemów zewnętrznych, takich jak siemiemy innych firm i inne rozwiązania do analizy dzienników. 
- [Konto magazynu platformy Azure,](../platform/resource-logs-collect-storage.md) które jest przydatne do inspekcji, analizy statycznej lub tworzenia kopii zapasowych.

Wykonaj procedurę w [Tworzenie ustawień diagnostycznych do zbierania dzienników platformy i metryki na platformie Azure,](../platform/diagnostic-settings.md) aby utworzyć ustawienia diagnostyczne i zarządzać nimi za pośrednictwem witryny Azure portal. Zobacz Tworzenie ustawień diagnostycznych na [platformie Azure przy użyciu szablonu Usługi Resource Manager,](../platform/diagnostic-settings-template.md) aby zdefiniować je w szablonie i włączyć pełne monitorowanie zasobu podczas jego tworzenia.


## <a name="monitoring-in-the-azure-portal"></a>Monitorowanie w witrynie Azure portal
 Dostęp do danych monitorowania większości zasobów platformy Azure można uzyskać z menu zasobu w witrynie Azure portal. Zapewni to dostęp do danych pojedynczego zasobu przy użyciu standardowych narzędzi usługi Azure Monitor. Niektóre usługi platformy Azure zapewni różne opcje, więc należy odwołać się do dokumentacji dla tej usługi, aby uzyskać dodatkowe informacje. Użyj menu **Usługi Azure Monitor** do analizowania danych ze wszystkich monitorowanych zasobów. 

### <a name="overview"></a>Omówienie
Wiele usług będzie zawierać dane monitorowania na ich stronie **przegląd** jako szybki rzut oka na ich działanie. Zazwyczaj będzie to oparte na podzbiorze metryk platformy przechowywanych w metrykach usługi Azure Monitor. Inne opcje monitorowania będą zazwyczaj dostępne w sekcji **Monitorowanie** usług. .

![Strona przeglądowa](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Spostrzeżenia i rozwiązania 
Niektóre usługi zapewnią narzędzia wykraczające poza standardowe funkcje usługi Azure Monitor. [Szczegółowe informacje](../insights/insights-overview.md) zapewniają dostosowane środowisko monitorowania oparte na platformie danych usługi Azure Monitor i standardowych funkcjach. [Rozwiązania](../insights/solutions.md) zapewniają wstępnie zdefiniowaną logikę monitorowania opartą na dziennikach usługi Azure Monitor. 

Jeśli usługa ma wgląd usługi Azure Monitor, można uzyskać do niego dostęp z **monitorowania** w menu każdego zasobu. Uzyskaj dostęp do wszystkich szczegółowych informacji i rozwiązań z menu **Usługi Azure Monitor.**

![Insights](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Metryki
Analizuj metryki w witrynie Azure portal przy użyciu [eksploratora metryk,](../platform/metrics-getting-started.md) który jest dostępny z elementu menu **Metryki** dla większości usług. To narzędzie umożliwia pracę z poszczególnymi metrykami lub łączenie wielu w celu zidentyfikowania korelacji i trendów. 

- Aby uzyskać informacje o podstawach korzystania z Eksploratora metryk, zobacz [Wprowadzenie do Eksploratora metryk platformy Azure.](../platform/metrics-getting-started.md)
- Zobacz [zaawansowane funkcje Eksploratora metryk platformy Azure, aby](../platform/metrics-charts.md) uzyskać zaawansowane funkcje Eksploratora metryk, takie jak używanie wielu metryk i stosowanie filtrów i dzielenie.

![Metryki](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Dziennik aktywności 
Wyświetlanie wpisów w dzienniku aktywności w witrynie Azure portal z filtrem początkowym ustawionym na bieżący zasób. Skopiuj dziennik aktywności do obszaru roboczego usługi Log Analytics, aby uzyskać do niego dostęp do niego, aby używać go w kwerendach dziennika i skoroszytach. 

- Zobacz [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure, aby](../platform/activity-log-view.md) uzyskać szczegółowe informacje na temat wyświetlania dziennika aktywności i pobierania wpisów przy użyciu różnych metod.
- Zobacz dokumentację usługi platformy Azure dla określonych zdarzeń, które są rejestrowane.

![Dziennik aktywności](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor
Dzienniki usługi Azure Monitor konsoliduje dzienniki i metryki z wielu usług i innych źródeł danych do analizy za pomocą zaawansowanego narzędzia zapytań. Jak opisano powyżej, utwórz ustawienie diagnostyczne do zbierania metryk platformy, dziennika aktywności i dzienników zasobów w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor.

[Usługa Log Analytics](../log-query/get-started-portal.md) umożliwia pracę z [zapytaniami dziennika,](../log-query/log-query-overview.md)co jest zaawansowaną funkcją usługi Azure Monitor, która umożliwia wykonywanie zaawansowanej analizy danych dziennika przy użyciu w pełni funkcjonowego języka zapytań. Otwórz usługę Log Analytics z **dzienników** w menu **Monitorowanie** zasobu platformy Azure, aby pracować z zapytaniami dziennika przy użyciu zasobu jako [zakresu kwerendy](../log-query/scope.md#query-scope). Dzięki temu można analizować dane w wielu tabelach tylko dla tego zasobu. Użyj **dzienników** z menu Usługi Azure Monitor, aby uzyskać dostęp do dzienników dla wszystkich zasobów. 

- Zobacz [Wprowadzenie do zapytań dziennika w usłudze Azure Monitor,](../log-query/get-started-queries.md) aby uzyskać samouczek dotyczący używania języka zapytań używanego do pisania zapytań dziennika.
- Zobacz [Zbieranie dzienników zasobów platformy Azure w obszarze roboczym usługi Log Analytics w usłudze Azure Monitor,](../platform/resource-logs-collect-workspace.md) aby uzyskać informacje na temat sposobu zbierania dzienników zasobów w dziennikach usługi Azure Monitor i szczegółowych informacji dotyczących uzyskiwania do nich dostępu w kwerendzie.
- Zobacz [tryb zbierania,](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode) aby uzyskać wyjaśnienie, jak dane dziennika zasobów są uporządkowane w dziennikach usługi Azure Monitor.
- Szczegółowe informacje na temat tabeli w dziennikach usługi Azure Monitor można znaleźć w dokumentacji każdej usługi platformy Azure.

![Dzienniki](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Monitorowanie z wiersza polecenia
Dostęp do danych monitorowania zebranych z zasobu można uzyskać z wiersza polecenia lub dołączyć do skryptu przy użyciu [interfejsu azure powershell](/powershell/azure/) lub [narzędzia polecenia platformy Azure](/cli/azure/). 

- Zobacz [odwołanie do metryk interfejsu wiersza polecenia,](/cli/azure/monitor/metrics) aby uzyskać dostęp do danych metryk z interfejsu wiersza polecenia.
- Zobacz [odwołanie do analizy dziennika interfejsu WIERSZa,](/cli/azure/ext/log-analytics/monitor/log-analytics) aby uzyskać dostęp do danych dzienników usługi Azure Monitor przy użyciu kwerendy dziennika z interfejsu wiersza polecenia.
- Zobacz [odwołanie do metryk programu Azure PowerShell,](/powershell/module/azurerm.insights/get-azurermmetric) aby uzyskać dostęp do danych metryk z programu Azure PowerShell.
- Zobacz [odwołanie do kwerendy dziennika programu Azure PowerShell,](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) aby uzyskać dostęp do danych dziennika usługi Azure Monitor przy użyciu kwerendy dziennika z programu Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Monitorowanie z interfejsu API REST
Uwzględnij dane monitorowania zebrane z zasobu w aplikacji niestandardowej przy użyciu interfejsu API REST.

- Zobacz [instruktaż interfejsu API interfejsu REST monitorowania platformy Azure, aby](../platform/rest-api-walkthrough.md) uzyskać szczegółowe informacje na temat uzyskiwania dostępu do metryk z interfejsu API REST usługi Azure Monitor.
- Zobacz [interfejs API REST usługi Azure Log Analytics, aby](https://dev.loganalytics.io/) uzyskać informacje na temat uzyskiwania dostępu do danych dzienników usługi Azure Monitor przy użyciu kwerendy dziennika z programu Azure PowerShell.

## <a name="alerts"></a>Alerty
[Alerty](../platform/alerts-overview.md) proaktywnie powiadamiają Cię i potencjalnie podejmują działania, gdy w danych monitorowania znajdują się ważne warunki. Tworzenie reguły alertu, która definiuje miejsce docelowe dla alertu, warunki, czy utworzyć alert i wszelkie akcje do podjęcia w odpowiedzi.

Różne rodzaje danych monitorowania są używane dla różnych rodzajów reguł alertów.

- [Alert dziennika aktywności](../platform/alerts-activity-log.md) — utwórz alert podczas tworzenia wpisu w dzienniku aktywności, który spełnia określone kryteria. Dzięki temu można otrzymywać powiadomienia na przykład, gdy tworzony jest określony typ zasobu lub jeśli zmiana konfiguracji nie powiedzie się.
- [Alert metryki](../platform/alerts-metric.md) — tworzenie alertu, gdy wartość metryki przekracza określony próg. Alerty metryki są bardziej responsywne niż inne alerty i mogą być automatycznie rozwiązywane po rozwiązaniu problemu.
- [Alert kwerendy dziennika](../platform/alerts-log.md) — uruchom kwerendę dziennika w regularnych odstępach czasu i utworzyć alert, jeśli zostanie znaleziony określony warunek. Dzięki temu można przeprowadzić złożoną analizę w wielu zestawach danych i .

Użyj **alertów** z menu zasobu, aby wyświetlać alerty i zarządzać regułami alertów dla tego zasobu. Tylko alerty dziennika aktywności i alerty metryki używają poszczególnych zasobów platformy Azure jako obiektu docelowego. Alerty kwerend dziennika używać obszaru roboczego usługi Log Analytics jako miejsce docelowe i są oparte na kwerendzie, która może uzyskać dostęp do wszystkich dzienników przechowywanych w tym obszarze roboczym. Użyj menu Usługi Azure Monitor, aby wyświetlać alerty i zarządzać nimi dla wszystkich zasobów oraz regułami alertów zapytań dziennika.

- Zobacz artykuły dla różnych rodzajów alertów powyżej, aby uzyskać szczegółowe informacje na temat tworzenia reguł alertów.
- Zobacz [Tworzenie grup akcji i zarządzanie nimi w witrynie Azure Portal,](../platform/action-groups.md) aby uzyskać szczegółowe informacje na temat tworzenia grupy akcji, która umożliwia zarządzanie odpowiedziami na alerty.



## <a name="next-steps"></a>Następne kroki

* Zobacz [obsługiwane usługi, schematy i kategorie dzienników zasobów platformy Azure, aby](../platform/diagnostic-logs-schema.md) uzyskać szczegółowe informacje o dziennikach zasobów dla różnych usług platformy Azure.  
