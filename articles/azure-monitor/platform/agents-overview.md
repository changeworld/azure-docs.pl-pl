---
title: Omówienie usługi Azure, agentów monitorowania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera szczegółowe omówienie agentów programu Azure dostępne, które obsługują monitorowanie maszyn wirtualnych hostowanych w środowisku platformy Azure lub hybrydowego.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: magoedte
ms.openlocfilehash: 89e73a4578134493a25ec3cbd2385433a2b36156
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003502"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Omówienie usługi Azure, agentów monitorowania 
Microsoft Azure oferuje wiele sposobów, aby zbierać różne typy danych z maszyn wirtualnych z systemem Microsoft Windows i Linux hostowanych na platformie Azure, centrum danych lub innych dostawców rozwiązań w chmurze. Są trzy typy dostępnych do monitorowania maszyn wirtualnych agentów:

* Rozszerzenia diagnostyki Azure
* Log Analytics Agent dla systemu Linux i Windows
* Agent zależności

W tym artykule opisano różnice między nimi i ich funkcje w kolejności, w celu ustalenia, który z nich będzie obsługiwać Twoje zarządzania usługami IT lub ogólnych wymagań dotyczących monitorowania.  

## <a name="azure-diagnostic-extension"></a>Rozszerzenie diagnostyki platformy Azure
[Rozszerzenia diagnostyki Azure](../../azure-monitor/platform/diagnostics-extension-overview.md) (często nazywany rozszerzenia diagnostyki Azure Windows (WAD) lub diagnostyki Azure Linux (LAD)), które podano dla usług Azure Cloud Services, ponieważ stały się ogólnie dostępna w 2010 r. jest agent, który dostarcza proste zbierania danych diagnostycznych z zasób obliczeniowy platformy Azure, takich jak maszyny Wirtualnej i utrzymują się do usługi Azure storage. Gdy w usłudze storage została wybrana opcja wyświetlania przy użyciu jednego z kilku dostępnych narzędzi, takich jak [Eksploratora serwera w programie Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) i [Eksploratora usługi Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

Istnieje możliwość zbierania:

* Wstępnie zdefiniowany zestaw liczników wydajności systemu operacyjnego i dzienniki zdarzeń lub można określić, których mają być gromadzone. 
* Wszystkie żądania i/lub żądań zakończonych niepowodzeniem na serwerze sieci web usług IIS
* Aplikacja platformy .NET, dzienniki danych wyjściowych śledzenia
* Zdarzenia śledzenia zdarzeń systemu Windows (ETW) 
* Zbieranie zdarzeń dziennika z dziennika systemu  
* Zrzuty awaryjne 

Jeśli chcesz, należy użyć agenta funkcji Diagnostyka Azure:

* Archiwizowanie dzienników i metryk do usługi Azure storage
* Zintegruj dane monitorowania z narzędziami innych firm. Te narzędzia korzystają z różnych metod, w tym konto magazynu, przesyłane dalej do kwerendy [usługi Event Hubs](../../event-hubs/event-hubs-about.md), lub wykonywanie zapytań za pomocą [interfejsu API REST monitorowania platformy Azure](../../azure-monitor/platform/rest-api-walkthrough.md)
* Przekazywanie danych do usługi Azure Monitor, tworzenie wykresów metryki w witrynie Azure portal lub utworzyć w czasie zbliżonym do rzeczywistego [alertów dotyczących metryk](../../azure-monitor/platform/alerts-metric-overview.md). 
* Zestawy skalowania maszyn wirtualnych skalowania automatycznego i klasycznej usługi w chmurze w oparciu metryki systemu operacyjnego gościa.
* Badanie problemów dotyczących rozruchu maszyny Wirtualnej za pomocą [diagnostyki rozruchu](../../virtual-machines/troubleshooting/boot-diagnostics.md).
* Zrozumienie, jak działają Twoje aplikacje i aktywnie identyfikuje problemów wpływających na ich za pomocą [usługi Application Insights](../../azure-monitor/overview.md).
* Konfigurowanie usługi Azure Monitor, aby zaimportować metryki i rejestrowanie danych zebranych z usług w chmurze, klasyczne maszyny wirtualne, a węzłów usługi Service Fabric są przechowywane na koncie usługi Azure storage.

## <a name="log-analytics-agent"></a>Log Analytics agent
W przypadku zaawansowanego monitorowania, gdy potrzebujesz więcej niż zbieranie metryk i podzbiór dzienników, agenta usługi Log Analytics dla Windows (nazywane także jako Microsoft Monitoring Agent (MMA)) i systemu Linux jest wymagany. Agenta usługi Log Analytics został opracowany do kompleksowego zarządzania dla lokalnych maszyn fizycznych i wirtualnych, komputery monitorowane przez System Center Operations Manager i maszyn wirtualnych w hostowanej w innych chmurach. Agenci Windows i Linux nawiązują połączenie z obszarem roboczym usługi Log Analytics w usłudze Azure Monitor, aby zbierać zarówno dane oparte na rozwiązaniach monitorowania, jak i niestandardowymi źródłami danych, które można skonfigurować.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Jeśli chcesz, należy użyć agenta usługi Log Analytics:

* Zbieraj dane z różnych źródeł, zarówno w obrębie platformy Azure, innych dostawców rozwiązań w chmurze i zasobów lokalnych. 
* Przy użyciu jednej z usługi Azure Monitor, takich jak monitorowanie rozwiązań [usługi Azure Monitor dla maszyn wirtualnych](../insights/vminsights-overview.md), [usługi Azure Monitor dla kontenerów](../insights/container-insights-overview.md)itp.  
* Użyj jednej z innych usług zarządzania systemu Azure, takich jak [usługi Azure Security Center](../../security-center/security-center-intro.md), [usługi Azure Automation](../../automation/automation-intro.md)itp.

Wcześniej kilka usług platformy Azure były powiązane jako *pakietu Operations Management Suite*, a w rezultacie agenta usługi Log Analytics jest udostępniany w usługach w tym usługi Azure Security Center i Azure Automation.  Obejmuje to pełny zestaw funkcji, które oferują one, zapewniając kompleksowe zarządzanie całym ich cyklu życia maszynach wirtualnych platformy Azure.  Przedstawiono kilka przykładów:

* [Azure zarządzania aktualizacjami automatyzacji](../../automation/automation-update-management.md) aktualizacji systemu operacyjnego.
* [Usługi Azure Automation Desired State Configuration](../../automation/automation-dsc-overview.md) do zarządzania stanem spójnej konfiguracji.
* Śledź zmiany konfiguracji przy użyciu [spis i śledzenie zmian w usłudze Azure Automation](../../automation/automation-change-tracking.md).
* Usługi systemu Azure, takich jak [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/) i [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/), która natywnie przechowują swoje dane bezpośrednio w usłudze Log Analytics.  

## <a name="dependency-agent"></a>Agent zależności
Agent zależności został opracowany jako część rozwiązania mapy usługi, które pierwotnie nie został opracowany przez firmę Microsoft. [Usługa Service Map](../insights/service-map.md) i [usługi Azure Monitor dla maszyn wirtualnych](../insights/vminsights-overview.md) wymaga agenta zależności w Windows i Linux maszyn wirtualnych i integruje się z agentem usługi Log Analytics, aby zbieranie odnalezionych danych dotyczących procesów uruchomionych na wirtualny Maszyna i zależności w procesie zewnętrznym. Ona te dane są przechowywane w obszarze roboczym usługi Log Analytics i wizualizuje odnalezionych powiązanych elementów.

Może być konieczne kombinację tych agentów do monitorowania maszyny Wirtualnej. Agentów można zainstalować obok siebie jako rozszerzeń platformy Azure, jednak w systemie Linux, agenta usługi Log Analytics *musi* można zainstalować pierwszy lub — w przeciwnym razie instalacja zakończy się niepowodzeniem. 

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [omówienie agenta usługi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) Przegląd wymagań i obsługiwanych metod, aby wdrożyć agenta na maszynach hostowanych na platformie Azure, w centrum danych lub w innym środowisku chmury.

