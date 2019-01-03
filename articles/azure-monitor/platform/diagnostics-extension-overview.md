---
title: Omówienie rozszerzenia diagnostyki Azure
description: Korzystanie z platformy Azure diagnostyki do debugowania, mierzenia wydajności, monitorowania, analizy ruchu w usługach w chmurze, maszyny wirtualne i usługi Service fabric
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: dc8b8e37224201062d0cf53174f3248f04ca0cc3
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789760"
---
# <a name="what-is-azure-diagnostics-extension"></a>Co to jest rozszerzenie diagnostyki platformy Azure
Rozszerzenie diagnostyki platformy Azure jest agenta w systemie Azure umożliwia zbieranie danych diagnostycznych na rozmieszczonej aplikacji. Rozszerzenie diagnostyki można użyć z wielu różnych źródeł. Obecnie obsługiwane wartości to usługa w chmurze (klasyczne) w sieci Web i ról procesów roboczych, maszyny wirtualne, zestawy skalowania maszyn wirtualnych i Service Fabric. Inne usługi platformy Azure jest diagnostyki różnych metod. Zobacz [monitorowania na platformie Azure — omówienie](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Agent systemu Linux
A [wersji systemu Linux rozszerzenie](../../virtual-machines/extensions/diagnostics-linux.md) dostępnej dla maszyn wirtualnych z systemem Linux. Dane statystyczne zebrane i zachowanie różnią się od wersji Windows.

## <a name="data-you-can-collect"></a>Dane, które można zebrać
Rozszerzenie diagnostyki platformy Azure można zebrać następujące typy danych:

| Źródło danych | Opis |
| --- | --- |
| Liczniki wydajności |System operacyjny i niestandardowych liczników wydajności |
| Dzienniki aplikacji |Komunikaty śledzenia zapisywane przez aplikację |
| Dzienniki zdarzeń systemu Windows |Informacje wysyłane do systemu Windows rejestrowania zdarzeń |
| Źródło zdarzeń .NET |Kod zapisywania zdarzeń za pomocą programu .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) klasy |
| Dzienniki usług IIS |Informacji na temat witryny sieci web usług IIS |
| Śledzenie zdarzeń systemu Windows w oparciu o manifest |Zdarzenia śledzenia dla Windows zdarzenia generowane przez żaden proces. (1) |
| Zrzuty awaryjne |Informacje o stanie procesu w przypadku awarii aplikacji |
| Dzienniki błędów niestandardowych |Dzienniki utworzone przez aplikację lub usługę |
| Dzienniki infrastruktury diagnostyki platformy Azure |Informacje o diagnostyce sam |

(1) Aby uzyskać listę dostawców ETW, uruchom `c:\Windows\System32\logman.exe query providers` w oknie konsoli, na komputerze, na które chcesz zebrać informacje z.

## <a name="data-storage"></a>Magazyn danych
Rozszerzenie zapisuje dane w [konta usługi Azure Storage](diagnostics-extension-to-storage.md) określonym przez użytkownika.

Można również wysłać go do [usługi Application Insights](../../azure-monitor/app/cloudservices.md). Innym rozwiązaniem jest przesyłanie strumieniowe do [Centrum zdarzeń](../../event-hubs/event-hubs-about.md), który następnie pozwala wysyłać je do usługi montoring spoza platformy Azure.

