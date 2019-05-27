---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 3c008e77116a9b42a2ea137069529c5e241adddd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160078"
---
Możesz z łatwością [automatyczne skalowanie](../articles/azure-monitor/platform/autoscale-best-practices.md) swoje [maszyn wirtualnych (VM)](../articles/virtual-machines/windows/overview.md) zastosowania [zestawy skalowania maszyn wirtualnych](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) i [funkcja automatycznego skalowania platformy Azure Monitor](../articles/azure-monitor/platform/autoscale-overview.md). Maszyny wirtualne muszą należeć do zestawu skalowania automatycznego skalowania. Ten artykuł zawiera informacje, która pozwala na lepsze zrozumienie, jak skalowanie w pionie i w poziomie przy użyciu automatycznej i ręcznej metody maszyn wirtualnych.

## <a name="horizontal-or-vertical-scaling"></a>Skalowanie w poziomie lub w pionie

Funkcja automatycznego skalowania usługi Azure monitor tylko jest skalowana w poziomie, zwiększenia ("out") lub spadek ("") liczby maszyn wirtualnych. Skalowanie w poziomie jest bardziej elastyczna w sytuacji, w chmurze, ponieważ pozwala ono potencjalnie uruchomienia tysięcy maszyn wirtualnych do obsługi obciążenia. Możesz skalować w poziomie, automatycznie lub ręcznie zmieniając pojemności (lub liczba wystąpień) w zestawie skalowania. 

Skalowanie w pionie zachowuje tę samą liczbę maszyn wirtualnych, ale sprawia, że maszyny wirtualne więcej ("do góry") lub mniej ("nie działa") zaawansowane. Moc jest mierzony w atrybutów, takich jak pamięci, szybkość procesora CPU lub miejsca na dysku. Skalowanie w pionie jest zależna od dostępności sprzętu większych i szybko osiągnie górny limit i może różnić między regionami. Skalowanie w pionie również zwykle wymaga maszyny Wirtualnej w celu zatrzymania i ponownego uruchomienia. Skalowanie w pionie, ustawiając nowy rozmiar w konfiguracji maszyn wirtualnych w zestawie skalowania.

Za pomocą elementów runbook w [usługi Azure Automation](../articles/automation/automation-intro.md), możesz z łatwością [skalowanie maszyn wirtualnych w zestawie skalowania](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) w górę lub w dół.

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

