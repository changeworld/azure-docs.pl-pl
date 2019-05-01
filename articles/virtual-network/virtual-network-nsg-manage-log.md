---
title: Zdarzenie grupy zabezpieczeń sieci i regułę licznika dzienniki diagnostyczne platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak włączyć regułę i zdarzeń licznika dzienniki diagnostyczne na potrzeby grupę zabezpieczeń sieci platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: b3225d8d2f9eb7ccd0f4087d93cd9c1d940783d9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64714682"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Rejestrowanie diagnostyczne dla grupy zabezpieczeń sieci

Sieciowa grupa zabezpieczeń (NSG) zawiera reguły, które blokują lub zezwalają na ruch do podsieci sieci wirtualnej i/lub interfejs sieciowy. Po włączeniu diagnostyczne dla sieciowych grup zabezpieczeń, możesz zalogować się następujące kategorie informacji:

* **Zdarzenie:** Zapisy są rejestrowane, dla których sieciowej grupy zabezpieczeń reguły są stosowane do maszyn wirtualnych, na podstawie adresu MAC. Stan reguły te są gromadzone co 60 sekund.
* **Licznik reguły:** Zawiera wpisy dla tyle razy, każda reguła sieciowej grupy zabezpieczeń są stosowane do odmowy lub zezwolić na ruch.

