Możesz z łatwością [automatycznie skalować](../articles/monitoring-and-diagnostics/insights-autoscale-best-practices.md) Twojego [maszynach wirtualnych (VM)](../articles/virtual-machines/windows/overview.md) korzystając [zestawy skalowania maszyny wirtualnej](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) i [Skalowanie automatyczne funkcji platformy Azure Monitor](../articles/monitoring-and-diagnostics/monitoring-overview-autoscale.md). Maszyny wirtualne muszą być członkami skali ustawiono automatyczne skalowanie. Ten artykuł zawiera informacje, które pozwala na lepsze zrozumienie sposobu skalowania maszyn wirtualnych w pionie i w poziomie przy użyciu automatycznej i ręcznej metody.

## <a name="horizontal-or-vertical-scaling"></a>Skalowanie w poziomie lub pionie

Funkcja automatycznego skalowania monitora Azure tylko skalowanie w poziomie, wzrost ("out") lub spadek ("") liczby maszyn wirtualnych. Skalowanie w poziomie jest bardziej elastyczna w sytuacji, w chmurze, ponieważ umożliwia uruchamianie potencjalnie tysiące maszyn wirtualnych do obsługi obciążenia. Możesz skalować w poziomie, automatycznie lub ręcznie zmieniając pojemności (lub liczba wystąpień) zestaw skali. 

Skalowanie w pionie zachowuje tę samą liczbę maszyn wirtualnych, ale powoduje, że maszyny wirtualne więcej ("w górę") lub mniej ("wyłączone") zaawansowane. Power jest mierzony w atrybutów, takich jak pamięci, szybkości procesora CPU lub miejsca na dysku. Skalowanie w pionie jest zależna od dostępności sprzętu większych i szybko trafienia górny limit i zależą od regionu. Skalowanie w pionie również zwykle wymaga maszyny Wirtualnej, aby zatrzymać i uruchomić ponownie. Skalowanie w pionie, ustawiając nowy rozmiar w konfiguracji w zestawie skalowania maszyn wirtualnych.

Używanie elementów runbook w [usługi Automatyzacja Azure](../articles/automation/automation-intro.md), możesz z łatwością [skalowania maszyn wirtualnych w zestawie skalowania](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md) w górę lub w dół.

## <a name="create-a-virtual-machine-scale-set"></a>Tworzenie zestawu skalowania maszyn wirtualnych

Zestawy skalowania ułatwiają wdrażanie i zarządzanie identycznych maszyn wirtualnych jako zestaw. Można utworzyć systemu Linux lub Windows skali ustawia przy użyciu [portalu Azure](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md), [programu Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md), lub [interfejsu wiersza polecenia Azure](../articles/virtual-machines/linux/tutorial-create-vmss.md). Można również utworzyć i zarządzać takie jak zestawy skalowania z zestawów SDK [Python](/develop/python) lub [Node.js](/nodejs/azure), lub bezpośrednio z [interfejsów API REST](/rest/api/compute/virtualmachinescalesets). Automatyczne skalowanie maszyn wirtualnych odbywa się przez zastosowanie metryki i reguły w zestawie skali.

## <a name="configure-autoscale-for-a-scale-set"></a>Konfigurowanie automatycznego skalowania dla zestawu skalowania

Automatyczne skalowanie zapewnia prawo liczby maszyn wirtualnych do obsługi obciążenia aplikacji. Umożliwia dodawanie maszyn wirtualnych w celu obsłużenia wzrost obciążenia i oszczędności, usuwając maszyn wirtualnych, które są obecne w stanie bezczynności. Należy określić minimalną i maksymalną liczbę maszyn wirtualnych do zestawu reguł. O minimalnej sprawia, że czy aplikacja zawsze działa nawet w przypadku obciążenia. Maksymalna wartość o ogranicza łącznym możliwy koszt co godzinę.

