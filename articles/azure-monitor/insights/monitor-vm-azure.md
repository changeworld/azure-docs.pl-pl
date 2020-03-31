---
title: Monitorowanie maszyn wirtualnych platformy Azure za pomocą usługi Azure Monitor
description: W tym artykule opisano sposób zbierania i analizowania danych monitorowania z maszyn wirtualnych na platformie Azure przy użyciu usługi Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283943"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Monitorowanie maszyn wirtualnych platformy Azure za pomocą usługi Azure Monitor
W tym artykule opisano sposób używania usługi Azure Monitor do zbierania i analizowania danych monitorowania z maszyn wirtualnych platformy Azure w celu utrzymania ich kondycji. Maszyny wirtualne mogą być monitorowane pod kątem dostępności i wydajności za pomocą usługi Azure Monitor, jak każdy [inny zasób platformy Azure,](monitor-azure-resource.md)ale są unikatowe z innych zasobów, ponieważ należy również monitorować system operacyjny i system gościa oraz obciążenia, które w nim działają. 

> [!NOTE]
> Ten artykuł zawiera pełne omówienie pojęć i opcji monitorowania maszyn wirtualnych w usłudze Azure Monitor. Aby szybko rozpocząć monitorowanie maszyn wirtualnych bez koncentrowania się na podstawowych pojęciach, zobacz [Szybki start: Monitorowanie maszyny wirtualnej platformy Azure za pomocą usługi Azure Monitor.](../learn/quick-monitor-azure-vm.md)


## <a name="differences-from-other-azure-resources"></a>Różnice w stosunku do innych zasobów platformy Azure
[Monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor](monitor-azure-resource.md) opisuje dane monitorowania generowane przez zasoby platformy Azure i jak można używać funkcji usługi Azure Monitor do analizowania i ostrzegania o tych danych. Można zbierać i działać na tych samych danych monitorowania z maszyn wirtualnych platformy Azure z następującymi różnicami:

- [Metryki platformy](../platform/data-platform-metrics.md) są zbierane automatycznie dla maszyn wirtualnych, ale tylko dla [hosta maszyny wirtualnej](#monitoring-data). Potrzebny jest agent do zbierania danych o wydajności z systemu operacyjnego gościa. 
- Maszyny wirtualne nie generują [dzienników zasobów,](../platform/platform-logs-overview.md) które zapewniają wgląd w operacje, które zostały wykonane w zasobie platformy Azure. Agent służy do zbierania danych dziennika z systemu operacyjnego gościa.
- Można utworzyć [ustawienia diagnostyczne](../platform/diagnostic-settings.md) dla maszyny wirtualnej, aby wysłać metryki platformy do innych miejsc docelowych, takich jak centrum magazynowe i zdarzenia, ale nie można skonfigurować tych ustawień diagnostycznych w witrynie Azure portal. 

## <a name="monitoring-data"></a>Dane monitorowania
Maszyny wirtualne na platformie Azure na platformie Azure generują [dzienniki](../platform/data-platform-logs.md) i [metryki](../platform/data-platform-metrics.md) pokazane na poniższym diagramie.

![Omówienie](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Host maszyny wirtualnej
Maszyny wirtualne na platformie Azure generują następujące dane dla hosta maszyny wirtualnej takie same jak inne zasoby platformy Azure, jak opisano w [obszarze Monitorowanie danych.](monitor-azure-resource.md#monitoring-data)

- [Metryki platformy](../platform/data-platform-metrics.md) — wartości liczbowe, które są automatycznie zbierane w regularnych odstępach czasu i opisują niektóre aspekty zasobu w określonym czasie. Metryki platformy są zbierane dla hosta maszyny wirtualnej, ale wymagane jest rozszerzenie diagnostyki do zbierania metryk dla systemu operacyjnego gościa.
- [Dziennik aktywności](../platform/platform-logs-overview.md) — zapewnia wgląd w operacje na każdym zasobie platformy Azure w subskrypcji z zewnątrz (płaszczyzny zarządzania). W przypadku maszyny wirtualnej obejmuje to takie informacje, jak podczas jego pracy i wszelkie zmiany konfiguracji.


### <a name="guest-operating-system"></a>System operacyjny gościa
Aby zbierać dane z systemu operacyjnego gościa maszyny wirtualnej, potrzebujesz agenta, który działa lokalnie na każdej maszynie wirtualnej i wysyła dane do usługi Azure Monitor. Wielu agentów są dostępne dla usługi Azure Monitor z każdego zbierania różnych danych i zapisywania danych w różnych lokalizacjach. Uzyskaj szczegółowe porównanie różnych agentów w [omówienie agentów usługi Azure Monitor](../platform/agents-overview.md). 

- [Agent analizy dzienników](../platform/agents-overview.md#log-analytics-agent) — dostępne dla maszyn wirtualnych na platformie Azure, innych środowiskach w chmurze i lokalnie. Zbiera dane do dzienników monitora platformy Azure. Obsługuje usługę Azure Monitor dla maszyn wirtualnych i rozwiązań do monitorowania. Jest to ten sam agent, który jest używany w programie System Center Operations Manager.
- [Agent zależności](../platform/agents-overview.md#dependency-agent) — zbiera dane dotyczące procesów uruchomionych na maszynie wirtualnej i ich zależności. Opiera się na agenta usługi Log Analytics do przesyłania danych do platformy Azure i obsługuje usługi Azure Monitor dla maszyn wirtualnych, mapy usług i przewodów danych 2.0 rozwiązań.
- [Rozszerzenie diagnostyki platformy Azure](../platform/agents-overview.md#azure-diagnostics-extension) — dostępne tylko dla maszyn wirtualnych usługi Azure Monitor. Może zbierać dane do wielu lokalizacji, ale głównie do zbierania danych wydajności gościa do metryk usługi Azure Monitor dla maszyn wirtualnych systemu Windows.
- [Agent Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) — zbieranie danych o wydajności z maszyn wirtualnych z systemem Linux do metryk usługi Azure Monitor.


## <a name="configuration-requirements"></a>Wymagania konfiguracyjne
Aby włączyć wszystkie funkcje usługi Azure Monitor do monitorowania maszyny wirtualnej, należy zbierać dane monitorowania z hosta maszyny wirtualnej i systemu operacyjnego gościa zarówno do [metryk usługi Azure Monitor,](../platform/data-platform-logs.md) jak i [dzienników usługi Azure Monitor.](../platform/data-platform-logs.md) W poniższej tabeli wymieniono konfigurację, która musi zostać wykonana, aby włączyć tę kolekcję. Możesz nie wykonywać wszystkich tych kroków w zależności od określonych wymagań.

| Krok konfiguracji | Akcje zakończone | Funkcje włączone |
|:---|:---|:---|
| Brak konfiguracji | - Metryki platformy hosta zebrane do metryki.<br>- Dziennik aktywności zebranych. | - Eksplorator metryk dla hosta.<br>- Metryki alerty dla hosta.<br>- Alerty dziennika aktywności. |
| [Włączanie usługi Azure Monitor dla maszyn wirtualnych](#enable-azure-monitor-for-vms) | - Zainstalowany agent analizy dzienników.<br>- Zainstalowany agent zależności.<br>- Dane dotyczące wydajności gościa zebrane w dziennikach.<br>- Szczegóły procesu i zależności zebrane w dziennikach. | - Wykresy wydajności i skoroszyty dla danych wydajności gościa.<br>- Rejestrowanie zapytań dla danych wydajności gościa.<br>- Alerty dziennika dla danych wydajności gościa.<br>- Mapa zależności. |
| [Zainstaluj rozszerzenie diagnostyczne i agent telegraf](#enable-diagnostics-extension-and-telegraf-agent) | - Dane dotyczące wydajności gości zebrane do metryki. | - Metryka explorer dla gościa.<br>- Alerty metryki dla gościa.  |
| [Konfigurowanie obszaru roboczego usługi Log Analytics](#configure-log-analytics-workspace) | - Wydarzenia zebrane od gościa. | - Rejestrowanie zapytań dla wydarzeń gościa.<br>- Dziennik alertów dla wydarzeń gościa. |
| [Tworzenie ustawienia diagnostycznego dla maszyny wirtualnej](#collect-platform-metrics-and-activity-log) | - Dane platformy zebrane w dziennikach.<br>- Dziennik aktywności zebranych do dzienników. | - Zapytania Loq dla metryk hosta.<br>- Alerty dziennika dla metryk hosta.<br>- Zapytania dziennika dla dziennika aktywności.

Każdy z tych kroków konfiguracji jest opisany w poniższych sekcjach.

### <a name="enable-azure-monitor-for-vms"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych
[Usługa Azure Monitor dla maszyn wirtualnych](vminsights-overview.md) to [szczegółowe informacje](insights-overview.md) w usłudze Azure Monitor, która jest podstawowym narzędziem do monitorowania maszyn wirtualnych w usłudze Azure Monitor. Zapewnia następującą dodatkową wartość w stosunku do standardowych funkcji usługi Azure Monitor.

- Uproszczone dołączanie agenta usługi Log Analytics i agenta zależności, aby umożliwić monitorowanie systemu operacyjnego gościa maszyny wirtualnej i obciążeń. 
- Wstępnie zdefiniowane wykresy wydajności trendów i skoroszyty, które umożliwiają analizowanie podstawowych metryk wydajności z systemu operacyjnego gościa maszyny wirtualnej.
- Mapa zależności, która wyświetla procesy uruchomione na każdej maszynie wirtualnej i połączone składniki z innymi maszynami i źródłami zewnętrznymi.

![Usługa Azure Monitor dla maszyn wirtualnych](media/monitor-vm-azure/vminsights-01.png)

![Usługa Azure Monitor dla maszyn wirtualnych](media/monitor-vm-azure/vminsights-02.png)


Włącz usługę Azure Monitor dla maszyn wirtualnych z opcji **Insights** w menu maszyny wirtualnej w witrynie Azure portal. Zobacz [Włącz usługę Azure Monitor dla maszyn wirtualnych omówienie](vminsights-enable-overview.md) szczegółów i innych metod konfiguracji.

![Włączanie usługi Azure Monitor dla maszyn wirtualnych](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Konfigurowanie obszaru roboczego usługi Log Analytics
Agent usługi Log Analytics używany przez usługę Azure Monitor dla maszyn wirtualnych wysyła dane do [obszaru roboczego usługi Log Analytics.](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured) Można włączyć zbieranie dodatkowych danych wydajności, zdarzeń i innych danych monitorowania z agenta, konfigurując obszar roboczy usługi Log Analytics. Wystarczy skonfigurować tylko raz, ponieważ każdy agent łączący się z obszarem roboczym automatycznie pobierze konfigurację i natychmiast rozpocznie zbieranie zdefiniowanych danych. 

Dostęp do konfiguracji obszaru roboczego można uzyskać bezpośrednio z usługi Azure Monitor dla maszyn wirtualnych, wybierając **konfigurację obszaru roboczego** z **programu Wprowadzenie**. Kliknij nazwę obszaru roboczego, aby otworzyć jego menu.

![Konfiguracja obszaru roboczego](media/monitor-vm-azure/workspace-configuration.png)

Wybierz **polecenie Ustawienia zaawansowane** z menu obszaru roboczego, a następnie pozycję **Dane,** aby skonfigurować źródła danych. W przypadku agentów systemu Windows wybierz pozycję **Dzienniki zdarzeń systemu Windows** i dodaj typowe dzienniki zdarzeń, takie jak *System* i *Aplikacja*. W przypadku agentów systemu Linux wybierz **pozycję Syslog** i dodaj typowe udogodnienia, takie jak *kern* i *demon*. Zobacz [źródła danych agenta w usłudze Azure Monitor,](../platform/agent-data-sources.md) aby uzyskać listę dostępnych źródeł danych i szczegółowe informacje na temat konfigurowania ich. 

![Konfigurowanie zdarzeń](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Można skonfigurować liczniki wydajności do zbierania z konfiguracji obszaru roboczego, ale może to być nadmiarowe z liczników wydajności zebranych przez usługę Azure Monitor dla maszyn wirtualnych. Usługa Azure Monitor dla maszyn wirtualnych zbiera najbardziej typowy zestaw liczników z częstotliwością raz na minutę. Skonfiguruj liczniki wydajności tylko do zbierania przez obszar roboczy, jeśli chcesz zbierać liczniki, które nie zostały jeszcze zebrane przez usługę Azure Monitor dla maszyn wirtualnych lub jeśli masz istniejące kwerendy przy użyciu danych o wydajności.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Włącz rozszerzenie diagnostyki i agent Telegraf
Usługa Azure Monitor dla maszyn wirtualnych jest oparta na agencie usługi Log Analytics, który zbiera dane w obszarze roboczym usługi Log Analytics. Obsługuje wiele [funkcji usługi Azure Monitor,](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) takich jak [zapytania dziennika,](../log-query/log-query-overview.md) [alerty dziennika](../platform/alerts-log.md)i [skoroszyty.](../platform/workbooks-overview.md) [Rozszerzenie diagnostyki](../platform/diagnostics-extension-overview.md) zbiera dane dotyczące wydajności z systemu operacyjnego gościa maszyn wirtualnych systemu Windows do usługi Azure Storage i opcjonalnie wysyła dane o wydajności do [metryk usługi Azure Monitor](../platform/data-platform-metrics.md). W przypadku maszyn wirtualnych systemu Linux [agent Telegraf](../platform/collect-custom-metrics-linux-telegraf.md) jest wymagany do wysyłania danych do metryk platformy Azure.  Dzięki temu inne funkcje usługi Azure Monitor, takie jak [eksplorator metryk](../platform/metrics-getting-started.md) i [alerty metryk.](../platform/alerts-metric.md) Można również skonfigurować rozszerzenie diagnostyki do wysyłania zdarzeń i danych wydajności poza usługą Azure Monitor przy użyciu usługi Azure Event Hubs.

Zainstaluj rozszerzenie diagnostyki dla pojedynczej maszyny wirtualnej systemu Windows w portalu Azure z opcji **ustawienia diagnostyki** w menu maszyny Wirtualnej. Wybierz opcję, aby włączyć **usługę Azure Monitor** na karcie **Ulewki.** Aby włączyć rozszerzenie z szablonu lub wiersza polecenia dla wielu maszyn wirtualnych, zobacz [Instalacja i konfiguracja](../platform/diagnostics-extension-overview.md#installation-and-configuration). W przeciwieństwie do agenta usługi Log Analytics dane do zebrania jest zdefiniowany w konfiguracji rozszerzenia na każdej maszynie wirtualnej.

![Ustawienie diagnostyczne](media/monitor-vm-azure/diagnostic-setting.png)

Zobacz [Instalowanie i konfigurowanie telegrafu, aby](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) uzyskać szczegółowe informacje na temat konfigurowania agentów Telegraf na maszynach wirtualnych systemu Linux. Opcja menu **ustawień diagnostycznych** jest dostępna dla systemu Linux, ale umożliwia wysyłanie danych tylko do magazynu platformy Azure.

### <a name="collect-platform-metrics-and-activity-log"></a>Zbieranie danych platformy i dziennika aktywności
Można wyświetlić metryki platformy i dziennik aktywności zebrane dla każdego hosta maszyny wirtualnej w witrynie Azure portal. Zbierz te dane w tym samym obszarze roboczym usługi Log Analytics co usługa Azure Monitor dla maszyn wirtualnych, aby analizować je z innymi danymi monitorowania zebranymi dla maszyny wirtualnej. Ta kolekcja jest skonfigurowana z [ustawieniem diagnostycznym](../platform/diagnostic-settings.md). Zbieranie dziennika aktywności z [ustawieniem diagnostycznym dla subskrypcji](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal).

Zbieranie metryk platformy z ustawieniem diagnostycznym dla maszyny wirtualnej. W przeciwieństwie do innych zasobów platformy Azure nie można utworzyć ustawienia diagnostycznego dla maszyny wirtualnej w witrynie Azure portal, ale należy użyć [innej metody.](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell) Poniższe przykłady pokazują, jak zbierać metryki dla maszyny wirtualnej przy użyciu programu PowerShell i interfejsu wiersza polecenia.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace

```

## <a name="monitoring-in-the-azure-portal"></a>Monitorowanie w witrynie Azure portal 
Po skonfigurowaniu zbierania danych monitorowania dla maszyny wirtualnej, masz wiele opcji dostępu do niego w witrynie Azure portal:

- Użyj menu **Usługi Azure Monitor,** aby uzyskać dostęp do danych ze wszystkich monitorowanych zasobów. 
- Użyj usługi Azure Monitor dla maszyn wirtualnych do monitorowania zestawów maszyn wirtualnych na dużą skalę.
- Analizowanie danych dla pojedynczej maszyny wirtualnej z jej menu w witrynie Azure portal. W poniższej tabeli wymieniono różne opcje monitorowania menu maszyn wirtualnych.

![Monitorowanie w witrynie Azure portal](media/monitor-vm-azure/monitor-menu.png)

| Opcja menu | Opis |
|:---|:---|
| Omówienie | Wyświetla [metryki platformy](../platform/data-platform-metrics.md) dla hosta maszyny wirtualnej. Kliknij wykres, aby pracować z tymi danymi w [Eksploratorze metryk](../platform/metrics-getting-started.md). |
| Dziennik aktywności | Wpisy [dziennika aktywności](../platform/activity-log-view.md) filtrowane dla bieżącej maszyny wirtualnej. |
| Insights | Otwiera [usługę Azure Monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) z mapą dla bieżącej maszyny wirtualnej wybranej. |
| Alerty | Widoki [alertów](../platform/alerts-overview.md) dla bieżącej maszyny wirtualnej.  |
| Metryki | Otwórz [eksploratora metryk](../platform/metrics-getting-started.md) z zakresem ustawionym na bieżącą maszynę wirtualną. |
| Ustawienia diagnostyczne | Włącz i skonfiguruj [rozszerzenie diagnostyki](../platform/diagnostics-extension-overview.md) dla bieżącej maszyny wirtualnej. |
| Zalecenia doradcy | Zalecenia dotyczące bieżącej maszyny wirtualnej z [usługi Azure Advisor](/azure/advisor/). |
| Dzienniki | Otwórz [usługi Log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) z [zakresem](../log-query/scope.md) ustawionym na bieżącą maszynę wirtualną. |
| Monitor połączenia | Otwórz [Monitor połączeń obserwatora sieci,](../../network-watcher/connection-monitor-preview.md) aby monitorować połączenia między bieżącą maszyną wirtualną a innymi maszynami wirtualnymi. |


## <a name="analyzing-metric-data"></a>Analizowanie danych metrycznych
Metryki dla maszyn wirtualnych można analizować przy użyciu eksploratora metryk, otwierając **metryki** z menu maszyny wirtualnej. Zobacz [Wprowadzenie do Eksploratora metryk platformy Azure, aby](../platform/metrics-getting-started.md) uzyskać szczegółowe informacje na temat korzystania z tego narzędzia. 

Istnieją dwa obszary nazw używane przez maszyny wirtualne dla metryk:

| Przestrzeń nazw | Opis |
|:---|:---|
| Host maszyny wirtualnej | Metryki hosta są automatycznie zbierane dla wszystkich maszyn wirtualnych platformy Azure. Szczegółowa lista metryk w witrynie [Microsoft.Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). |
| Gość maszyny wirtualnej | Metryki systemu operacyjnego gościa zebrane z maszyn wirtualnych z rozszerzeniem diagnostyki zainstalowany i skonfigurowany do wysyłania do ujścia usługi Azure Monitor. |

![Metryki](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analizowanie danych dziennika
Maszyny wirtualne platformy Azure będą zbierać następujące dane do dzienników usługi Azure Monitor. 

Usługa Azure Monitor dla maszyn wirtualnych umożliwia zbieranie wstępnie określony zestaw liczników wydajności, które są zapisywane w *insightsmetrics* tabeli. Jest to ta sama tabela używana przez [usługę Azure Monitor for Containers](container-insights-overview.md). 

| Źródło danych | Wymagania | Tabele |
|:---|:---|:---|
| Usługa Azure Monitor dla maszyn wirtualnych | Włącz na każdej maszynie wirtualnej. | Wskaźniki insights<br>VMBoundPort (Port wirtualny)<br>VMComputer (VMComputer)<br>Połączenie VMConnection<br>VMProcess<br>Zobacz [Jak kwerendy dzienników z usługi Azure Monitor dla maszyn wirtualnych, aby](vminsights-log-search.md) uzyskać szczegółowe informacje. |
| Dziennik aktywności | Ustawienie diagnostyczne dla subskrypcji. | AzureActivity |
| Dane hosta | Ustawienie diagnostyczne maszyny wirtualnej. | AzureMetrics |
| Źródła danych z systemu operacyjnego gościa | Włącz agenta usługi Log Analytics i skonfiguruj źródła danych. | Zobacz dokumentację dla każdego źródła danych. |


> [!NOTE]
> Dane dotyczące wydajności zbierane przez agenta usługi Log Analytics zapisuje w tabeli *Perf,* podczas gdy usługa Azure Monitor dla maszyn wirtualnych będzie zbierać je do *insightsmetrics* tabeli. Są to te same dane, ale tabele mają inną strukturę. Jeśli masz istniejące kwerendy oparte na *Perf,* trzeba będzie przepisać, aby użyć *InsightsMetrics*.


## <a name="alerts"></a>Alerty
[Alerty](../platform/alerts-overview.md) w usłudze Azure Monitor proaktywnie powiadamiają o znalezieniu ważnych warunków w danych monitorowania i potencjalnie uruchamiają akcję, taką jak uruchamianie aplikacji logiki lub wywoływanie elementu webhook. Reguły alertów definiują logikę używaną do określenia, kiedy należy utworzyć alert. Usługa Azure Monitor zbiera dane używane przez reguły alertów, ale należy utworzyć reguły do definiowania warunków alertów w subskrypcji platformy Azure.

W poniższych sekcjach opisano typy reguł alertów i zalecenia dotyczące tego, kiedy należy ich używać. To zalecenie jest oparte na funkcjonalności i koszcie typu reguły alertu. Aby uzyskać szczegółowe informacje na temat cen alertów, zobacz [Cennik usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Reguły alertów dziennika aktywności
[Reguły alertów dziennika aktywności](../platform/alerts-activity-log.md) są uruchamiane po utworzeniu wpisu spełniających określone kryteria w dzienniku działań. Nie mają żadnych kosztów, więc powinny być pierwszym wyborem, jeśli logika, której potrzebujesz, znajduje się w dzienniku aktywności. 

Zasób docelowy dla alertów dziennika aktywności może być określoną maszyną wirtualną, wszystkimi maszynami wirtualnymi w grupie zasobów lub wszystkimi maszynami wirtualnymi w ramach subskrypcji.

Na przykład utwórz alert, jeśli krytyczna maszyna wirtualna zostanie zatrzymana, wybierając opcję *Wyłączanie maszyny wirtualnej* dla nazwy sygnału.

![Alert dziennika aktywności](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Reguły alertów metryk
[Reguły alertu metryki](../platform/alerts-metric.md) są uruchamiane, gdy wartość metryki przekracza próg. Można zdefiniować określoną wartość progową lub zezwolić platformie Azure Monitor dynamicznie określać próg na podstawie danych historycznych.  Używaj alertów metryk, gdy tylko jest to możliwe, korzystając z danych metryk, ponieważ kosztują mniej i są bardziej responsywne niż reguły alertów dziennika. Są one również stateful znaczenie będą rozwiązywać się, gdy metryka spadnie poniżej progu.

Zasób docelowy dla alertów dziennika aktywności może być określoną maszyną wirtualną lub wszystkimi maszynami wirtualnymi w grupie zasobów.

Na przykład, aby utworzyć alert, gdy procesor maszyny wirtualnej przekracza określoną wartość, należy utworzyć regułę alertu metryki przy użyciu *procesora CPU procentowego* jako typu sygnału. Ustaw określoną wartość progową lub zezwalaj monitorowi Azure Monitor na ustawienie progu dynamicznego. 

![Alert metryczny](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Alerty dotyczące dzienników
[Reguły alertu dziennika](../platform/alerts-log.md) są uruchamiane, gdy wyniki zaplanowanej kwerendy dziennika są zgodne z określonymi kryteriami. Alerty kwerend dziennika są najdroższe i najmniej responsywne reguł alertów, ale mają dostęp do najbardziej zróżnicowanych danych i mogą wykonywać złożoną logikę, która nie może być wykonywana przez inne reguły alertów. 

Zasób docelowy dla kwerendy dziennika jest obszar roboczy usługi Log Analytics. Filtrowanie dla określonych komputerów w kwerendzie.

Na przykład, aby utworzyć alert, który sprawdza, czy wszystkie maszyny wirtualne w określonej grupie zasobów są w trybie offline, należy użyć następującej kwerendy, która zwraca rekord dla każdego komputera, który nie odebrano pulsu w ciągu ostatnich dziesięciu minut. Użyj progu 1, który uruchamia się, jeśli co najmniej jeden komputer ma pominięte bicie serca.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Alert dziennika](media/monitor-vm-azure/log-alert-01.png)

Aby utworzyć alert, jeśli na maszynach wirtualnych systemu Windows w ramach subskrypcji wystąpiła nadmierna liczba nieudanych logów, należy użyć następującej kwerendy, która zwraca rekord dla każdego zdarzenia logowania, które nie powiodło się w ciągu ostatniej godziny. Użyj progu ustawionego na liczbę nieudanych logowania, które pozwolisz. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![Alert dziennika](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
Program System Center Operations Manager (SCOM) zapewnia szczegółowe monitorowanie obciążeń na maszynach wirtualnych. Zobacz [Przewodnik monitorowania chmury](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/) dla porównania platform monitorowania i różnych strategii implementacji.

Jeśli masz istniejące środowisko SCOM, które zamierzasz nadal używać, możesz zintegrować je z usługą Azure Monitor, aby zapewnić dodatkowe funkcje. Agent usługi Log Analytics używany przez usługę Azure Monitor jest tym samym, który jest używany dla usługi SCOM, dzięki czemu monitorowane maszyny wirtualne wysyłają dane do obu. Nadal trzeba dodać agenta do usługi Azure Monitor dla maszyn wirtualnych i skonfigurować obszar roboczy do zbierania dodatkowych danych, jak określono powyżej, ale maszyny wirtualne mogą nadal uruchamiać istniejące pakiety administracyjne w środowisku SCOM bez modyfikacji.

Funkcje usługi Azure Monitor, które rozszerzają istniejące funkcje SCOM, są następujące:

- Użyj usługi Log Analytics do interaktywnej analizy danych dziennika i wydajności.
- Alerty dziennika umożliwiają definiowanie warunków alertów na wielu maszynach wirtualnych i używanie długoterminowych trendów, które nie są możliwe przy użyciu alertów w środowisku SCOM.   

Zobacz [Łączenie programu Operations Manager z usługą Azure Monitor, aby](../platform/om-agents.md) uzyskać szczegółowe informacje na temat łączenia istniejącej grupy zarządzania SCOM z obszarem roboczym usługi Log Analytics.


## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak analizować dane w dziennikach usługi Azure Monitor przy użyciu zapytań dziennika.](../log-query/get-started-queries.md)
* [Dowiedz się więcej o alertach przy użyciu metryk i dzienników w usłudze Azure Monitor.](../platform/alerts-overview.md)

