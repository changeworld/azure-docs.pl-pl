---
title: Omówienie usługi Azure, monitorowanie agentów | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera szczegółowe omówienie Azure agentów dostępne, które obsługują monitorowanie maszyn wirtualnych platformy Azure.
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
ms.openlocfilehash: a399c3968e5ee1e2d1f6d623a68dbb1e15cef212
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088645"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Omówienie usługi Azure agentów do monitorowania maszyn wirtualnych platformy Azure
Microsoft Azure oferuje wiele sposobów, aby zbierać różne typy danych z maszyn wirtualnych hostowanych na platformie Azure lub innych dostawców w chmurze systemem Microsoft Windows i Linux.  Ten artykuł pomoże opisano różnice i możliwości dostępne z każdego agenta, w kolejności, w celu ustalenia, która będzie obsługiwać zarządzania infrastrukturą usługi lub ogólne wymagania dotyczące monitorowania.  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Porównywanie agenta diagnostyki Azure i analizy dzienników
Obecnie na platformie Azure, istnieją dwa typy agentów dostępnych do monitorowania maszyny Wirtualnej platformy Azure — rozszerzenie diagnostyki Azure i Agent analizy dziennika dla systemów Linux i Windows.  Zasadniczo agenci mają zbierać metryki i dzienniki i przekazywać je do repozytorium. Dotyczy to jednak gdzie podobieństwa ich zakończyć.  

[Rozszerzenia diagnostyki Azure](../monitoring-and-diagnostics/azure-diagnostics.md), które podano dla usług w chmurze Azure, ponieważ stały się ogólnie dostępna w 2010, jest agentem, który dostarcza prostego zbierania danych diagnostycznych z zasobów IaaS platformy Azure, takich jak maszyny Wirtualnej, i Utrwalanie go do magazynu Azure.  Gdy w magazynie, wybrana do wyświetlenia za pomocą jednego z kilku dostępnych narzędzi, takich jak [Eksploratora serwera w programie Visual Studio](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md) i [Eksploratora usługi Storage Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Można zbierać:

* Wstępnie zdefiniowany zestaw liczników wydajności systemu operacyjnego i dzienniki zdarzeń, można również określić zbierania. 
* Wszystkie żądania i/lub nieudane żądania do serwera sieci web usług IIS
* Dane wyjściowe dzienniki śledzenia aplikacji .NET
* Zdarzenia śledzenia dla zdarzeń systemu Windows (ETW) 
* Zbieranie zdarzeń dziennika z syslog  
* Zrzuty awaryjne 

Dane mogą być także przekazywane do [usługi Application Insights](../application-insights/app-insights-cloudservices.md), [analizy dzienników](../log-analytics/log-analytics-overview.md), lub usług innych niż Azure za pomocą [Centrum zdarzeń](../event-hubs/event-hubs-what-is-event-hubs.md). 

W przypadku zaawansowanego monitorowania konieczne więcej niż zbieranie metryk i podzestawu dzienniki, agent analizy dzienników dla systemu Windows i Linux jest wymagany.  Z tego agenta będą mogli korzystać z usług Azure, takich jak automatyzacji i analizy dziennika, w tym pełny zestaw funkcji, które oferują one dostarczać kompleksowe zarządzanie maszynach wirtualnych platformy Azure w całym cyklu ich życia. Obejmuje to:

* [Zarządzanie aktualizacjami usługi Automatyzacja Azure](../automation/automation-update-management.md) aktualizacji systemu operacyjnego
* [Konfiguracji usługi Azure Automation żądany stan](../automation/automation-dsc-overview.md) do zarządzania stanem spójnej konfiguracji
* Śledzenie zmian konfiguracji z [śledzenia zmian automatyzacji Azure i magazynu](../automation/automation-change-tracking.md)
* Kolekcja dzienników niestandardowych z systemu operacyjnego i obsługiwanych aplikacji, takich jak [FluentD](../log-analytics/log-analytics-data-sources-json.md), [dzienników niestandardowych](../log-analytics/log-analytics-data-sources-custom-logs.md), [MySQL i Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) z analizy dzienników
* Z usług takich jak Azure [usługi Application Insights](https://docs.microsoft.com/azure/application-insights/) i [Centrum zabezpieczeń Azure](https://docs.microsoft.com/azure/security-center/) natywnie przechowywać swoje dane bezpośrednio w analizy dzienników.  

## <a name="next-steps"></a>Kolejne kroki

- Zobacz [zbierania danych z komputerów w środowisku z analizy dzienników](../log-analytics/log-analytics-concept-hybrid.md) Aby przejrzeć wymagania i dostępnych metod, aby wdrożyć agenta na komputerach w centrum danych lub w innym środowisku chmury.
- Aby skonfigurować zbieranie danych z maszyn wirtualnych platformy Azure, zobacz [Zbieranie danych dotyczących infrastruktury Azure Virtual Machines](../log-analytics/log-analytics-quick-collect-azurevm.md). 