Można włączyć automatycznego skalowania, podczas tworzenia skali ustawić za pomocą [programu Azure PowerShell](../articles/monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings) lub [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings). Możesz je również włączyć po utworzeniu zestawu skalowania. Utwórz zestaw skali, należy zainstalować rozszerzenie i skonfigurować za pomocą automatycznego skalowania [szablonu usługi Azure Resource Manager](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md). W portalu Azure włączyć automatycznego skalowania z monitora Azure lub skalowania automatycznego z ustawień zestaw skalowania.

![Włącz skalowanie automatyczne](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>Metryki

Funkcja automatycznego skalowania monitora Azure umożliwia skalowanie liczba uruchomionych maszyn wirtualnych w lub w dół na podstawie [metryki](../articles/monitoring-and-diagnostics/insights-autoscale-common-metrics.md). Domyślnie maszyn wirtualnych zapewnia podstawowe metryki na poziomie hosta dla dysków, sieci i użycie procesora CPU. Konfigurując zbierania danych diagnostycznych przy użyciu rozszerzenia diagnostycznych, liczniki wydajności systemu operacyjnego gościa dodatkowe staną się dostępne dla dysku, procesora CPU i pamięci.

![Metryki kryteriów](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

Jeśli aplikacja wymaga można skalować na podstawie metryk, które nie są dostępne za pośrednictwem hosta, a następnie w zestawie skalowania maszyn wirtualnych musi być albo [rozszerzenia diagnostycznych Linux](../articles/virtual-machines/linux/diagnostic-extension.md) lub [rozszerzenie Diagnostyka systemu Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)zainstalowane. Jeśli tworzysz skali ustawić za pomocą portalu Azure, musisz również zainstalować rozszerzenie z konfiguracją diagnostyki, które należy użyć programu Azure PowerShell lub interfejsu wiersza polecenia Azure.
 
### <a name="rules"></a>Reguły

[Reguły](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) łączyć metrykę z akcji do wykonania. Gdy warunki reguły są spełnione, co najmniej jednej akcji skalowania automatycznego są wyzwalane. Na przykład może być zdefiniowana regułę, która zwiększa liczbę maszyn wirtualnych o 1, jeśli średnie użycie Procesora powyżej 85%.

![Akcji skalowania automatycznego](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>Powiadomienia

Możesz [ustawić wyzwalacze](../articles/monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md) określonej sieci web są określane jako adresy URL lub wiadomości e-mail są wysyłane na podstawie utworzonych reguł skalowania automatycznego. Elementów Webhook pozwalają kierować Azure powiadomień o alertach do innych systemów przetwarzania końcowego lub niestandardowych powiadomień.

## <a name="manually-scale-vms-in-a-scale-set"></a>Ręcznie skalować w zestawie skalowania maszyn wirtualnych

### <a name="horizontal"></a>Poziomo

Można dodawać i usuwać maszyn wirtualnych, zmieniając pojemność zestawu skalowania. W portalu Azure, można zmniejszyć lub zwiększyć liczbę maszyn wirtualnych (wyświetlane jako **wystąpienia liczba**) w skali ustawiony przez przesuwanie na pasku stanu zastąpienie na ekranie skalowanie w lewo lub w prawo.

Przy użyciu programu Azure PowerShell, należy uzyskać, używając obiektu zestawu skali [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss). Następnie ustaw **sku.capacity** właściwość, aby liczba maszyn wirtualnych, które mają i aktualizacji z zestawu skalowania [AzureRmVmss aktualizacji](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss). Przy użyciu wiersza polecenia platformy Azure, zmień pojemności z **— nowego miejsca** parametr [skali vmss az](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) polecenia.

### <a name="vertical"></a>Pionowa

Należy ręcznie zmienić rozmiar w portalu Azure na ekranie rozmiar dla zestawu skalowania maszyn wirtualnych. Za pomocą programu Azure PowerShell Get-AzureRmVmss, ustawienia właściwości sku obrazu odniesienia, a następnie za pomocą [AzureRmVmss aktualizacji](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) i [AzureRmVmssInstance aktualizacji](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o zestawy skalowania w [projektowania zestawach skali](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md).

