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
ms.devlang: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: ce6f40e580595e4f3fbf0519aa1ba8be0355ded1
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621909"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Omówienie usługi Azure, agentów monitorowania 
Microsoft Azure oferuje wiele sposobów, aby zbierać różne typy danych z maszyn wirtualnych hostowanych na platformie Azure lub innych dostawców rozwiązań w chmurze systemem Microsoft Windows i Linux. Ten artykuł pomoże opisano różnice i możliwości dostępne z każdego agenta w kolejności, w celu ustalenia, który z nich będzie obsługiwać Twoje zarządzania usługami IT lub ogólne wymagania dotyczące monitorowania.  

## <a name="comparing-agents"></a>Porównywanie agentów
Już dziś na platformie Azure, istnieją trzy rodzaje agentów dostępnych do monitorowania maszyn wirtualnych platformy Azure — rozszerzenie diagnostyki platformy Azure, agenta zależności i Log Analytics Agent dla systemu Linux i Windows.  Zasadniczo rozszerzenie Azure zależności i agentów usługi Log Analytics są przeznaczone do zbierać metryki i dzienniki i przekazywać je do repozytorium. Jednak to, gdzie kończy się ich podobieństwa.  

### <a name="azure-diagnostic-extension"></a>Rozszerzenie diagnostyki platformy Azure
[Rozszerzenia diagnostyki Azure](../monitoring-and-diagnostics/azure-diagnostics.md) (często nazywany rozszerzenia diagnostyki Azure Windows (WAD) lub diagnostyki Azure Linux (LAD)), które podano dla usług Azure Cloud Services, ponieważ stały się ogólnie dostępna w 2010 r. jest agent, który dostarcza proste zbierania danych diagnostycznych z zasób obliczeniowy platformy Azure, takich jak maszyny Wirtualnej i utrzymują się do usługi Azure storage. Gdy w usłudze storage została wybrana opcja wyświetlania przy użyciu jednego z kilku dostępnych narzędzi, takich jak [Eksploratora serwera w programie Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) i [Eksploratora usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Istnieje możliwość zbierania:

* Wstępnie zdefiniowany zestaw liczników wydajności systemu operacyjnego i dzienniki zdarzeń lub można określić, których mają być gromadzone. 
* Wszystkie żądania i/lub żądań zakończonych niepowodzeniem na serwerze sieci web usług IIS
* Aplikacja platformy .NET, dzienniki danych wyjściowych śledzenia
* Śledzenie zdarzeń systemu Windows (ETW) zdarzenia 
* Zbieranie zdarzeń dziennika z dziennika systemu  
* Zrzuty awaryjne 

Agenta funkcji Diagnostyka Azure powinna być używana, gdy:

* Chcesz zarchiwizować dzienniki i metryki w usłudze Azure storage
* Zintegruj dane monitorowania z narzędziami innych firm. Te narzędzia korzystają z różnych metod, w tym konto magazynu, przesyłane dalej do kwerendy [usługi Event Hubs](../event-hubs/event-hubs-about.md), lub wykonywanie zapytań za pomocą [interfejsu API REST monitorowania platformy Azure](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md)
* Przekazywanie danych do usługi Azure Monitor, tworzenie wykresów metryki w witrynie Azure portal lub utworzyć w czasie zbliżonym do rzeczywistego [alertów dotyczących metryk](../monitoring-and-diagnostics/alert-metric-overview.md). 
* Zestawy skalowania maszyn wirtualnych skalowania automatycznego i klasycznej usługi w chmurze w oparciu metryki systemu operacyjnego gościa.
* Badanie problemów dotyczących rozruchu maszyny Wirtualnej za pomocą [diagnostyki rozruchu](../virtual-machines/troubleshooting/boot-diagnostics.md).
* Zrozumienie, jak działają Twoje aplikacje i aktywnie identyfikuje problemów wpływających na ich za pomocą [usługi Application Insights](../azure-monitor/overview.md).
* Skonfiguruj usługę Log Analytics, aby zaimportować metryki i rejestrowanie danych zebranych z usług w chmurze, klasyczne maszyny wirtualne, a węzłów usługi Service Fabric są przechowywane na koncie usługi Azure storage.

### <a name="log-analytics-agent"></a>Log Analytics agent
W przypadku zaawansowanego monitorowania, gdzie potrzebujesz więcej niż zbieranie metryk i podzbiór dzienników, agenta usługi Log Analytics dla Windows i Linux jest wymagany. Agenta usługi Log Analytics został opracowany do kompleksowego zarządzania dla lokalnych maszyn fizycznych i wirtualnych, komputery monitorowane przez System Center Operations Manager i maszyn wirtualnych w hostowanej w innych chmurach. Agenci Windows i Linux nawiązują połączenie z obszarem roboczym usługi Log Analytics, aby zbierać zarówno dane oparte na rozwiązaniach monitorowania, jak i niestandardowymi źródłami danych, które można skonfigurować.

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

Agenta usługi Log Analytics powinny być używane, gdy:

* Zostały uruchomione w środowisku lokalnym lub w innym środowisku chmury maszyny
* Przy użyciu jednej z usługi Azure Monitor, takich jak monitorowanie rozwiązań [usługi Azure Monitor dla maszyn wirtualnych](../monitoring/monitoring-vminsights-overview.md?toc=%2fazure%2fmonitoring%2ftoc.json), [usługi Azure Monitor dla kontenerów](../monitoring/monitoring-container-insights-overview.md?toc=%2fazure%2fmonitoring%2ftoc.json)itp.  
* Przy użyciu jednej z pozostałych usług zarządzania systemu Azure takie jak [usługi Azure Security Center](../security-center/security-center-intro.md), [usługi Azure Automation](../automation/automation-intro.md)itp.
* Przekazywanie dzienników i/lub metryki danych do usługi Azure Monitor.

Wcześniej kilka usług platformy Azure były powiązane jako *pakietu Operations Management Suite*, a w rezultacie agenta usługi Log Analytics jest udostępniany w usługach w tym usługi Azure Security Center i Azure Automation.  Obejmuje to pełny zestaw funkcji, które oferują one, zapewniając kompleksowe zarządzanie całym ich cyklu życia maszynach wirtualnych platformy Azure. Obejmuje to:

* [Azure zarządzania aktualizacjami automatyzacji](../automation/automation-update-management.md) aktualizacji systemu operacyjnego.
* [Usługi Azure Automation Desired State Configuration](../automation/automation-dsc-overview.md) do zarządzania stanem spójnej konfiguracji.
* Śledź zmiany konfiguracji przy użyciu [spis i śledzenie zmian w usłudze Azure Automation](../automation/automation-change-tracking.md).
* Zbieranie dzienników niestandardowych z systemu operacyjnego i obsługiwanych aplikacji, takich jak [FluentD](../log-analytics/log-analytics-data-sources-json.md), [niestandardowe dzienniki](../log-analytics/log-analytics-data-sources-custom-logs.md), i [MySQL i Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) z usługą Log Analytics.
* Usługi systemu Azure, takich jak [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/) i [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/) natywnie przechowują swoje dane bezpośrednio w usłudze Log Analytics.  

### <a name="dependency-agent"></a>Agent zależności
Agent zależności został opracowany jako część rozwiązania mapy usługi, który pierwotnie został zewnętrznie opracowany przez firmę Microsoft. [Usługa Service Map](../monitoring/monitoring-service-map.md) i [usługi Azure Monitor dla maszyn wirtualnych](monitoring-vminsights-overview.md) wymaga agenta zależności w Windows i Linux maszyn wirtualnych i integruje się z usługą agenta usługi Log Analytics zbiera odnalezione dane dotyczące procesów uruchomionych na wirtualny Maszyna i zależności w procesie zewnętrznym. Ona te dane są przechowywane w usłudze Log Analytics i wizualizuje odnalezionych powiązanych elementów.

Może być konieczne kombinację tych agentów do monitorowania maszyny Wirtualnej. Agentów można zainstalować obok siebie jako rozszerzeń platformy Azure, jednak w systemie Linux, agenta usługi Log Analytics *musi* można zainstalować pierwszy lub — w przeciwnym razie instalacja zakończy się niepowodzeniem. 

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [omówienie agenta usługi Log Analytics](../log-analytics/log-analytics-agent-overview.md) Przegląd wymagań i obsługiwanych metod, aby wdrożyć agenta maszyn hostowanych na platformie Azure, w centrum danych lub w innym środowisku chmury.

