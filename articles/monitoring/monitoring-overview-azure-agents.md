---
title: Omówienie usługi Azure, agentów monitorowania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera szczegółowe omówienie agentów programu Azure dostępne, które obsługują monitorowanie maszyn wirtualnych platformy Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: magoedte
ms.openlocfilehash: 81db6720422de111cc5b390c58e9020d7c19f90a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282037"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Omówienie usługi Azure agentów do monitorowania maszyn wirtualnych platformy Azure
Microsoft Azure oferuje wiele sposobów, aby zbierać różne typy danych z maszyn wirtualnych hostowanych na platformie Azure lub innych dostawców rozwiązań w chmurze systemem Microsoft Windows i Linux.  Ten artykuł pomoże opisano różnice i możliwości dostępne z każdego agenta w kolejności, w celu ustalenia, który z nich będzie obsługiwać zarządzania usługi lub ogólne wymagania dotyczące monitorowania.  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Porównywanie agenta diagnostyki platformy Azure i usługi Log Analytics
Już dziś na platformie Azure, istnieją dwa rodzaje agentów dostępnych do monitorowania maszyn wirtualnych platformy Azure — rozszerzenie diagnostyki platformy Azure i Log Analytics Agent dla systemu Linux i Windows.  Zasadniczo agenci pozwalają zbierać metryki i dzienniki i przekazywać je do repozytorium. Jednak to, gdzie kończy się ich podobieństwa.  

[Rozszerzenia diagnostyki Azure](../monitoring-and-diagnostics/azure-diagnostics.md), które podano dla usług Azure Cloud Services, ponieważ stały się ogólnie dostępna w 2010 r. jest agent, który dostarcza proste zbierania danych diagnostycznych z zasobu usługi IaaS platformy Azure, takich jak maszyny Wirtualnej, a Utrwalanie go do usługi Azure storage.  Gdy w usłudze storage została wybrana opcja wyświetlania przy użyciu jednego z kilku dostępnych narzędzi, takich jak [Eksploratora serwera w programie Visual Studio](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) i [Eksploratora usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Istnieje możliwość zbierania:

* Wstępnie zdefiniowany zestaw liczników wydajności systemu operacyjnego i dzienniki zdarzeń lub można określić, których mają być gromadzone. 
* Wszystkie żądania i/lub żądań zakończonych niepowodzeniem na serwerze sieci web usług IIS
* Aplikacja platformy .NET, dzienniki danych wyjściowych śledzenia
* Śledzenie zdarzeń systemu Windows (ETW) zdarzenia 
* Zbieranie zdarzeń dziennika z dziennika systemu  
* Zrzuty awaryjne 

Dane mogą być także przekazywane do [usługi Application Insights](../application-insights/app-insights-cloudservices.md), [usługi Log Analytics](../log-analytics/log-analytics-queries.md), lub usługi spoza platformy Azure przy użyciu [Centrum zdarzeń](../event-hubs/event-hubs-about.md). 

W przypadku zaawansowanego monitorowania, gdzie potrzebujesz więcej niż zbieranie metryk i podzbiór dzienników, agenta usługi Log Analytics dla Windows i Linux jest wymagany.  Z tego agenta jest możliwe korzystanie z usług platformy Azure, takich jak usługi Automation i Log Analytics, w tym pełny zestaw funkcji, które oferują one dostarczać kompleksowe zarządzanie całym ich cyklu życia maszynach wirtualnych platformy Azure. Obejmuje to:

* [Azure zarządzania aktualizacjami automatyzacji](../automation/automation-update-management.md) aktualizacji systemu operacyjnego
* [Usługi Azure Automation Desired State Configuration](../automation/automation-dsc-overview.md) do zarządzania stanem spójnej konfiguracji
* Śledź zmiany konfiguracji przy użyciu [spis i śledzenie zmian w usłudze Azure Automation](../automation/automation-change-tracking.md)
* Zbieranie dzienników niestandardowych z systemu operacyjnego i obsługiwanych aplikacji, takich jak [FluentD](../log-analytics/log-analytics-data-sources-json.md), [niestandardowe dzienniki](../log-analytics/log-analytics-data-sources-custom-logs.md), [MySQL i Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) z usługą Log Analytics
* Usługi systemu Azure, takich jak [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/) i [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/) natywnie przechowują swoje dane bezpośrednio w usłudze Log Analytics.  

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [zbieranie danych z komputerów w środowisku z usługą Log Analytics](../log-analytics/log-analytics-concept-hybrid.md) Przegląd wymagań i dostępnych metod, aby wdrożyć agenta na komputerach w centrum danych lub w innym środowisku chmury.
- Aby skonfigurować zbieranie danych z maszyn wirtualnych platformy Azure, zobacz [Zbieranie danych dotyczących infrastruktury Azure Virtual Machines](../log-analytics/log-analytics-quick-collect-azurevm.md). 