### <a name="azure-monitor"></a>Azure Monitor
Istnieje również możliwość wysyłania danych do usługi Azure Monitor. W tej chwili zlew ma zastosowanie tylko do liczników wydajności. Umożliwia wysyłanie liczniki wydajności zebrane na maszynie Wirtualnej, zestawu skalowania maszyn wirtualnych, lub w chmurze Usługa do usługi Azure Monitor jako metryki niestandardowe. Obsługuje ujścia usługi Azure Monitor:
* Pobieranie wszystkich liczników wydajności wysyłane do usługi Azure Monitor za pośrednictwem [metryk usługi Azure Monitor interfejsów API.](https://docs.microsoft.com/rest/api/monitor/)
* Alertów dla wszystkich liczników wydajności wysyłane do usługi Azure Monitor w nowej witrynie [ujednolicone środowisko alertów](../../azure-monitor/platform/alerts-overview.md) w usłudze Azure Monitor
* Traktowanie operator symbolu wieloznacznego w liczniki wydajności jako wymiar "Wystąpienie" w swoje metryki.  Na przykład, gdy zostały zebrane "dysk logiczny (\*) / DiskWrites na sekundę" licznik będzie mieć możliwość filtrowania i podział wymiaru "Wystąpienie" do kreślenia lub alertu na zapisy dysku/s dla każdego dysku logicznego na maszynie Wirtualnej (C: D:, itp.)

Aby dowiedzieć się więcej na temat konfigurowania tego obiektu sink, zapoznaj się [dokumentacji schemat usługi Diagnostyka Azure.](diagnostics-extension-schema-1dot3.md)

## <a name="versioning-and-configuration-schema"></a>Schemat przechowywania wersji i konfiguracji
Zobacz [schemat i Historia wersji usługi Diagnostyka Azure](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Kolejne kroki
Wybierz usługę, której chcesz się zbieranie danych diagnostycznych na i skorzystaj z następujących artykułów, aby rozpocząć pracę. Użyj linków ogólne diagnostyki platformy Azure dla odwołania do wykonywania określonych zadań.

## <a name="cloud-services-using-azure-diagnostics"></a>Usługi w chmurze przy użyciu diagnostyki Azure
* Jeśli używasz programu Visual Studio, zobacz [za pomocą Visual Studio śledzenia aplikacji usługi w chmurze](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) na rozpoczęcie pracy. W przeciwnym razie zobacz
* [Jak monitorować usługi Cloud services przy użyciu diagnostyki Azure](../../cloud-services/cloud-services-how-to-monitor.md)
* [Konfigurowanie diagnostyki platformy Azure w aplikacji usługi w chmurze](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Aby uzyskać bardziej zaawansowanych tematów zobacz

* [Narzędzie diagnostyczne systemu Azure za pomocą usługi Application Insights do usług w chmurze](../../azure-monitor/app/cloudservices.md)
* [Śledzenie przepływu aplikacji usługi w chmurze przy użyciu diagnostyki Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Konfigurowanie diagnostyki w usługach Cloud Services za pomocą programu PowerShell](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Maszyny wirtualne
* Jeśli używasz programu Visual Studio, zobacz [za pomocą Visual Studio śledzenia usługi Azure Virtual Machines](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) na rozpoczęcie pracy. W przeciwnym razie zobacz
* [Konfigurowanie diagnostyki platformy Azure na maszynie wirtualnej platformy Azure](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

Aby uzyskać bardziej zaawansowanych tematów zobacz

* [Konfigurowanie diagnostyki w usłudze Azure Virtual Machines przy użyciu programu PowerShell](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Utwórz maszynę wirtualną Windows z funkcjami monitorowania i diagnostyki przy użyciu szablonu Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Rozpocznij pracę w [monitorowania aplikacji usługi Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Wiele innych artykułów diagnostyki usługi Service Fabric są dostępne w drzewie nawigacji po lewej stronie, gdy można uzyskać dostęp do tego artykułu.

## <a name="general-articles"></a>Ogólne artykuły
* Dowiedz się, jak [używanie liczników wydajności w usłudze Diagnostyka Azure](../../cloud-services/diagnostics-performance-counters.md).
* Jeśli masz problemy z uruchomieniem diagnostyki lub wyszukiwanie danych w tabelach usługi Azure storage, zobacz [Rozwiązywanie problemów z usługi Azure Diagnostics](diagnostics-extension-troubleshooting.md)
