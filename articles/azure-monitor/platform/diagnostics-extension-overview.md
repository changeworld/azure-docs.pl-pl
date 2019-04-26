---
title: Omówienie rozszerzenia diagnostyki Azure
description: Korzystanie z platformy Azure diagnostyki do debugowania, mierzenia wydajności, monitorowania, analizy ruchu w usługach w chmurze, maszyny wirtualne i usługi Service fabric
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 8a287f118c126967d2cf8cad77a434cfecc098eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60236233"
---
# <a name="what-is-azure-diagnostics-extension"></a>Co to jest rozszerzenie diagnostyki platformy Azure
Rozszerzenie diagnostyki platformy Azure jest agenta w systemie Azure umożliwia zbieranie danych diagnostycznych na rozmieszczonej aplikacji. Rozszerzenie diagnostyki można użyć z wielu różnych źródeł. Obecnie obsługiwane wartości to usługa w chmurze (klasyczne) w sieci Web i ról procesów roboczych, maszyny wirtualne, maszyna wirtualna skalowanie zestawów i usługi Service Fabric. Inne usługi platformy Azure jest diagnostyki różnych metod. Zobacz [monitorowania na platformie Azure — omówienie](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Agent systemu Linux
A [wersji systemu Linux rozszerzenie](../../virtual-machines/extensions/diagnostics-linux.md) dostępnej dla maszyn wirtualnych z systemem Linux. Dane statystyczne zebrane i zachowanie różnią się od wersji Windows.

## <a name="data-you-can-collect"></a>Dane, które można zebrać
Rozszerzenie diagnostyki platformy Azure można zebrać następujące typy danych:

| Źródło danych | Opis |
| --- | --- |
| Metryki licznika wydajności |System operacyjny i niestandardowych liczników wydajności |
| Dzienniki aplikacji |Komunikaty śledzenia zapisywane przez aplikację |
| Dzienniki zdarzeń systemu Windows |Informacje wysyłane do systemu Windows rejestrowania zdarzeń |
| Rejestruje Element EventSource platformy .NET |Kod zapisywania zdarzeń za pomocą programu .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) klasy |
| Dzienniki usług IIS |Informacji na temat witryny sieci web usług IIS |
| [Manifest na podstawie dzienników zdarzeń systemu Windows](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Zdarzenia śledzenia dla Windows zdarzenia generowane przez żaden proces. (1) |
| Zrzuty awaryjne (Dzienniki) |Informacje o stanie procesu, jeśli wystąpiła awaria aplikacji |
| Dzienniki błędów niestandardowych |Dzienniki utworzone przez aplikację lub usługę |
| Dzienniki infrastruktury diagnostyki platformy Azure |Informacji na temat usługi Azure Diagnostics, sama |

(1) Aby uzyskać listę dostawców ETW, uruchom `c:\Windows\System32\logman.exe query providers` w oknie konsoli, na komputerze, na które chcesz zebrać informacje z.

## <a name="data-storage"></a>Magazyn danych
Rozszerzenie zapisuje dane w [konta usługi Azure Storage](diagnostics-extension-to-storage.md) określonym przez użytkownika.

Można również wysłać go do [usługi Application Insights](../../azure-monitor/app/cloudservices.md). 

Innym rozwiązaniem jest przesyłanie strumieniowe do [Centrum zdarzeń](../../event-hubs/event-hubs-about.md), który następnie pozwala wysyłać je do usługi monitorowania spoza platformy Azure.

Istnieje również możliwość wysyłania danych do bazy danych szeregów czasowych metryk usługi Azure Monitor. W tej chwili zlew ma zastosowanie tylko do liczników wydajności. Umożliwia on wysyłanie liczników wydajności w jako metryki niestandardowe. Ta funkcja jest dostępna w wersji zapoznawczej. Obsługuje ujścia usługi Azure Monitor:
* Pobieranie wszystkich liczników wydajności wysyłane do usługi Azure Monitor za pośrednictwem [metryk usługi Azure Monitor interfejsów API.](https://docs.microsoft.com/rest/api/monitor/)
* Alertów dla wszystkich liczników wydajności wysyłane do usługi Azure Monitor za pośrednictwem [alertów dotyczących metryk](../../azure-monitor/platform/alerts-overview.md) w usłudze Azure Monitor
* Traktowanie operator symbolu wieloznacznego w liczniki wydajności jako wymiar "Wystąpienie" w swoje metryki.  Na przykład, gdy zostały zebrane "dysk logiczny (\*) / DiskWrites na sekundę" licznik będzie mieć możliwość filtrowania i podział wymiaru "Wystąpienie" do kreślenia lub alertu na zapisy dysku/s dla każdego dysku logicznego na maszynie Wirtualnej (na przykład C:)

Aby dowiedzieć się więcej na temat konfigurowania tego ujścia, zobacz [dokumentacji schemat usługi Diagnostyka Azure.](diagnostics-extension-schema-1dot3.md)

## <a name="costs"></a>Koszty
Każdego z powyższych opcji może być naliczane opłaty. Pamiętaj zbadać je, aby uniknąć nieoczekiwanych opłat.  Usługa Application Insights, Centrum zdarzeń i usługi Azure Storage ma oddzielne koszty pozyskiwania i czas przechowywane. W szczególności usługi Azure Storage będzie przechowywać żadnych danych zawsze więc chcesz przeczyścić starszych danych po określonym czasie do niskich kosztów usługi.    

## <a name="versioning-and-configuration-schema"></a>Schemat przechowywania wersji i konfiguracji
Zobacz [schemat i Historia wersji usługi Diagnostyka Azure](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Kolejne kroki
Wybierz usługę, której próbujesz się zbieranie danych diagnostycznych na i skorzystaj z następujących artykułów, aby rozpocząć pracę. Użyj linków ogólne diagnostyki platformy Azure dla odwołania do wykonywania określonych zadań.

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

