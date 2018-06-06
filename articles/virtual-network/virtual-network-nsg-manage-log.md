---
title: Zdarzenie grupy zabezpieczeń sieci i regułę licznika dzienników diagnostycznych platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć dzienników zdarzeń i regułę licznika diagnostycznych dla grupy zabezpieczeń sieci platformy Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: jdial
ms.openlocfilehash: c3f4a64c9e11d17899987bbe818506f61c415e3f
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757063"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Rejestrowanie diagnostyczne dla grupy zabezpieczeń sieci

Grupa zabezpieczeń sieci (NSG) zawiera reguły, które akceptować lub odrzucać ruch do podsieć sieci wirtualnej i/lub interfejs sieciowy. Po włączeniu diagnostyczne dla grupy NSG można rejestrować kategorii następujących informacji:

* **Zdarzenie:** są rejestrowane wpisy, dla których NSG reguły są stosowane do maszyn wirtualnych, na podstawie adresu MAC. Stan zasady te są gromadzone co 60 sekund.
* **Licznik reguł:** reguł zawiera wpisy dla ile razy każda grupa NSG jest stosowana do odmowy lub zezwolić na ruch.

Dzienniki diagnostyczne są dostępne tylko dla grup NSG wdrożone za pośrednictwem modelu wdrażania usługi Azure Resource Manager. Nie można włączyć rejestrowania diagnostycznego w celu grup NSG wdrożone za pośrednictwem klasycznego modelu wdrażania. Aby lepiej zrozumieć dwa modele, zobacz [modele wdrażania Azure opis](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Diagnostyczne dla jest włączone rejestrowanie oddzielnie *każdego* NSG, które mają być zbierane dane diagnostyczne. Jeśli interesuje Cię operacyjne, albo działania, rejestruje w dzienniku, zobacz Azure [rejestrowanie aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enable-logging"></a>Włącz rejestrowanie

Można użyć [Azure Portal](#azure-portal), lub [PowerShell](#powershell), aby włączyć rejestrowanie diagnostyczne.

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do [portal](https://portal.azure.com).
2. Wybierz **wszystkie usługi**, wpisz *sieciowej grupy zabezpieczeń*. Gdy **sieciowej grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
3. Wybierz grupy NSG, aby włączyć rejestrowanie.
4. W obszarze **monitorowanie**, wybierz pozycję **dzienników diagnostycznych**, a następnie wybierz **Włącz diagnostykę**, jak pokazano na poniższej ilustracji:
 
    ![Włączanie diagnostyki](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. W obszarze **ustawień diagnostycznych**, wprowadź, lub wybierz następujące informacje, a następnie wybierz **zapisać**:

    | Ustawienie                                                                                     | Wartość                                                          |
    | ---------                                                                                   |---------                                                       |
    | Name (Nazwa)                                                                                        | Nazwa wybrane.  Na przykład: *myNsgDiagnostics*      |
    | **Archiwum na konto magazynu**, **strumienia do Centrum zdarzeń**, i **wysyłać do analizy dzienników** | Można wybrać dowolną liczbę miejsc docelowych, wybierz polecenie. Aby dowiedzieć się więcej na temat, zobacz [dziennika miejsc docelowych](#log-destinations).                                                                                                                                           |
    | DZIENNIK                                                                                         | Wybierz jedną lub obie te kategorie dziennika. Aby dowiedzieć się więcej o danych zarejestrowane dla każdej kategorii, zobacz [dziennika kategorii](#log-categories).                                                                                                                                             |
6. Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [widoku i analizować dzienniki](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

Możesz uruchamiać polecenia, które należy wykonać w [powłoki chmury Azure](https://shell.azure.com/powershell), lub przez uruchomienie programu PowerShell z komputera. Powłoka chmury Azure jest bezpłatne powłoki interakcyjne. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Po uruchomieniu programu PowerShell z komputera, należy *AzureRM* modułu programu PowerShell, wersja 6.1.1 lub nowszym. Uruchom `Get-Module -ListAvailable AzureRM` na komputerze, aby znaleźć zainstalowanej wersji. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, należy uruchomić `Login-AzureRmAccount` do zalogowania się do platformy Azure przy użyciu konta, które ma [niezbędne uprawnienia](virtual-network-network-interface.md#permissions)].

Aby włączyć rejestrowanie diagnostyczne, należy identyfikator istniejącej grupy NSG. Jeśli nie masz istniejącej grupy NSG można utworzyć jedną z [AzureRmNetworkSecurityGroup nowy](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

Pobierz sieciową grupę zabezpieczeń, który chcesz włączyć diagnostyczne dla z [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup). Na przykład, aby pobrać grupy NSG o nazwie *myNsg* znajdujące się w grupie zasobów o nazwie *myResourceGroup*, wprowadź następujące polecenie:

```azurepowershell-interactive
$Nsg=Get-AzureRmNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Trzy typy docelowego może zapisać dzienników diagnostycznych. Aby uzyskać więcej informacji, zobacz [dziennika miejsc docelowych](#log-destinations). W tym artykule dzienniki są wysyłane do *analizy dzienników* docelowym, na przykład. Pobrać istniejący obszar roboczy analizy dzienników z [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace). Na przykład, aby pobrać istniejący obszar roboczy o nazwie *myLaWorkspace* w grupie zasobów o nazwie *LaWorkspaces*, wprowadź następujące polecenie:

```azurepowershell-interactive
$Oms=Get-AzureRmOperationalInsightsWorkspace `
  -ResourceGroupName LaWorkspaces `
  -Name myLaWorkspace
```

Jeśli nie masz istniejący obszar roboczy, możesz utworzyć jedną z [AzureRmOperationalInsightsWorkspace nowy](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace).

Istnieją dwie kategorie rejestrowania można włączyć dzienniki. Aby uzyskać więcej informacji, zobacz [dziennika kategorii](#log-categories). Włączanie rejestrowania diagnostyki dla grupy NSG z [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting). Poniższy przykład w dziennikach zarówno zdarzeń i licznik kategorii danych do obszaru roboczego grupy NSG, przy użyciu identyfikatorów NSG oraz obszaru roboczego, który można pobrać wcześniej:

```azurepowershell-interactive
Set-AzureRmDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Jeśli chcesz rejestrować dane jedną kategorię lub innych, a nie obie, Dodaj `-Categories` możliwość poprzedniego polecenia, a następnie *NetworkSecurityGroupEvent* lub *NetworkSecurityGroupRuleCounter*. Jeśli chcesz zalogować się do innej [docelowego](#log-destinations) niż obszar roboczy analizy dzienników użycia odpowiednie parametry dla platformy Azure [konta magazynu](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Centrum zdarzeń](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [widoku i analizować dzienniki](#view-and-analyze-logs).

## <a name="log-destinations"></a>Dziennik miejsc docelowych

Dane diagnostyczne można:
- [Zapisywane na koncie usługi Magazyn Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), dla inspekcji inspekcji lub ręcznie. Można określić czas przechowywania (w dniach) przy użyciu ustawień diagnostycznych zasobu.
- [Przesyłane strumieniowo do Centrum zdarzeń](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dla wprowadzanie przez usługi innej firmy lub rozwiązania analizy niestandardowych, takich jak usługi Power BI.
- [Zapisane Analiza dzienników Azure](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics).

## <a name="log-categories"></a>Kategorie dzienników

Dane w formacie JSON jest przeznaczony dla następujących kategorii dziennika:

### <a name="event"></a>Wydarzenie

Dziennik zdarzeń zawiera informacje o tym, które reguły NSG są stosowane do maszyn wirtualnych, na podstawie adresu MAC. Rejestrowane są następujące dane przykładowe dla każdego zdarzenia:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"[PORT RANGE]",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter"></a>Licznik reguł

Dziennik licznika reguła zawiera informacje o poszczególnych regułach stosowane do zasobów. Następujące przykładowe dane jest rejestrowane za każdym razem, których dotyczy reguła:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> Źródłowy adres IP do komunikacji nie jest zalogowany. Można włączyć [rejestrowania przepływu NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md) dla grupy NSG, która rejestruje wszystkie informacje o liczniku reguły, a także źródłowy adres IP, który zainicjował komunikacji. Dane dziennika przepływu sieciowej grupy zabezpieczeń są zapisywane na koncie usługi Azure Storage. Można analizować dane z [ruchu analytics](../network-watcher/traffic-analytics.md) możliwości obserwatora sieciowego Azure.

## <a name="view-and-analyze-logs"></a>Wyświetlanie i analizowanie dzienników

Aby dowiedzieć się, jak wyświetlać dane dzienników diagnostycznych, zobacz [dzienników diagnostycznych platformy Azure — omówienie](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). W przypadku wysłania danych diagnostycznych:
- **Zaloguj się Analytics**: można użyć [analytics grupy zabezpieczeń sieci](../log-analytics/log-analytics-azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-log-analytics
) rozwiązania, rozszerzoną szczegółowe informacje o. Rozwiązanie zawiera wizualizacje dla reguły NSG, które lub odmawiają ruchu na adres MAC interfejsu sieciowego na maszynie wirtualnej.
- **Konto usługi Azure Storage**: dane są zapisywane do pliku PT1H.json. Można znaleźć:
    - Dziennik zdarzeń w następującej ścieżce: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
    - Zasada dziennika liczników w następującej ścieżce: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [rejestrowanie aktywności](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), wcześniej znana jako inspekcji lub operacyjne dzienniki. Rejestrowanie aktywności jest domyślnie włączone dla grupy NSG został utworzony za pomocą obu modelu wdrożenia usługi Azure. Aby ustalić, jakie operacje zostały zakończone na grup NSG w dzienniku aktywności, Wyszukaj wpisy zawierające następujących zasobów:
    - Microsoft.ClassicNetwork/networkSecurityGroups
    - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
    - Microsoft.Network/networkSecurityGroups
    - Microsoft.Network/networkSecurityGroups/securityRules
- Aby dowiedzieć się, jak rejestrować informacje diagnostyczne, aby uwzględnić źródłowy adres IP dla każdego przepływu zobacz [rejestrowania przepływu NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).