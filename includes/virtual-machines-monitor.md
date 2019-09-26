---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: 11c9b2ea3ea054415f25f864651df28288aa0025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266844"
---
Można korzystać z wielu możliwości monitorowania maszyn wirtualnych przez zbieranie, wyświetlanie i analizowanie danych diagnostycznych i dzienników. Aby wykonać proste [monitorowanie](../articles/azure-monitor/overview.md) maszyny wirtualnej, można użyć ekranu przegląd dla maszyny wirtualnej w Azure Portal. Za pomocą [rozszerzeń](../articles/virtual-machines/windows/extensions-features.md) można skonfigurować diagnostykę na maszynach wirtualnych w celu zbierania dodatkowych danych metryk. Możesz również użyć bardziej zaawansowanych opcji monitorowania, takich jak [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) i [log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnostyka i metryki 

Można skonfigurować i monitorować zbieranie [danych diagnostycznych](https://docs.microsoft.com/cli/azure/vm/diagnostics) przy użyciu [metryk](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) w Azure Portal, interfejsu wiersza polecenia platformy Azure, Azure PowerShell i programowania aplikacji programistycznych (API). Można na przykład:

- **Obserwuj Podstawowe metryki dla maszyny wirtualnej.** Na ekranie przeglądu Azure Portal są wyświetlane podstawowe metryki obejmują użycie procesora, użycie sieci, całkowitą liczbę bajtów dysku i operacje na dyskach na sekundę.

- **Włącz zbieranie danych diagnostycznych rozruchu i wyświetlanie ich przy użyciu Azure Portal.** W przypadku przenoszenia własnego obrazu na platformę Azure lub nawet uruchamiania jednego z obrazów platformy może istnieć wiele przyczyn, dla których maszyna wirtualna przechodzi w stan rozruchowy. Możesz łatwo włączyć diagnostykę rozruchu podczas tworzenia maszyny wirtualnej, klikając pozycję **włączone** dla diagnostyki rozruchu w sekcji Monitorowanie ekranu ustawienia.

    W przypadku rozruchu maszyn wirtualnych agent diagnostyki rozruchu przechwytuje dane wyjściowe rozruchu i przechowuje je w usłudze Azure Storage. Dane te mogą posłużyć do rozwiązywania problemów związanych z rozruchem maszyny wirtualnej. Diagnostyka rozruchu nie jest włączana automatycznie podczas tworzenia maszyny wirtualnej przy użyciu narzędzi wiersza polecenia. Przed włączeniem diagnostyki rozruchu należy utworzyć konto magazynu do przechowywania dzienników rozruchu. Po włączeniu diagnostyki rozruchu w Azure Portal konto magazynu zostanie automatycznie utworzone.

    Jeśli nie włączono diagnostyki rozruchu podczas tworzenia maszyny wirtualnej, zawsze można ją włączyć później przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)lub [szablonu Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Włącz zbieranie danych diagnostycznych systemu operacyjnego gościa.** Podczas tworzenia maszyny wirtualnej możesz korzystać z ekranu ustawienia, aby włączyć diagnostykę systemu operacyjnego gościa. Po włączeniu zbierania danych diagnostycznych [rozszerzenie IaaSDiagnostics dla systemu Linux](../articles/virtual-machines/linux/diagnostic-extension.md) lub [rozszerzenie IaaSDiagnostics dla Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) jest dodawane do maszyny wirtualnej, co umożliwia zbieranie dodatkowych danych dotyczących dysku, procesora i pamięci.

    Korzystając z zebranych danych diagnostycznych, można skonfigurować Skalowanie automatyczne dla maszyn wirtualnych. Możesz również skonfigurować dzienniki do przechowywania danych i skonfigurować alerty, aby poinformować o tym, kiedy wydajność nie jest w pełni odpowiednia.

## <a name="alerts"></a>Alerty

Możesz tworzyć [alerty](../articles/azure-monitor/platform/alerts-overview.md) na podstawie określonych metryk wydajności. Przykłady problemów, dla których można otrzymywać alerty w przypadku, gdy średnie użycie procesora CPU przekracza określony próg lub ilość wolnego miejsca na dysku spadnie poniżej określonej ilości. Alerty można skonfigurować w [Azure Portal](../articles/azure-monitor/platform/alerts-classic-portal.md)przy użyciu [Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell)lub [interfejsu wiersza polecenia platformy Azure](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md) udostępnia spersonalizowane wskazówki i pomoc techniczną w przypadku problemów z usługami platformy Azure i ułatwia przygotowanie się do nadchodzącej planowanej konserwacji. Azure Service Health alerty użytkownika i Twoich zespołów przy użyciu funkcji i elastycznych powiadomień.

## <a name="azure-resource-health"></a>Azure Resource Health

[Usługa Azure Resource Health](../articles/service-health/resource-health-overview.md) pomaga diagnozować i uzyskiwać pomoc techniczną, gdy problem z platformą Azure ma wpływ na zasoby. Informuje o bieżącej i wcześniejszej kondycji zasobów oraz pomaga uniknąć problemów. Usługa Resource Health oferuje pomoc techniczną w przypadku problemów z usługą platformy Azure.

## <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure

[Dziennik aktywności platformy Azure](../articles/azure-monitor/platform/activity-logs-overview.md) to dziennik subskrypcji, który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Dziennik zawiera szereg danych, z Azure Resource Manager dane operacyjne do aktualizacji Service Health zdarzeń. Możesz kliknąć pozycję Dziennik aktywności w Azure Portal, aby wyświetlić dziennik dla maszyny wirtualnej.

W przypadku dziennika aktywności można wykonać następujące czynności:

- Utwórz alert dotyczący [zdarzenia dziennika aktywności](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Przesyłaj strumieniowo do centrum zdarzeń](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) w celu pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie do analizy, takie jak PowerBI.
- Analizuj ją w usłudze PowerBI przy użyciu [pakietu zawartości](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)usługi Power BI.
- [Zapisz go na koncie magazynu](../articles/azure-monitor/platform/archive-activity-log.md) , aby przeprowadzić archiwizację lub inspekcję ręczną. Możesz określić czas przechowywania (w dniach) przy użyciu profilu dziennika.

Możesz również uzyskać dostęp do danych dziennika aktywności przy użyciu [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor)lub [monitorować interfejsy API REST](https://docs.microsoft.com/rest/api/monitor/).

[Dzienniki zasobów platformy Azure](../articles/azure-monitor/platform/resource-logs-overview.md) to dzienniki emitowane przez MASZYNę wirtualną, które zapewniają rozbudowane, częste dane dotyczące operacji. Dzienniki zasobów różnią się od dziennika aktywności, zapewniając wgląd w operacje wykonywane w ramach maszyny wirtualnej.

Poniżej przedstawiono niektóre czynności, które można wykonać za pomocą dzienników diagnostycznych:

- [Zapisz je na koncie magazynu](../articles/azure-monitor/platform/archive-diagnostic-logs.md) , aby przeprowadzić inspekcję lub inspekcję ręczną. Możesz określić czas przechowywania (w dniach) przy użyciu ustawień diagnostycznych zasobu.
- [Przesyłaj strumieniowo do Event Hubs](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) na potrzeby pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie do analizy, takie jak PowerBI.
- Analizuj je za pomocą [log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Zaawansowane monitorowanie

- [Azure monitor](../articles/azure-monitor/overview.md) to usługa, która monitoruje środowiska chmurowe i lokalne w celu utrzymania ich dostępności i wydajności. Oferuje kompleksowe rozwiązanie do zbierania, analizowania i działania w ramach telemetrii w środowiskach w chmurze i lokalnych. Pomaga interpretować działanie aplikacji i proaktywnie identyfikuje problemy dotyczące aplikacji i zasobów, od których zależą. Można zainstalować rozszerzenie na [maszynie wirtualnej](../articles/virtual-machines/linux/extensions-oms.md) z systemem Linux lub [maszynie wirtualnej systemu Windows](../articles/virtual-machines/windows/extensions-oms.md) , która instaluje agenta log Analytics, aby zbierać dane dziennika i magazyn w obszarze roboczym log Analytics.

    W przypadku maszyn wirtualnych z systemami Windows i Linux zalecaną metodą zbierania dzienników jest zainstalowanie agenta Log Analytics. Najprostszym sposobem instalowania agenta Log Analytics na maszynie wirtualnej jest [rozszerzenie maszyny wirtualnej log Analytics](../articles/log-analytics/log-analytics-azure-vm-extension.md). Użycie tego rozszerzenia upraszcza proces instalacji i automatycznie konfiguruje agenta do przesyłania danych do określonego obszaru roboczego usługi Log Analytics. Agent jest również automatycznie uaktualniany, co zapewnia, że posiadane funkcje i poprawki są zawsze najnowsze.

- [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) pozwala monitorować maszynę wirtualną i skojarzone z nią zasoby, ponieważ odnoszą się one do sieci, w której się znajdują. Rozszerzenie agenta Network Watcher można zainstalować na [maszynie wirtualnej systemu Linux](../articles/virtual-machines/linux/extensions-nwa.md) lub [maszynie wirtualnej z systemem Windows](../articles/virtual-machines/windows/extensions-nwa.md).

- [Azure monitor dla maszyn wirtualnych](../articles/azure-monitor/insights/vminsights-overview.md) monitoruje maszyny wirtualne platformy Azure na dużą skalę, analizując wydajność i kondycję maszyn wirtualnych z systemami Windows i Linux, w tym różne procesy i połączone zależności od innych zasobów i zewnętrznych przetwarzające. 

## <a name="next-steps"></a>Następne kroki
- Wykonaj kroki opisane w sekcji [monitorowanie maszyny wirtualnej z systemem Windows przy użyciu Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) lub [monitorowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Dowiedz się więcej o najlepszych rozwiązaniach dotyczących [monitorowania i diagnostyki](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
