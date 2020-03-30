---
title: Omówienie agentów monitorowania platformy Azure| Dokumenty firmy Microsoft
description: Ten artykuł zawiera szczegółowe omówienie dostępnych agentów platformy Azure, którzy obsługują monitorowanie maszyn wirtualnych hostowanych na platformie Azure lub w środowisku hybrydowym.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249089"
---
# <a name="overview-of-azure-monitor-agents"></a>Omówienie agentów usługi Azure Monitor

Maszyny wirtualne i inne zasoby obliczeniowe wymagają agenta do zbierania danych monitorowania do pomiaru wydajności i dostępności ich systemu operacyjnego gościa i obciążeń. W tym artykule opisano agentów używanych przez usługę Azure Monitor i pomaga określić, które należy spełnić wymagania dla danego środowiska.

> [!NOTE]
> Usługa Azure Monitor ma obecnie wielu agentów z powodu niedawnej konsolidacji usługi Azure Monitor i usługa Log Analytics. Chociaż mogą występować w ich funkcji, każdy ma unikatowe możliwości. W zależności od wymagań może być potrzebny jeden lub więcej agentów na maszynach wirtualnych. 

Może mieć określony zestaw wymagań, które nie mogą być całkowicie spełnione z jednego agenta dla określonej maszyny wirtualnej. Na przykład można użyć alertów metryk, które wymagają rozszerzenia diagnostyki platformy Azure, ale również chcesz korzystać z funkcji usługi Azure Monitor dla maszyn wirtualnych, która wymaga agenta usługi Log Analytics i agenta zależności. W takich przypadkach można użyć wielu agentów i jest to typowy scenariusz dla klientów, którzy wymagają funkcji od każdego.

## <a name="summary-of-agents"></a>Podsumowanie agentów

Poniższe tabele zawierają szybkie porównanie agentów usługi Azure Monitor dla systemów Windows i Linux. Więcej szczegółów na temat każdego z nich znajduje się w poniższej sekcji. 

### <a name="windows-agents"></a>Agenci dla systemu Windows

| | Diagnostyka<br>rozszerzenie (WAD) | Log Analytics<br>Agenta | Zależność<br>Agenta |
|:---|:---|:---|:---|
| Obsługiwane środowiska | Azure | Azure<br>Inna chmura<br>Lokalnie | Azure<br>Inna chmura<br>Lokalnie | 
| Wymagania dotyczące agenta  | Brak | Brak | Wymaga agenta analizy dzienników |
| Zbierane dane | Dzienniki zdarzeń<br>zdarzenia ETW<br>Wydajność<br>Dzienniki oparte na plikach<br>Dzienniki usług IIS<br>Dzienniki aplikacji .NET<br>Zrzuty awaryjne<br>Dzienniki diagnostyki agenta | Dzienniki zdarzeń<br>Wydajność<IIS logs><br>Dzienniki oparte na plikach<br>Spostrzeżenia i rozwiązania<br>Inne usługi | Szczegóły procesu i zależności<br>Metryki połączenia sieciowego |
| Dane wysyłane do | Azure Storage<br>Metryki usługi Azure Monitor<br>Centrum zdarzeń | Dzienniki usługi Azure Monitor | Dzienniki usługi Azure Monitor |


### <a name="linux-agents"></a>Agenci dla systemu Linux

| | Diagnostyka<br>rozszerzenie (LAD) | Telegraf<br>Agenta | Log Analytics<br>Agenta | Zależność<br>Agenta |
|:---|:---|:---|:---|:---|
| Obsługiwane środowiska | Azure | Azure<br>Inna chmura<br>Lokalnie | Azure<br>Inna chmura<br>Lokalnie | Azure<br>Inna chmura<br>Lokalnie |
| Wymagania dotyczące agenta  | Brak | Brak | Brak | Wymaga agenta analizy dzienników |
| Zbierane dane | Dziennik systemu<br>Wydajność | Wydajność | Dziennik systemu<br>Wydajność| Szczegóły procesu i zależności<br>Metryki połączenia sieciowego |
| Dane wysyłane do | Azure Storage<br>Centrum zdarzeń | Metryki usługi Azure Monitor | Dzienniki usługi Azure Monitor | Dzienniki usługi Azure Monitor |

