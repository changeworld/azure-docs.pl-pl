---
title: Omówienie agentów monitorowania platformy Azure | Microsoft Docs
description: Ten artykuł zawiera szczegółowe omówienie dostępnych agentów platformy Azure, które obsługują monitorowanie maszyn wirtualnych hostowanych na platformie Azure lub w środowisku hybrydowym.
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: a01258799efa81c8d3ddba398facaa90c24c2513
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150029"
---
# <a name="overview-of-the-azure-monitor-agents"></a>Omówienie agentów Azure Monitor 
Zasoby obliczeniowe, takie jak maszyny wirtualne, generują dane w celu monitorowania ich wydajności i dostępności, podobnie jak w przypadku [innych zasobów w chmurze](../insights/monitor-azure-resource.md). Zasoby obliczeniowe, chociaż mają również system operacyjny gościa i obciążenia, które muszą być monitorowane. Zbieranie danych monitorowania z wewnątrz zasobu wymaga agenta. W tym artykule opisano agentów używanych przez Azure Monitor i można określić, które z nich muszą spełniać wymagania dotyczące konkretnego środowiska.

## <a name="summary-of-agents"></a>Podsumowanie agentów

> [!NOTE]
> Azure Monitor ma obecnie wielu agentów z powodu ostatniej konsolidacji Azure Monitor i Log Analytics. Oba agenci udostępniają pewne możliwości, a inne funkcje są unikatowe dla określonego agenta. W zależności od wymagań może być potrzebny jeden z agentów. 

Azure Monitor ma trzech agentów, którzy mają określone funkcje. W zależności od wymagań można zainstalować jednego agenta lub wiele na maszynach wirtualnych i innych zasobach obliczeniowych.

