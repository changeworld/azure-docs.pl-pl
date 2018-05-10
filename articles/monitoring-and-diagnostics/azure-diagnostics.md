---
title: Omówienie diagnostyki Azure rozszerzenia | Dokumentacja firmy Microsoft
description: Użyj diagnostyki Azure dla debugowania pomiaru wydajności, monitorowanie, analiza ruchu w usługi w chmurze, maszyn wirtualnych i sieci szkieletowej usług
services: multiple
documentationcenter: .net
author: rboucher
manager: ''
editor: ''
ms.assetid: baad40d8-c915-4f93-b486-8b160bf33463
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/01/2018
ms.author: robb
ms.openlocfilehash: daeaddefa461e71fcc62af4efc4fb7084b237cf9
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="what-is-azure-diagnostics-extension"></a>Co to jest rozszerzenie diagnostyki Azure
Rozszerzenie Azure Diagnostics jest agenta w ramach platformy Azure, która umożliwia zbieranie danych diagnostycznych na wdrożonej aplikacji. Można użyć rozszerzenia diagnostyki z wielu różnych źródeł. Obecnie obsługiwane są usługi w chmurze Azure (klasyczne) w sieci Web i proces roboczy, maszyn wirtualnych, zestawy skalowania maszyny wirtualnej i sieci szkieletowej usług. Innymi usługami platformy Azure jest diagnostyki różnych metod. Zobacz [monitorowania na platformie Azure — omówienie](monitoring-overview.md). 

## <a name="linux-agent"></a>Agent systemu Linux
A [wersji systemu Linux rozszerzenia](../virtual-machines/linux/diagnostic-extension.md) jest dostępna dla maszyn wirtualnych z systemem Linux. Statystyki zebranych i zachowanie się różnić od wersji systemu Windows. 

## <a name="data-you-can-collect"></a>Dane, które można zbierać
Rozszerzenia diagnostyki Azure można zebrać następujące typy danych:

| Źródło danych | Opis |
| --- | --- |
| Liczniki wydajności |System operacyjny i niestandardowe liczniki wydajności |
| Dzienniki aplikacji |Wiadomości śledzenia zapisanych przez aplikację |
| Dzienniki zdarzeń systemu Windows |Informacje wysyłane do rejestrowania zdarzeń systemu |
| Źródło zdarzenia platformy .NET |Kod zapisywania zdarzeń przy użyciu programu .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) — klasa |
| Dzienniki programu IIS |Informacje o witryn sieci web usług IIS |
| Manifest na podstawie ETW |Zdarzenia śledzenia dla systemu Windows zdarzenia generowane przez żaden proces |
| Zrzuty awaryjne |Informacje o stanie procesu w przypadku awarii aplikacji |
| Dzienniki błędów niestandardowych |Dzienniki tworzone przez usługi lub aplikacji |
| Dzienniki diagnostyczne infrastruktury platformy Azure |Informacje o diagnostyce sam |

## <a name="data-storage"></a>Magazyn danych
Rozszerzenie przechowuje dane w [konta magazynu Azure](azure-diagnostics-storage.md) określonym przez użytkownika. 

Można również wysyłać do [usługi Application Insights](../application-insights/app-insights-cloudservices.md). Inną możliwością jest wykonanie go do strumienia [Centrum zdarzeń](../event-hubs/event-hubs-what-is-event-hubs.md), który następnie służy do wysyłania do innych niż Azure montoring usług. 


## <a name="versioning-and-configuration-schema"></a>Przechowywanie wersji i konfiguracji schematu
Zobacz [Historia wersji diagnostyki Azure i schemat](azure-diagnostics-versioning-history.md).


## <a name="next-steps"></a>Kolejne kroki
Wybierz usługi, które próbujesz na zbieranie danych diagnostycznych i skorzystaj z poniższych artykułów, aby rozpocząć pracę. Użyj łączy ogólne diagnostyki Azure dla odwołania do wykonywania określonych zadań.

## <a name="cloud-services-using-azure-diagnostics"></a>Usługi w chmurze za pomocą diagnostyki Azure
* Jeśli używasz programu Visual Studio, zobacz [program Visual Studio do śledzenia aplikacji usługi w chmurze](../vs-azure-tools-debug-cloud-services-virtual-machines.md) rozpocząć pracę. W przeciwnym razie zobacz
* [Jak monitorować usługi w chmurze za pomocą diagnostyki Azure](../cloud-services/cloud-services-how-to-monitor.md)
* [Konfigurowanie diagnostyki Azure w aplikacji usługi w chmurze](../cloud-services/cloud-services-dotnet-diagnostics.md)

Aby uzyskać bardziej zaawansowanych tematów zobacz

* [Za pomocą diagnostyki Azure z usługą Application Insights dla usługi w chmurze](../application-insights/app-insights-cloudservices.md)
* [Śledzenie przepływu aplikacji usługi w chmurze Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Aby skonfigurować diagnostykę na usługi w chmurze za pomocą programu PowerShell](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Maszyny wirtualne
* Jeśli używasz programu Visual Studio, zobacz [program Visual Studio do śledzenia maszyny wirtualne Azure](../vs-azure-tools-debug-cloud-services-virtual-machines.md) rozpocząć pracę. W przeciwnym razie zobacz
* [Konfigurowanie diagnostyki Azure na maszynie wirtualnej platformy Azure](../virtual-machines-dotnet-diagnostics.md)

Aby uzyskać bardziej zaawansowanych tematów zobacz

* [Aby skonfigurować diagnostykę na maszynach wirtualnych platformy Azure za pomocą programu PowerShell](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utwórz maszynę wirtualną systemu Windows z monitorowania i diagnostyki za pomocą szablonu usługi Resource Manager Azure](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Rozpoczynanie pracy w [monitorowanie aplikacji sieci szkieletowej usług](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Wiele innych artykułów diagnostyki sieci szkieletowej usług są dostępne w drzewie nawigacji po lewej stronie, gdy pojawi się w tym artykule.

## <a name="general-articles"></a>Artykuły ogólne
* Dowiedz się, jak [użyć liczników wydajności w diagnostyce Azure](../cloud-services/diagnostics-performance-counters.md).
* Jeśli masz problem z uruchomieniem diagnostyki lub wyszukiwanie danych w tabelach magazynu Azure, zobacz [rozwiązywania problemów Diagnostyka Azure](azure-diagnostics-troubleshooting.md)
