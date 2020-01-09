---
title: Przegląd rozszerzenia Diagnostyka Azure
description: Korzystaj z diagnostyki platformy Azure na potrzeby debugowania, mierzenia wydajności, monitorowania, analizy ruchu w usługach Cloud Services, Virtual Machines i Service Fabric
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: 1bdefc6b61e4e5cc5b8648880c5fdd8662af1bc1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75395369"
---
# <a name="what-is-azure-diagnostics-extension"></a>Co to jest rozszerzenie Diagnostyka Azure
Rozszerzenie Diagnostyka Azure jest agentem na platformie Azure, który umożliwia zbieranie danych diagnostycznych we wdrożonej aplikacji. Można użyć rozszerzenia diagnostyki z wielu różnych źródeł. Obecnie obsługiwane są role sieci Web i procesu roboczego usługi w chmurze Azure (klasycznej), Virtual Machines, zestawy skalowania maszyn wirtualnych i Service Fabric. Inne usługi platformy Azure mają różne metody diagnostyki. Zobacz [Omówienie monitorowania na platformie Azure](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Agent systemu Linux
[Wersja systemu Linux rozszerzenia](../../virtual-machines/extensions/diagnostics-linux.md) jest dostępna dla Virtual Machines z systemem Linux. Zebrane dane statystyczne i zachowanie różnią się w zależności od wersji systemu Windows.

## <a name="data-you-can-collect"></a>Dane, które można zbierać
Rozszerzenie Diagnostyka Azure może zbierać następujące typy danych:

| Źródło danych | Opis |
| --- | --- |
| Metryki licznika wydajności |System operacyjny i niestandardowe liczniki wydajności |
| Dzienniki aplikacji |Śledzenie komunikatów pisanych przez aplikację |
| Dzienniki zdarzeń Windows |Informacje wysyłane do systemu rejestrowania zdarzeń systemu Windows |
| Dzienniki zdarzeń platformy .NET |Kod pisania zdarzeń przy użyciu klasy [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) platformy .NET |
| Dzienniki usług IIS |Informacje o witrynach sieci Web usług IIS |
| [Dzienniki ETW oparte na manifestach](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Śledzenie zdarzeń dla zdarzeń systemu Windows generowanych przez dowolny proces. jedno |
| Zrzuty awaryjne (dzienniki) |Informacje o stanie procesu w przypadku awarii aplikacji |
| Dzienniki błędów niestandardowych |Dzienniki utworzone przez aplikację lub usługę |
| Dzienniki infrastruktury diagnostycznej platformy Azure |Informacje dotyczące Diagnostyka Azure samego siebie |

(1) Aby uzyskać listę dostawców ETW, uruchom `c:\Windows\System32\logman.exe query providers` w oknie konsoli na komputerze, z którego chcesz zebrać informacje.

## <a name="data-storage"></a>Magazyn danych
Rozszerzenie przechowuje swoje dane na [koncie usługi Azure Storage](diagnostics-extension-to-storage.md) , które określisz.

Możesz również wysłać ją do [Application Insights](../../azure-monitor/app/cloudservices.md). 

Innym rozwiązaniem jest przesyłanie strumieniowe do [centrum zdarzeń](../../event-hubs/event-hubs-about.md), co następnie pozwala na wysłanie go do usług monitorowania niezwiązanych z platformą Azure.

Istnieje również możliwość wysłania danych do Azure Monitor metryki bazy danych szeregów czasowych. W tej chwili ten obiekt sink ma zastosowanie tylko do liczników wydajności. Umożliwia wysyłanie liczników wydajności w postaci metryk niestandardowych. Ta funkcja jest dostępna w wersji zapoznawczej. Azure Monitor sink obsługuje:
* Pobieranie wszystkich liczników wydajności wysyłanych do Azure Monitor za pośrednictwem [interfejsów API metryk Azure monitor.](https://docs.microsoft.com/rest/api/monitor/)
* Generowanie alertów dotyczących wszystkich liczników wydajności wysyłanych do Azure Monitor za pośrednictwem [alertów metryk](../../azure-monitor/platform/alerts-overview.md) w Azure monitor
* Sposób traktowania operatora wieloznacznego w licznikach wydajności jako wymiar "wystąpienie" w metryce.  Na przykład jeśli zebrano licznik "dysk logiczny (\*)/DiskWrites/sec", można filtrować i dzielić w wymiarze "wystąpienie" na potrzeby wykreślania lub generowania alertów na dysku zapisy/s dla każdego dysku logicznego na maszynie wirtualnej (na przykład C:)

Aby dowiedzieć się więcej na temat konfigurowania tego ujścia, zapoznaj się z [dokumentacją schematu usługi Diagnostyka Azure.](diagnostics-extension-schema-1dot3.md)

## <a name="costs"></a>Koszty
Każda z powyższych opcji może ponosić koszty. Upewnij się, że Zbadaj je, aby uniknąć nieoczekiwanych rachunków.  Application Insights, centrum zdarzeń i Magazyn Azure mają oddzielne koszty związane z pozyskiwaniem i czas przechowywania. W szczególności usługa Azure Storage będzie przechowywać dowolne dane w nieskończoność, dzięki czemu możesz chcieć przeczyścić starsze dane po upływie pewnego czasu, aby utrzymać swoje koszty.    

## <a name="versioning-and-configuration-schema"></a>Schemat konfiguracji i przechowywania wersji
Zobacz [historię wersji i schemat Diagnostyka Azure](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Następne kroki
Wybierz usługę, w której próbujesz zbierać dane diagnostyczne, i zacznij korzystać z poniższych artykułów. Użyj ogólnych linków diagnostyki platformy Azure, aby uzyskać informacje na temat określonych zadań.

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services przy użyciu Diagnostyka Azure
* Jeśli używasz programu Visual Studio, zobacz [Korzystanie z programu Visual Studio do śledzenia aplikacji Cloud Services](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) , aby rozpocząć pracę. W przeciwnym razie Zobacz
* [Jak monitorować usługi Cloud Services przy użyciu Diagnostyka Azure](../../cloud-services/cloud-services-how-to-monitor.md)
* [Konfigurowanie Diagnostyka Azure w aplikacji Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Aby uzyskać bardziej zaawansowane tematy, zobacz

* [Używanie Diagnostyka Azure z Application Insights Cloud Services](../../azure-monitor/app/cloudservices.md)
* [Śledzenie przepływu aplikacji Cloud Services z Diagnostyka Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Konfigurowanie diagnostyki na Cloud Services przy użyciu programu PowerShell](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtual Machines
* Jeśli używasz programu Visual Studio, zobacz [Korzystanie z programu Visual Studio do śledzenia Virtual Machines platformy Azure](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) , aby rozpocząć pracę. W przeciwnym razie Zobacz
* [Konfigurowanie Diagnostyka Azure na maszynie wirtualnej platformy Azure](/azure/virtual-machines/extensions/diagnostics-windows)

Aby uzyskać bardziej zaawansowane tematy, zobacz

* [Konfigurowanie diagnostyki na platformie Azure Virtual Machines przy użyciu programu PowerShell](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Tworzenie maszyny wirtualnej z systemem Windows z funkcją monitorowania i diagnostyki przy użyciu szablonu Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Sieć szkieletowa usługi
Rozpocznij od [monitorowania aplikacji Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Wiele innych artykułów diagnostyki Service Fabric jest dostępnych w drzewie nawigacyjnym po lewej stronie po przeprowadzeniu tego artykułu.

## <a name="general-articles"></a>Ogólne artykuły
* Dowiedz się, jak [używać liczników wydajności w Diagnostyka Azure](../../cloud-services/diagnostics-performance-counters.md).
* Jeśli masz problemy z uruchamianiem diagnostyki lub wyszukiwaniem danych w tabelach usługi Azure Storage, zobacz [Rozwiązywanie problemów Diagnostyka Azure](diagnostics-extension-troubleshooting.md)