* [Diagnostyka Azure rozszerzenie](#azure-diagnostic-extension)
* [Agent Log Analytics](#log-analytics-agent)
* [Agent zależności](#dependency-agent)

Poniższa tabela zawiera krótkie porównanie różnych agentów. Zapoznaj się z resztą tego artykułu, aby uzyskać szczegółowe informacje o każdej z nich.

| | Rozszerzenie diagnostyki platformy Azure | Agent Log Analytics | Agent zależności |
|:---|:---|:---|:---|
| Obsługiwane środowiska | Azure | Azure<br>Inna chmura<br>Lokalnie | Azure<br>Inna chmura<br>Lokalnie |
| Systemy operacyjne | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| Zależności agentów  | Brak | Brak | Wymaga agenta Log Analytics |
| Zebrane dane | Dzienniki zdarzeń<br>Zdarzenia ETW<br>Dziennik systemu<br>Wydajność<br>Dzienniki usług IIS<br>Dzienniki wyjściowe śledzenia aplikacji platformy .NET<br>Zrzuty awaryjne | Dzienniki zdarzeń<br>Dziennik systemu<br>Wydajność<br>Dzienniki usług IIS<br>Niestandardowe dzienniki<br>Dane z rozwiązań | Szczegóły procesu i zależności<br>Metryki połączenia sieciowego |
| Dane wysyłane do | Azure Storage<br>Metryki Azure Monitor<br>Centrum zdarzeń | Dzienniki usługi Azure Monitor | Dzienniki usługi Azure Monitor |



## <a name="azure-diagnostic-extension"></a>Rozszerzenie diagnostyki platformy Azure
[Rozszerzenie Diagnostyka Azure](../../azure-monitor/platform/diagnostics-extension-overview.md) zbiera dane monitorowania z systemu operacyjnego gościa i obciążeń zasobów obliczeniowych platformy Azure. Głównie zbiera dane do usługi Azure Storage. Azure Monitor można skonfigurować do kopiowania danych z magazynu do Log Analytics obszaru roboczego. Możesz również zbierać dane dotyczące wydajności gościa w metrykach Azure Monitor.

Rozszerzenie diagnostyki platformy Azure jest często określane jako rozszerzenie diagnostyki systemu Windows Azure (funkcji wad) lub Linux Azure (LAD).


### <a name="scenarios-supported"></a>Obsługiwane scenariusze

Scenariusze obsługiwane przez rozszerzenie Diagnostyka Azure obejmują następujące elementy:

* Zbieraj dzienniki i dane wydajności z zasobów obliczeniowych platformy Azure.
* Archiwizuj dzienniki i dane wydajności z systemu operacyjnego gościa w usłudze Azure Storage.
* Wyświetlanie danych monitorowania w magazynie przy użyciu narzędzia, takiego jak [Eksplorator usługi Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
* Zbieraj dane wydajności w bazie danych metryk, aby korzystać z funkcji obsługiwanych przez [Azure monitor metryki](data-platform-metrics.md) , takich jak [alerty metryk](../../azure-monitor/platform/alerts-metric-overview.md) niemal w czasie rzeczywistym i [Automatyczne skalowanie](autoscale-overview.md). 
* Zbierz dane monitorowania z [magazynu do obszaru roboczego log Analytics](azure-storage-iis-table.md) , aby korzystać z funkcji obsługiwanych przez [dzienniki Azure monitor](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , takie jak [zapytania dziennika](../log-query/log-query-overview.md).
* Wysyłaj dane monitorowania do narzędzi innych firm przy użyciu [usługi Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
* Zbadaj problemy z rozruchem maszyny wirtualnej, korzystając z [diagnostyki rozruchu](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Skopiuj dane z aplikacji działających na maszynie wirtualnej [, aby Application Insights](diagnostics-extension-to-application-insights.md) integrację z innym monitorowaniem aplikacji.

## <a name="log-analytics-agent"></a>Agent Log Analytics
[Agent log Analytics](log-analytics-agent.md) zbiera dane monitorowania z systemu operacyjnego gościa i obciążeń maszyn wirtualnych na platformie Azure, innych dostawców chmury i lokalnie. Gromadzi dane w obszarze roboczym Log Analytics.

Agent Log Analytics jest tym samym agentem, który jest używany przez System Center Operations Manager, oraz komputery z wieloma domową do komunikacji z grupą zarządzania i Azure Monitor jednocześnie. Ten agent jest również wymagany przez niektóre rozwiązania w Azure Monitor.

Agent Log Analytics dla systemu Windows jest często określany jako Microsoft Management Agent (MMA). Agent Log Analytics dla systemu Linux jest często nazywany agentem pakietu OMS.


### <a name="scenarios-supported"></a>Obsługiwane scenariusze

Scenariusze obsługiwane przez agenta Log Analytics są następujące:

* Zbieraj dzienniki i dane wydajności z maszyn wirtualnych na platformie Azure, innych dostawców chmury i lokalnie. 
* Zbierz dane monitorowania do obszaru roboczego Log Analytics, aby korzystać z funkcji obsługiwanych przez [dzienniki Azure monitor](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , takie jak [zapytania dziennika](../log-query/log-query-overview.md).
* Używaj rozwiązań do monitorowania Azure Monitor, takich jak [Azure monitor dla maszyn wirtualnych](../insights/vminsights-overview.md), [Azure monitor dla kontenerów](../insights/container-insights-overview.md)itp.  
* Zarządzaj zabezpieczeniami maszyn wirtualnych za pomocą funkcji [wskaźnikowej platformy Azure](../../sentinel/overview.md) , która wymaga agenta log Analytics.
* Zarządzaj zabezpieczeniami maszyn wirtualnych przy użyciu [Azure Security Center](../../security-center/security-center-intro.md) , które wymagają agenta log Analytics.
* Korzystaj z funkcji programu [Azure Automation](../../automation/automation-intro.md) , aby zapewnić kompleksowe zarządzanie maszynami wirtualnymi platformy Azure przez ich cykl życia.  Przykłady tych funkcji, które wymagają agenta Log Analytics, obejmują:
  * [Azure Automation zarządzanie aktualizacjami](../../automation/automation-update-management.md) systemu operacyjnego.
  * [Azure Automation konfigurację stanu](../../automation/automation-dsc-overview.md) , aby zachować spójność stanu konfiguracji.
  * Śledź zmiany konfiguracji przy użyciu [Azure Automation Change Tracking i spisu](../../automation/change-tracking.md).

## <a name="dependency-agent"></a>Agent zależności
Agent zależności zbiera odnalezione dane dotyczące procesów uruchomionych na maszynie wirtualnej i zewnętrznych zależności procesów. Ten agent jest wymagany do [Service map](../insights/service-map.md) i [Azure monitor dla maszyn wirtualnych](../insights/vminsights-overview.md)funkcji mapy. Agent zależności wymaga agenta Log Analytics i zapisuje dane w obszarze roboczym Log Analytics w Azure Monitor.


## <a name="using-multiple-agents"></a>Korzystanie z wielu agentów
Użytkownik może mieć określone wymagania, aby użyć rozszerzenia diagnostyki platformy Azure lub agenta Log Analytics dla określonej maszyny wirtualnej. Na przykład możesz chcieć użyć alertów metryk, które wymagają rozszerzenia diagnostyki platformy Azure. Możesz również użyć funkcji map Azure Monitor dla maszyn wirtualnych, która wymaga agenta zależności i agenta Log Analytics. W takim przypadku można użyć wielu agentów i jest to typowy scenariusz dla klientów, którzy wymagają funkcjonalności z każdego z nich.

### <a name="considerations"></a>Zagadnienia do rozważenia

- Agent zależności wymaga zainstalowania agenta Log Analytics na tej samej maszynie wirtualnej.
- Na maszynach wirtualnych z systemem Linux należy zainstalować agenta Log Analytics przed rozszerzeniem diagnostyki platformy Azure.


## <a name="next-steps"></a>Następne kroki

- Zobacz [Omówienie agenta log Analytics](../../azure-monitor/platform/log-analytics-agent.md) , aby przejrzeć wymagania i obsługiwane metody wdrażania agenta na maszynach hostowanych na platformie Azure, w centrum danych lub w innym środowisku chmury.

