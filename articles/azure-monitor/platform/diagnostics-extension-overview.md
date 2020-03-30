---
title: Omówienie rozszerzenia diagnostyki platformy Azure
description: Używanie diagnostyki platformy Azure do debugowania, pomiaru wydajności, monitorowania, analizy ruchu w usługach w chmurze, maszynach wirtualnych i sieci szkieletowej usług
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 6cb514312db525ffd2ccf9f7b70968daaa94f322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672382"
---
# <a name="azure-diagnostics-extension-overview"></a>Omówienie rozszerzenia diagnostyki platformy Azure
Rozszerzenie diagnostyki platformy Azure jest [agentem w usłudze Azure Monitor,](agents-overview.md) który zbiera dane monitorowania z systemu operacyjnego gościa zasobów obliczeniowych platformy Azure, w tym maszyn wirtualnych. Ten artykuł zawiera omówienie rozszerzenia diagnostyki platformy Azure, w tym określone funkcje, które obsługuje i opcje instalacji i konfiguracji. 

> [!NOTE]
> Rozszerzenie diagnostyki platformy Azure jest jednym z dostępnych agentów do zbierania danych monitorowania z systemu operacyjnego gościa zasobów obliczeniowych. Zobacz [Omówienie agentów usługi Azure Monitor, aby](agents-overview.md) uzyskać opis różnych agentów i wskazówki dotyczące wybierania odpowiednich agentów dla twoich wymagań.

## <a name="comparison-to-log-analytics-agent"></a>Porównanie z agentem usługi Log Analytics
Agent usługi Log Analytics w usłudze Azure Monitor może również służyć do zbierania danych monitorowania z systemu operacyjnego gościa maszyn wirtualnych. Możesz użyć jednego lub obu w zależności od wymagań. Zobacz [Omówienie agentów usługi Azure Monitor, aby](agents-overview.md) uzyskać szczegółowe porównanie agentów usługi Azure Monitor. 

Najważniejsze różnice, które należy wziąć pod uwagę, to:

- Rozszerzenie diagnostyki platformy Azure może być używane tylko z maszynami wirtualnymi platformy Azure. Agent usługi Log Analytics może być używany z maszynami wirtualnymi na platformie Azure, innych chmurach i lokalnie.
- Rozszerzenie diagnostyki platformy Azure wysyła dane do usługi Azure Storage, [Metryki usługi Azure Monitor](data-platform-metrics.md) (tylko dla systemu Windows) i centrum zdarzeń. Agent usługi Log Analytics zbiera dane do [dzienników monitora platformy Azure](data-platform-logs.md).
- Agent usługi Log Analytics jest wymagany dla [rozwiązań,](../monitor-reference.md#insights-and-core-solutions) [usługi Azure Monitor dla maszyn wirtualnych](../insights/vminsights-overview.md)i innych usług, takich jak Usługa Azure Security [Center.](/azure/security-center/)

## <a name="costs"></a>Koszty
Rozszerzenie diagnostyczne platformy Azure nie jest żadnym kosztem, ale mogą być naliczane opłaty za dane ponajmowane. Sprawdź [ceny usługi Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) dla miejsca docelowego, w którym zbierasz dane.

## <a name="data-collected"></a>Zbierane dane
W poniższych tabelach przedstawiono dane, które mogą być zbierane przez rozszerzenie diagnostyki systemu Windows i Linux.

### <a name="windows-diagnostics-extension-wad"></a>Rozszerzenie diagnostyki systemu Windows (WAD)

| Źródło danych | Opis |
| --- | --- |
| Dzienniki zdarzeń systemu Windows   | Zdarzenia z dziennika zdarzeń systemu Windows. |
| Liczniki wydajności | Wartości liczbowe mierzące wydajność różnych aspektów systemu operacyjnego i obciążeń. |
| Dzienniki usług IIS             | Informacje o użyciu witryn sieci Web usług IIS działających w systemie operacyjnym gościa. |
| Dzienniki aplikacji     | Śledzenie wiadomości napisanych przez aplikację. |
| Dzienniki usługi .NET EventSource |Kody zapisu zdarzeń przy użyciu klasy [.NET EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) |
| [Dzienniki ETW oparte na manifestach](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Śledzenie zdarzeń dla zdarzeń systemu Windows generowanych przez dowolny proces. |
| Zrzuty awaryjne (dzienniki)   | Informacje o stanie procesu w przypadku awarii aplikacji. |
| Dzienniki oparte na plikach    | Dzienniki utworzone przez aplikację lub usługę. |
| Dzienniki diagnostyczne agenta | Informacje o samej diagnostyce platformy Azure. |


### <a name="linux-diagnostics-extension-lad"></a>Rozszerzenie diagnostyki systemu Linux (LAD)

| Źródło danych | Opis |
| --- | --- |
| Dziennik systemu | Zdarzenia wysyłane do systemu rejestrowania zdarzeń systemu Linux.   |
| Liczniki wydajności  | Wartości liczbowe mierzące wydajność różnych aspektów systemu operacyjnego i obciążeń. |
| Pliki dziennika | Wpisy wysyłane do dziennika opartego na plikach.  |

## <a name="data-destinations"></a>Miejsca docelowe danych
Rozszerzenie diagnostyki platformy Azure dla systemów Windows i Linux zawsze zbiera dane na konto usługi Azure Storage. Zobacz [Instalowanie i konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (WAD)](diagnostics-extension-windows-install.md) i [Użyj rozszerzenia diagnostycznego systemu Linux do monitorowania metryk i dzienników, aby wyświetlić](../../virtual-machines/extensions/diagnostics-linux.md) listę określonych tabel i obiektów blob, w których te dane są zbierane.

Skonfiguruj co najmniej jeden *pochłaniacz danych,* aby wysyłać dane do innych dodatkowych miejsc docelowych. W poniższych sekcjach przedstawiono pochłaniacze dostępne dla rozszerzenia diagnostyki systemu Windows i Linux.

### <a name="windows-diagnostics-extension-wad"></a>Rozszerzenie diagnostyki systemu Windows (WAD)

| Element docelowy | Opis |
|:---|:---|
| Metryki usługi Azure Monitor | Zbieranie danych o wydajności do metryk usługi Azure Monitor. Zobacz [Wysyłanie metryk systemu operacyjnego gościa do bazy danych metryk usługi Azure Monitor](collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Usługa Event Hubs | Usługi Azure Event Hubs za pomocą usługi Azure Event Hubs do wysyłania danych poza platformą Azure. Zobacz [Przesyłanie strumieniowe danych diagnostyki platformy Azure do centrów zdarzeń](diagnostics-extension-stream-event-hubs.md) |
| Obiekty BLOB usługi Azure Storage | Zapisz do danych do obiektów blob w usłudze Azure Storage oprócz tabel. |
| Application Insights | Zbieranie danych z aplikacji uruchomionych na maszynie Wirtualnej do usługi Application Insights w celu zintegrowania z innym monitorowaniem aplikacji. Zobacz [Wysyłanie danych diagnostycznych do usługi Application Insights](diagnostics-extension-to-application-insights.md). |

Można również zbierać dane WAD z magazynu do obszaru roboczego usługi Log Analytics do analizy za pomocą dzienników usługi Azure Monitor, chociaż agent usługi Log Analytics jest zwykle używany dla tej funkcji. Może wysyłać dane bezpośrednio do obszaru roboczego usługi Log Analytics i obsługuje rozwiązania i szczegółowe informacje, które zapewniają dodatkowe funkcje.  Zobacz [Zbieranie dzienników diagnostycznych platformy Azure z usługi Azure Storage](diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>Rozszerzenie diagnostyki systemu Linux (LAD)
LAD zapisuje dane w tabelach w usłudze Azure Storage. Obsługuje zlewozmywaki w poniższej tabeli.

| Element docelowy | Opis |
|:---|:---|
| Usługa Event Hubs | Usługi Azure Event Hubs za pomocą usługi Azure Event Hubs do wysyłania danych poza platformą Azure. |
| Obiekty BLOB usługi Azure Storage | Zapisz do danych do obiektów blob w usłudze Azure Storage oprócz tabel. |
| Metryki usługi Azure Monitor | Zainstaluj agenta Telegraf oprócz LAD. Zobacz [Zbieranie niestandardowych metryk dla maszyny Wirtualnej z systemem Linux za pomocą agenta InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Instalacja i konfiguracja
Rozszerzenie diagnostyki jest implementowane jako [rozszerzenie maszyny wirtualnej](../../virtual-machines/extensions/overview.md) na platformie Azure, więc obsługuje te same opcje instalacji przy użyciu szablonów Menedżera zasobów, programu PowerShell i interfejsu wiersza polecenia. Zobacz [rozszerzenia i funkcje maszyn wirtualnych dla](../../virtual-machines/extensions/features-windows.md) [rozszerzeń i funkcji systemu Windows i maszyny wirtualnej dla systemu Linux, aby](../../virtual-machines/extensions/features-linux.md) uzyskać ogólne informacje na temat instalowania i utrzymywania rozszerzeń maszyn wirtualnych.

Można również zainstalować i skonfigurować rozszerzenie diagnostyczne systemu Windows i Linux w witrynie Azure portal w obszarze **Ustawienia diagnostyczne** w sekcji **Monitorowanie** menu maszyny wirtualnej.

Zobacz następujące artykuły, aby uzyskać szczegółowe informacje na temat instalowania i konfigurowania rozszerzenia diagnostyki dla systemów Windows i Linux.

- [Instalowanie i konfigurowanie rozszerzenia diagnostyki systemu Windows Azure (WAD)](diagnostics-extension-windows-install.md)
- [Używanie rozszerzenia diagnostycznego systemu Linux do monitorowania metryk i dzienników](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Inna dokumentacja

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Role usługi w chmurze azure (klasyczna) i rola procesu roboczego
- [Wprowadzenie do monitorowania usług w chmurze](../../cloud-services/cloud-services-how-to-monitor.md)
- [Włączanie diagnostyki platformy Azure w usługach w chmurze platformy Azure](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Usługa Application Insights dla usług w chmurze platformy Azure](../app/cloudservices.md)<br>[Śledzenie przepływu aplikacji usług w chmurze za pomocą diagnostyki platformy Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Monitor and diagnose services in a local machine development setup (Monitorowanie i diagnozowanie usług w konfiguracji środowiska deweloperskiego na maszynie lokalnej)](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Następne kroki


* Dowiedz się, jak [używać liczników wydajności w diagnostyce platformy Azure.](../../cloud-services/diagnostics-performance-counters.md)
* Jeśli masz problemy z uruchamianiem diagnostyki lub znajdowaniem danych w tabelach magazynu platformy Azure, zobacz Rozwiązywanie problemów z [diagnostyką platformy Azure](diagnostics-extension-troubleshooting.md)