## <a name="log-analytics-agent"></a>Agent usługi Log Analytics

[Agent usługi Log Analytics](log-analytics-agent.md) zbiera dane monitorowania z systemu operacyjnego gościa i obciążeń maszyn wirtualnych na platformie Azure, innych dostawców chmury i lokalnie. Zbiera dane do obszaru roboczego usługi Log Analytics. Agent usługi Log Analytics jest tym samym agentem używanym przez program System Center Operations Manager i można jednocześnie komunikować się z grupą zarządzania i usługą Azure Monitor na komputerach z wieloma urządzeniami. Ten agent jest również wymagane przez niektóre szczegółowe informacje i rozwiązania w usłudze Azure Monitor.


> [!NOTE]
> Agent analizy dzienników dla systemu Windows jest często określany jako Agent monitorowania firmy Microsoft (MMA). Agent analizy dzienników dla systemu Linux jest często określany jako agent OMS.



Użyj agenta usługi Log Analytics, jeśli potrzebujesz:

* Zbieranie dzienników i danych o wydajności z maszyn wirtualnych lub fizycznych poza platformą Azure. 
* Wysyłaj dane do obszaru roboczego usługi Log Analytics, aby korzystać z funkcji obsługiwanych przez [dzienniki monitora platformy Azure,](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) takich jak [zapytania dziennika.](../log-query/log-query-overview.md)
* Użyj [usługi Azure Monitor dla maszyn wirtualnych,](../insights/vminsights-overview.md) która umożliwia monitorowanie maszyn wirtualnych na dużą skalę i monitoruje ich procesy i zależności od innych zasobów i procesów zewnętrznych.Use Azure Monitor for VMs which allows you to monitor your virtual machines at scale and monitors their processes and dependencs on other resources and external processes.Use Azure Monitor for VMs which allows you to monitor your virtual machines at scale and monitors their processes and dependencs on other resources and external processes.Use Azure Monitor  
* Zarządzaj zabezpieczeniami maszyn wirtualnych za pomocą [usługi Azure Security Center](../../security-center/security-center-intro.md) lub usługi Azure [Sentinel.](../../sentinel/overview.md)
* Korzystaj z [zarządzania usługą Azure Automation Update,](../../automation/automation-update-management.md) [konfiguracji stanu automatyzacji platformy Azure](../../automation/automation-dsc-overview.md)lub śledzenia zmian [automatyzacji platformy Azure i zasobów reklamowych,](../../automation/change-tracking.md) aby zapewnić kompleksowe zarządzanie maszynami wirtualnymi platformy Azure
* Użyj różnych [rozwiązań](../monitor-reference.md#insights-and-core-solutions) do monitorowania określonej usługi lub aplikacji.

Ograniczenia agenta usługi Log Analytics obejmują:

- Nie można wysyłać danych do metryk usługi Azure Monitor, usługi Azure Storage lub usługi Azure Event Hubs.

## <a name="azure-diagnostics-extension"></a>Rozszerzenie diagnostyki platformy Azure

[Rozszerzenie diagnostyki platformy Azure](diagnostics-extension-overview.md) zbiera dane monitorowania z systemu operacyjnego gościa i obciążeń maszyn wirtualnych platformy Azure i innych zasobów obliczeniowych. Zbiera dane głównie do usługi Azure Storage, ale także umożliwia definiowanie pochłaniacze danych, aby również wysyłać dane do innych miejsc docelowych, takich jak Metryki usługi Azure Monitor i usługi Azure Event Hubs.

Użyj rozszerzenia diagnostycznego platformy Azure, jeśli chcesz:

- Wysyłaj dane do usługi Azure Storage w celu archiwizacji lub analizowania ich za pomocą narzędzi, takich jak [Eksplorator usługi Azure Storage.](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
- Wysyłaj dane do [metryk usługi Azure Monitor,](data-platform-metrics.md) aby analizować je za pomocą [Eksploratora metryk](metrics-getting-started.md) i korzystać z funkcji, takich jak [alerty metryk](../../azure-monitor/platform/alerts-metric-overview.md) w czasie zbliżonym do rzeczywistego i [skalowanie automatyczne](autoscale-overview.md) (tylko system Windows).
- Wysyłanie danych do narzędzi innych firm przy użyciu [usługi Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
- Zbieranie [diagnostyki rozruchu](../../virtual-machines/troubleshooting/boot-diagnostics.md) w celu zbadania problemów z rozruchem maszyny Wirtualnej.

Ograniczenia rozszerzenia diagnostyki platformy Azure obejmują:

- Może być używany tylko z zasobami platformy Azure.
- Ograniczona możliwość wysyłania danych do dzienników usługi Azure Monitor.

## <a name="telegraf-agent"></a>Agent Telegraf

[Agent InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) służy do zbierania danych o wydajności z komputerów z systemem Linux do metryk usługi Azure Monitor.

Użyj agenta Telegraf, jeśli potrzebujesz:

* Wysyłaj dane do [metryk usługi Azure Monitor,](data-platform-metrics.md) aby analizować je za pomocą [Eksploratora metryk](metrics-getting-started.md) i korzystać z funkcji, takich jak [alerty metryk](../../azure-monitor/platform/alerts-metric-overview.md) w czasie zbliżonym do rzeczywistego i [skalowanie automatyczne](autoscale-overview.md) (tylko system Linux). 



## <a name="dependency-agent"></a>Agent zależności

Agent zależności zbiera odnalezione dane dotyczące procesów uruchomionych na maszynie wirtualnej i zależnościach procesu zewnętrznego. 

Użyj agenta zależności, jeśli chcesz:

* Użyj funkcji mapy [Usługi Azure Monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) lub rozwiązania mapy [usług.](../insights/service-map.md)

Należy wziąć pod uwagę następujące podczas korzystania z agenta zależności:

- Agent zależności wymaga agenta usługi Log Analytics do zainstalowania na tej samej maszynie wirtualnej.
- Na maszynach wirtualnych z systemem Linux agent usługi Log Analytics musi zostać zainstalowany przed rozszerzeniem diagnostyki platformy Azure.

## <a name="extensions-compared-to-agents"></a>Rozszerzenia w porównaniu z agentami

Rozszerzenie usługi Log Analytics dla [systemów Windows](../../virtual-machines/extensions/oms-windows.md) i [Linux](../../virtual-machines/extensions/oms-linux.md) zainstalować agenta usługi Log Analytics na maszynach wirtualnych platformy Azure. Rozszerzenie zależności monitora platformy Azure dla [systemów Windows](../../virtual-machines/extensions/agent-dependency-windows.md) i [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) zainstalować agenta zależności na maszynach wirtualnych platformy Azure. Są to te same czynniki opisane powyżej, ale pozwalają zarządzać nimi za pośrednictwem [rozszerzeń maszyn wirtualnych.](../../virtual-machines/extensions/overview.md) Rozszerzenia należy używać do instalowania agentów i zarządzania nimi, gdy tylko jest to możliwe.


## <a name="next-steps"></a>Następne kroki

Uzyskaj więcej informacji na temat każdego z agentów, o następujących kwestiach:

- [Omówienie agenta usługi Log Analytics](log-analytics-agent.md)
- [Omówienie rozszerzenia diagnostyki platformy Azure](diagnostics-extension-overview.md)
- [Zbieranie niestandardowych metryk dla maszyny Wirtualnej z systemem Linux za pomocą agenta InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md)
