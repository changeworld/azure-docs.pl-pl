---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: ac400c86af8236ff5d67b8b6fbf99f6f4b1d36c9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183204"
---
Można korzystać z zalet wiele okazji do monitorowania maszyn wirtualnych, zbierając, wyświetlanie i analizowanie danych diagnostycznych i dane dziennika. W prostych [monitorowania](../articles/azure-monitor/overview.md) ekran Przegląd maszyny wirtualnej, można użyć dla maszyny Wirtualnej w witrynie Azure portal. Możesz użyć [rozszerzenia](../articles/virtual-machines/windows/extensions-features.md) Aby skonfigurować diagnostykę na maszynach wirtualnych, aby zbierać dodatkowe dane metryk. Umożliwia również bardziej zaawansowane opcje monitorowania, takich jak [usługi Application Insights](../articles/azure-monitor/app/app-insights-overview.md) i [usługi Log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Dane diagnostyczne i metryki 

Można skonfigurować i monitorować zbiorem [dane diagnostyczne](https://docs.microsoft.com/cli/azure/vm/diagnostics) przy użyciu [metryki](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) w witrynie Azure portal, interfejsu wiersza polecenia platformy Azure, programu Azure PowerShell i programowania aplikacji programowanie interfejsów API. Można na przykład:

- **Obserwuj podstawowe metryki dla maszyny Wirtualnej.** Na ekranie Omówienie witryny Azure portal podstawowe metryki wyświetlane obejmują użycie procesora CPU, użycie sieci, w sumie Bajty dysku i operacji dysku na sekundę.

- **Włącz zbieranie diagnostyki rozruchu i wyświetlić je w witrynie Azure portal.** Podczas przełączania z własnego obrazu na platformie Azure lub nawet wykonywania rozruchu jednego z obrazów platformy, może wystąpić wiele przyczyn, dlaczego pobiera Maszynę wirtualną do stanu uniemożliwiającego. Łatwo można włączyć diagnostykę rozruchu, podczas tworzenia maszyny Wirtualnej, klikając **włączone** potrzeby diagnostyki rozruchu w sekcji monitorowanie ekranu ustawień.

    Jak rozruchu maszyny wirtualnej agenta diagnostyki rozruchu przechwytuje dane wyjściowe rozruchu i zapisuje go w usłudze Azure storage. Dane te mogą posłużyć do rozwiązywania problemów związanych z rozruchem maszyny wirtualnej. Diagnostyka rozruchu nie są włączane automatycznie podczas tworzenia maszyny Wirtualnej z poziomu narzędzi wiersza polecenia. Przed włączeniem diagnostyki rozruchu należy utworzyć konto magazynu do przechowywania dzienników rozruchu. Włączenie diagnostyki rozruchu w witrynie Azure portal, konto magazynu jest tworzone automatycznie dla Ciebie.

    Jeśli nie zostały włączone diagnostyki rozruchu, podczas tworzenia maszyny Wirtualnej, zawsze można włączyć je później za pomocą [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic), lub [szablonu usługi Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Włącz zbieranie danych diagnostycznych systemu operacyjnego gościa.** Podczas tworzenia maszyny Wirtualnej, masz szansę na ekranie ustawienia, aby włączyć diagnostykę systemu operacyjnego gościa. Po włączeniu funkcji zbierania danych diagnostycznych [IaaSDiagnostics rozszerzenie dla systemu Linux](../articles/virtual-machines/linux/diagnostic-extension.md) lub [IaaSDiagnostics rozszerzenia dla Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) zostanie dodany do maszyny Wirtualnej, co umożliwia zbieranie dodatkowych dane na dysku, Procesora i pamięci.

    Korzystanie z danych diagnostycznych zebranych, można skonfigurować automatyczne skalowanie dla maszyn wirtualnych. Można również skonfigurować dzienniki, aby przechowywać dane i Konfigurowanie alertów z informacją, gdy wydajność nie jest całkowicie poprawny.

## <a name="alerts"></a>Alerty

Możesz utworzyć [alerty](../articles/azure-monitor/platform/alerts-overview.md) oparciu o konkretne metryki wydajności. Przykłady problemów, które użytkownik może otrzymywać alerty o średniego użycia procesora CPU przekroczy określony próg lub dostępnego wolnego miejsca na dysku spada poniżej pewnej ilości. Alerty można skonfigurować w [witryny Azure portal](../articles/azure-monitor/platform/alerts-classic-portal.md)przy użyciu [programu Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell), lub [wiersza polecenia platformy Azure](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli).

## <a name="azure-service-health"></a>Azure Service Health

[Usługa Azure Service Health](../articles/service-health/service-health-overview.md) zapewnia spersonalizowane wskazówki i pomoc techniczna, gdy napotkasz problemy z usługami platformy Azure i pomaga należy przygotować się na nadchodzącą planowanej konserwacji. Usługa Azure Service Health powiadamia Ciebie i Twoje zespoły za pomocą ukierunkowanych i elastycznych powiadomień.

## <a name="azure-resource-health"></a>Azure Resource Health

[Kondycja zasobów Azure](../articles/service-health/resource-health-overview.md) pomaga diagnozować i uzyskać pomoc techniczną, gdy na Twoje zasoby wpływa jakiś problem z platformą Azure. Informuje o bieżącej i wcześniejszej kondycji zasobów oraz pomaga uniknąć problemów. Usługa Resource Health oferuje pomoc techniczną w przypadku problemów z usługą platformy Azure.

## <a name="azure-activity-log"></a>Dziennik aktywności platformy Azure

[Dziennika aktywności platformy Azure](../articles/azure-monitor/platform/activity-logs-overview.md) jest Dziennik subskrypcji, który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które miały miejsce w systemie Azure. Dziennik zawiera szeroką gamę danych z usługi Azure Resource Manager danych operacyjnych do aktualizacji dla zdarzeń kondycji usługi. Możesz kliknąć dziennika aktywności w witrynie Azure portal, aby wyświetlić dziennik dla maszyny Wirtualnej.

Oto niektóre rzeczy, które można zrobić z dziennika aktywności:

- Tworzenie [alertów dotyczących zdarzenia dziennika aktywności](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Stream go do Centrum zdarzeń](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) dla pozyskiwania przez usługi innych firm lub rozwiązania analizy niestandardowych, takich jak usługi Power BI.
- Analizowanie ich w usłudze Power BI przy użyciu [pakietu zawartości usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Zapisz go na koncie magazynu](../articles/azure-monitor/platform/archive-activity-log.md) inspekcji archiwizacji lub ręcznie. Można określić czas przechowywania (w dniach), korzystając z profilu dziennika.

Dane dzienników aktywności można także przejść za pomocą [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor), lub [interfejsów API REST usługi Monitor](https://docs.microsoft.com/rest/api/monitor/).

[Dzienniki diagnostyczne platformy Azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md) są dzienniki emitowane przez maszynę Wirtualną, które zapewniają bogate, często dane dotyczące jego działania. Dzienniki diagnostyczne różnią się od dziennika aktywności, zapewniając szczegółowe informacje o operacjach wykonywanych na maszynie wirtualnej.

Oto niektóre rzeczy, które można zrobić z dziennikami diagnostycznymi:

- [Zapisz je na konto magazynu](../articles/azure-monitor/platform/archive-diagnostic-logs.md) do wglądu, inspekcji czy ręcznie. Można określić czas przechowywania (w dniach), za pomocą ustawień diagnostycznych zasobu.
- [Stream je do usługi Event Hubs](../articles/azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) dla pozyskiwania przez usługi innych firm lub rozwiązania analizy niestandardowych, takich jak usługi Power BI.
- Analizuj je za pomocą [usługi Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Zaawansowane monitorowanie

- [Usługa Azure Monitor](../articles/azure-monitor/overview.md) to usługa, która monitoruje środowiska chmurowe lokalnych i w celu zachowania ich dostępności i wydajności. Ona zapewnia kompleksowe rozwiązanie umożliwiające obsługę zbieranie, analizowanie i przetwarzanie danych telemetrycznych z chmury i środowiskach lokalnych. Pomaga interpretować działanie aplikacji i proaktywnie identyfikuje problemy dotyczące aplikacji i zasobów, od których zależą. Można zainstalować rozszerzenia na [maszyny Wirtualnej systemu Linux](../articles/virtual-machines/linux/extensions-oms.md) lub [Windows VM](../articles/virtual-machines/windows/extensions-oms.md) , instaluje agenta usługi Log Analytics w taki sposób, aby zebrać dane dziennika i zapisać w obszarze roboczym usługi Log Analytics.

    Dla maszyn wirtualnych systemu Linux i Windows zalecaną metodą do zbierania dzienników jest po zainstalowaniu agenta usługi Log Analytics. Najprostszym sposobem zainstalowania agenta usługi Log Analytics na maszynie Wirtualnej jest użycie [rozszerzenia Log Analytics VM Extension](../articles/log-analytics/log-analytics-azure-vm-extension.md). Użycie tego rozszerzenia upraszcza proces instalacji i automatycznie konfiguruje agenta do przesyłania danych do określonego obszaru roboczego usługi Log Analytics. Agent jest również automatycznie uaktualniany, co zapewnia, że posiadane funkcje i poprawki są zawsze najnowsze.

- [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) umożliwia monitorowanie maszyny Wirtualnej i skojarzone z nią zasoby odnoszących się do sieci, w którym się znajdują. Można zainstalować rozszerzenia Agent usługi Network Watcher na [maszyny Wirtualnej systemu Linux](../articles/virtual-machines/linux/extensions-nwa.md) lub [Windows VM](../articles/virtual-machines/windows/extensions-nwa.md).

- [Usługa Azure Monitor dla maszyn wirtualnych](../articles/azure-monitor/insights/vminsights-overview.md) monitoruje usługi Azure virtual machines (VM) na dużą skalę, analizując wydajności i kondycji Windows i maszyn wirtualnych systemu Linux, łącznie z różnych procesów i wzajemnie połączonych zależności od innych zasobów i zewnętrzne procesy. 

## <a name="next-steps"></a>Kolejne kroki
- Przewodnik po krokach w [umożliwia monitorowanie maszyny wirtualnej Windows przy użyciu programu Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) lub [monitorowanie maszyny wirtualnej z systemem Linux przy użyciu wiersza polecenia platformy Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).
- Dowiedz się więcej o najlepszych rozwiązaniach dotyczących [monitorowania i diagnostyki](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