Dzienniki diagnostyczne są dostępne tylko dla sieciowych grup zabezpieczeń, wdrożone za pośrednictwem modelu wdrażania usługi Azure Resource Manager. Nie można włączyć rejestrowanie diagnostyczne dla sieciowych grup zabezpieczeń, wdrożone za pośrednictwem klasycznego modelu wdrażania. W celu lepszego zrozumienia dwóch modeli, zobacz [informacje o platformie Azure, modelami wdrażania](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Rejestrowanie diagnostyczne włączono oddzielnie *każdego* mają być zbierane dane diagnostyczne dla sieciowych grup zabezpieczeń. Jeśli interesuje Cię operacyjne, lub dzienniki aktywności, zamiast niego, zobacz Azure [rejestrowania aktywności](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enable-logging"></a>Włącz rejestrowanie

Możesz użyć [witryny Azure Portal](#azure-portal), [PowerShell](#powershell), lub [wiersza polecenia platformy Azure](#azure-cli) Aby włączyć rejestrowanie diagnostyczne.

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do [portalu](https://portal.azure.com).
2. Wybierz **wszystkich usług**, a następnie wpisz *sieciowe grupy zabezpieczeń*. Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
3. Zaznacz, aby włączyć rejestrowanie dla sieciowej grupy zabezpieczeń.
4. W obszarze **monitorowanie**, wybierz opcję **dzienniki diagnostyczne**, a następnie wybierz pozycję **Włącz diagnostykę**, jak pokazano na poniższej ilustracji:

   ![Włączanie diagnostyki](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. W obszarze **ustawień diagnostycznych**wprowadź lub wybierz poniższe informacje, a następnie wybierz **Zapisz**:

    | Ustawienie                                                                                     | Wartość                                                          |
    | ---------                                                                                   |---------                                                       |
    | Name (Nazwa)                                                                                        | Nazwa wybranej.  Na przykład: *myNsgDiagnostics*      |
    | **Zarchiwizuj na koncie magazynu**, **Stream do usługi event hub**, i **wysyłanie do usługi Log Analytics** | Możesz wybrać dowolną liczbę miejsc docelowych, zgodnie z wybraniu. Aby dowiedzieć się więcej na temat, zobacz [dziennika miejsc docelowych](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Wybierz jednego lub obu tych kategorii dziennika. Aby dowiedzieć się więcej na temat dane rejestrowane dla każdej kategorii, zobacz [dziennika kategorie](#log-categories).                                                                                                                                             |
6. Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [wyświetlanie i analizowanie dzienników](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Możesz uruchamiać polecenia, które należy wykonać w [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Azure Cloud Shell to bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Po uruchomieniu programu PowerShell z komputera, należy modułu Azure PowerShell w wersji 1.0.0 lub nowszym. Uruchom `Get-Module -ListAvailable Az` na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, trzeba będzie również uruchomić `Connect-AzAccount` zalogować się do platformy Azure za pomocą konta mającego [niezbędne uprawnienia](virtual-network-network-interface.md#permissions).

Aby włączyć rejestrowanie diagnostyczne, musisz mieć identyfikator istniejącej sieciowej grupie zabezpieczeń. Jeśli nie masz istniejącej sieciowej grupie zabezpieczeń, możesz utworzyć jedno z [New AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Pobierz sieciową grupę zabezpieczeń, który chcesz włączyć diagnostyki rejestrowanie dla za pomocą [Get AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Na przykład można pobrać sieciową grupę zabezpieczeń o nazwie *myNsg* znajdujące się w grupie zasobów o nazwie *myResourceGroup*, wprowadź następujące polecenie:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Można napisać dzienniki diagnostyczne na trzy typy docelowego. Aby uzyskać więcej informacji, zobacz [dziennika miejsc docelowych](#log-destinations). W tym artykule, dzienniki są wysyłane do *usługi Log Analytics* miejsca docelowego, na przykład. Pobieranie istniejącego obszaru roboczego usługi Log Analytics za pomocą [Get AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Na przykład, aby pobrać istniejący obszar roboczy o nazwie *myWorkspace* w grupie zasobów o nazwie *myWorkspaces*, wprowadź następujące polecenie:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Jeśli nie masz istniejącego obszaru roboczego, możesz utworzyć jeden z [New AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Istnieją dwie kategorie rejestrowania, który można włączyć dzienniki. Aby uzyskać więcej informacji, zobacz [dziennika kategorie](#log-categories). Włącz rejestrowanie diagnostyczne dla sieciowych grup zabezpieczeń przy użyciu [AzDiagnosticSetting zestaw](/powershell/module/az.monitor/set-azdiagnosticsetting). Poniższy przykład dzienniki zdarzeń i liczników dane kategorii do obszaru roboczego dla sieciowej grupy zabezpieczeń, za pomocą identyfikatorów dla sieciowej grupy zabezpieczeń i obszar roboczy, którego wcześniej pobrano:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Jeśli chcesz rejestrować dane dotyczące jednej kategorii lub innych, a nie oba, Dodaj `-Categories` możliwość poprzednie polecenie, a następnie *NetworkSecurityGroupEvent* lub *NetworkSecurityGroupRuleCounter*. Jeśli chcesz się zalogować na inne [docelowy](#log-destinations) niż obszar roboczy usługi Log Analytics, należy użyć odpowiednich parametrów dla platformy Azure [konta magazynu](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Centrum zdarzeń](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [wyświetlanie i analizowanie dzienników](#view-and-analyze-logs).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Możesz uruchamiać polecenia, które należy wykonać w [usługi Azure Cloud Shell](https://shell.azure.com/bash), albo w wyniku uruchomienia wiersza polecenia platformy Azure z Twojego komputera. Azure Cloud Shell to bezpłatna interaktywna powłoka. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli uruchamiasz interfejs wiersza polecenia z komputera, potrzebujesz wersji 2.0.38 lub nowszej. Uruchom `az --version` na komputerze, aby znaleźć zainstalowaną wersję. Jeśli musisz uaktualnić, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](/cli/azure/install-azure-cli?view=azure-cli-latest). Jeśli używasz interfejsu wiersza polecenia lokalnie, trzeba będzie również uruchomić `az login` zalogować się do platformy Azure za pomocą konta mającego [niezbędne uprawnienia](virtual-network-network-interface.md#permissions).

Aby włączyć rejestrowanie diagnostyczne, musisz mieć identyfikator istniejącej sieciowej grupie zabezpieczeń. Jeśli nie masz istniejącej sieciowej grupie zabezpieczeń, możesz utworzyć jedno z [tworzenie az sieciowej](/cli/azure/network/nsg#az-network-nsg-create).

Pobierz sieciową grupę zabezpieczeń, który chcesz włączyć diagnostyki rejestrowanie dla za pomocą [Pokaż sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg#az-network-nsg-show). Na przykład można pobrać sieciową grupę zabezpieczeń o nazwie *myNsg* znajdujące się w grupie zasobów o nazwie *myResourceGroup*, wprowadź następujące polecenie:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Można napisać dzienniki diagnostyczne na trzy typy docelowego. Aby uzyskać więcej informacji, zobacz [dziennika miejsc docelowych](#log-destinations). W tym artykule, dzienniki są wysyłane do *usługi Log Analytics* miejsca docelowego, na przykład. Aby uzyskać więcej informacji, zobacz [dziennika kategorie](#log-categories).

Włącz rejestrowanie diagnostyczne dla sieciowych grup zabezpieczeń przy użyciu [tworzenie az monitor diagnostic-settings](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). Poniższy przykład dzienniki zdarzeń i liczników dane kategorii do istniejącego obszaru roboczego o nazwie *myWorkspace*, który istnieje w grupie zasobów o nazwie *myWorkspaces*oraz identyfikator sieciowej grupy zabezpieczeń, możesz pobrać wcześniej:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Jeśli nie masz istniejącego obszaru roboczego, możesz utworzyć ją przy użyciu [witryny Azure portal](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Istnieją dwie kategorie rejestrowania, który można włączyć dzienniki.

Jeśli chcesz rejestrować dane dotyczące jednej kategorii lub innych, Usuń kategorii nie chcesz rejestrować dane dotyczące w poprzednim poleceniu. Jeśli chcesz się zalogować na inne [docelowy](#log-destinations) niż obszar roboczy usługi Log Analytics, należy użyć odpowiednich parametrów dla platformy Azure [konta magazynu](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Centrum zdarzeń](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [wyświetlanie i analizowanie dzienników](#view-and-analyze-logs).

## <a name="log-destinations"></a>Miejsca docelowe dziennika

Dane diagnostyczne mogą być:
- [Zapisane na koncie usługi Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), do wglądu, inspekcji czy ręcznie. Można określić czas przechowywania (w dniach), za pomocą ustawień diagnostycznych zasobu.
- [Przesyłane strumieniowo do Centrum zdarzeń](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) dla pozyskiwania przez usługi innych firm lub rozwiązania analizy niestandardowe, takie jak usługi Power BI.
- [Zapisywane do dzienników usługi Azure Monitor](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics).

## <a name="log-categories"></a>Kategorie dzienników

Dane w formacie JSON jest przeznaczony dla następujących kategorii dziennika:

### <a name="event"></a>Wydarzenie

Dziennik zdarzeń zawiera informacje o tym, jakie reguły sieciowej grupy zabezpieczeń są stosowane do maszyn wirtualnych, na podstawie adresu MAC. Dla każdego zdarzenia rejestrowane są następujące dane. W poniższym przykładzie dane są rejestrowane dla maszyny wirtualnej przy użyciu adresu IP 192.168.1.4 i adres MAC 00-0D-3A-92-6A-7C:

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
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Regułę licznika

Dziennik licznika reguła zawiera informacje o każdej reguły stosowane do zasobów. Następujące przykładowe dane jest rejestrowane za każdym razem, gdy reguła jest stosowana. W poniższym przykładzie dane są rejestrowane dla maszyny wirtualnej przy użyciu adresu IP 192.168.1.4 i adres MAC 00-0D-3A-92-6A-7C:

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
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> Źródłowy adres IP do komunikacji nie jest zalogowany. Aby umożliwić [rejestrowanie przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md) dla sieciowej grupy zabezpieczeń, która rejestruje wszystkie informacje o liczniku reguły, a także źródłowy adres IP, który zainicjował komunikacji. Dane dziennika przepływu sieciowej grupy zabezpieczeń są zapisywane na koncie usługi Azure Storage. Można analizować dane przy użyciu [traffic analytics](../network-watcher/traffic-analytics.md) możliwości usługi Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Wyświetlanie i analizowanie dzienników

Aby dowiedzieć się, jak wyświetlać dane dzienników diagnostycznych, zobacz [dzienniki diagnostyczne platformy Azure — omówienie](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). W przypadku wysłania danych diagnostycznych:
- **Dzienniki platformy Azure Monitor**: Możesz użyć [analizy grupy zabezpieczeń sieci](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) rozwiązania do lepszego wglądu w dane. Rozwiązanie udostępnia wizualizacje dla reguły sieciowej grupy zabezpieczeń, które blokują lub zezwalają na ruch na adres MAC interfejsu sieciowego w maszynie wirtualnej.
- **Konto usługi Azure Storage**: Dane są zapisywane do pliku PT1H.json. Możesz znaleźć:
  - Dziennik zdarzeń w następującej ścieżce: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Reguła dziennika liczników w następującej ścieżce: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [rejestrowania aktywności](../azure-monitor/platform/diagnostic-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), wcześniej znane jako inspekcji lub dzienniki operacyjne. Rejestrowanie aktywności jest domyślnie włączona, dla grup zabezpieczeń sieci utworzonych za pomocą dowolnego modelu wdrażania na platformie Azure. Aby ustalić, jakie operacje zostały zakończone na sieciowych grup zabezpieczeń w dzienniku aktywności, Wyszukaj wpisy, które zawierają następujące typy zasobów:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Aby dowiedzieć się, jak rejestrować informacje diagnostyczne, aby uwzględnić źródłowy adres IP dla każdego przepływu, zobacz [rejestrowanie przepływu sieciowych grup zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).