Zestawy skalowania ułatwiają wdrażanie i zarządzanie identycznych maszyn wirtualnych jako zestawu. Można utworzyć systemu Linux lub Windows scale sets przy użyciu [witryny Azure portal](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [programu Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md), lub [wiersza polecenia platformy Azure](../articles/virtual-machines/linux/tutorial-create-vmss.md). Można również tworzyć i zarządzania zestawami skalowania za pośrednictwem zestawów SDK, takimi jak [Python](https://azure.microsoft.com/develop/python/) lub [Node.js](/nodejs/azure), lub bezpośrednio z [interfejsów API REST](/rest/api/compute/virtualmachinescalesets). Automatyczne skalowanie maszyn wirtualnych odbywa się za pomocą metryk i reguł w zestawie skalowania.

## <a name="configure-autoscale-for-a-scale-set"></a>Konfigurowanie automatycznego skalowania dla zestawu skalowania

Automatyczne skalowanie zapewnia odpowiednią liczbę maszyn wirtualnych do obsługi obciążenia w swojej aplikacji. Umożliwia ona Dodawanie maszyn wirtualnych do obsługi zwiększenia obciążenia i oszczędzać pieniądze, usuwając maszyn wirtualnych, które są obecne w stanie bezczynności. Możesz określić minimalną i maksymalną liczbę maszyn wirtualnych do zestawu reguł. O minimalnej sprawia, że się, że Twoja aplikacja zawsze działa nawet przy braku obciążenia. O maksymalnej wartości ogranicza możliwości całkowity koszt na godzinę.

Można włączyć automatyczne skalowanie, podczas tworzenia zestawu skalowania przy użyciu [programu Azure PowerShell](../articles/azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings). Możesz również je włączyć po utworzeniu zestawu skalowania. Tworzenie zestawu skalowania, należy zainstalować rozszerzenie i Konfigurowanie automatycznego skalowania przy użyciu [szablonu usługi Azure Resource Manager](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). W witrynie Azure portal włączyć automatyczne skalowanie z usługi Azure Monitor lub skalowania automatycznego z ustawień zestawu skalowania.

![Włącz skalowanie automatyczne](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Metryki

Funkcja automatycznego skalowania usługi Azure Monitor pozwala na skalowanie liczba uruchomionych maszyn wirtualnych w lub w dół na podstawie [metryki](../articles/azure-monitor/platform/autoscale-common-metrics.md). Domyślnie maszyny wirtualne zapewniają podstawowe metryki na poziomie hosta dla dysku, sieci i użycie procesora CPU. Podczas konfigurowania zbierania danych diagnostycznych za pomocą diagnostycznego rozszerzenia, liczniki wydajności systemu operacyjnego gościa dodatkowe stają się dostępne dla dysku, Procesora i pamięci.

![Kryteria metryki](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Jeśli Twoja aplikacja potrzebuje skalowania na podstawie metryk, które nie są dostępne za pośrednictwem hosta, a następnie maszyn wirtualnych w zestawie skalowania muszą mieć [rozszerzenia diagnostycznego systemu Linux](../articles/virtual-machines/linux/diagnostic-extension.md) lub [rozszerzenie diagnostyki Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)zainstalowane. Jeśli utworzysz zestaw skalowania za pomocą witryny Azure portal, musisz również użyć programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure można zainstalować rozszerzenia z konfiguracji diagnostyki, które są potrzebne.
 
### <a name="rules"></a>Reguły

[Reguły](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) łączyć metryki z akcję do wykonania. Po spełnieniu warunków reguły, co najmniej jednej akcji skalowania automatycznego są wyzwalane. Na przykład Niewykluczone, że reguły zdefiniowane zwiększa liczbę maszyn wirtualnych przez 1, gdy średnie użycie procesora CPU przekroczy 85 procent.

![Akcje skalowania automatycznego](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Powiadomienia

Możesz [ustawić wyzwalacze](../articles/azure-monitor/platform/autoscale-webhook-email.md) tak, aby konkretne adresy URL są nazywane lub wiadomości e-mail są wysyłane na podstawie reguł skalowania automatycznego, które tworzysz. Elementy Webhook pozwalają kierować powiadomienia o alertach platformy Azure z innymi systemami powiadomień przetwarzania końcowego lub niestandardowy.

## <a name="manually-scale-vms-in-a-scale-set"></a>Ręczne skalowanie maszyn wirtualnych w zestawie skalowania

### <a name="horizontal"></a>Poziomo

Można dodać lub usunąć zmianę pojemności zestawu skalowania maszyn wirtualnych. W witrynie Azure portal, można zmniejszyć lub zwiększyć liczbę maszyn wirtualnych (wyświetlana jako **liczba wystąpień**) w zestawie skalowania za przesuwanie na pasku stanu zastąpienie na ekranie skalowanie w lewo lub w prawo.

Przy użyciu programu Azure PowerShell, należy uzyskać, używając obiektu zestawu skalowania [Get AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss). Następnie ustawiamy **sku.capacity** liczbę maszyn wirtualnych, które mają i zaktualizuj zestaw skalowania za pomocą [AzVmss aktualizacji](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss). Przy użyciu wiersza polecenia platformy Azure, możesz zmienić pojemnością przy użyciu **— nową pojemność** parametr [az vmss scale](/cli/azure/vmss?view=azure-cli-latest#az-vmss-scale) polecenia.

### <a name="vertical"></a>Pionowa

Można ręcznie zmienić rozmiar maszyn wirtualnych w witrynie Azure portal w rozmiaru ekranu dla zestawu skalowania. Za pomocą programu Azure PowerShell Get-AzVmss, ustawiając właściwość sku obrazu odniesienia, a następnie używając [AzVmss aktualizacji](https://docs.microsoft.com/powershell/module/az.compute/update-azvmss) i [AzVmssInstance aktualizacji](https://docs.microsoft.com/powershell/module/az.compute/update-azvmssinstance).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o zestawach skalowania w [zagadnienia dotyczące projektowania dla zestawów skalowania](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

