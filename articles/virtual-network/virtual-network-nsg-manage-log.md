---
title: Rejestrowanie diagnostyczne dla sieciowej grupy zabezpieczeń
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak włączyć dzienniki diagnostyczne liczników zdarzeń i reguł dla sieciowej grupy zabezpieczeń platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 9829e713f19ab9755e9dc79d676446c8048e09b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751186"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Rejestrowanie diagnostyczne dla sieciowej grupy zabezpieczeń

Sieciowa grupa zabezpieczeń (NSG) zawiera reguły zezwalane lub odrzucane ruch do podsieci sieci wirtualnej, interfejsu sieciowego lub obu tych obrażeń. Po włączeniu rejestrowania diagnostycznego sieciowej grupy sieciowej można rejestrować następujące kategorie informacji:

* **Wydarzenie:** Wpisy są rejestrowane, dla których reguły sieciowej grupy płciowych są stosowane do maszyn wirtualnych, na podstawie adresu MAC.
* **Licznik reguł:** Zawiera wpisy dotyczące liczby razy każda reguła nsg jest stosowana do odmowy lub zezwalania na ruch. Stan tych reguł jest zbierany co 60 sekund.

Dzienniki diagnostyczne są dostępne tylko dla grup zabezpieczeń wdrożonych za pośrednictwem modelu wdrażania usługi Azure Resource Manager. Nie można włączyć rejestrowania diagnostycznego dla grup zabezpieczeń sieciowych wdrożonych za pośrednictwem klasycznego modelu wdrażania. Aby uzyskać lepsze zrozumienie dwóch modeli, zobacz [Opis modeli wdrażania platformy Azure.](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Rejestrowanie diagnostyczne jest włączone oddzielnie dla *każdej* sieciowej sieciowej sieciowej, dla której chcesz zbierać dane diagnostyczne. Jeśli jesteś zainteresowany działaniem operacyjnym lub aktywnością, dzienniki zamiast tego, zobacz [Rejestrowanie aktywności](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)platformy Azure .

## <a name="enable-logging"></a>Włącz rejestrowanie

Aby włączyć rejestrowanie diagnostyczne, można użyć [usługi Azure Portal](#azure-portal), [PowerShell](#powershell)lub [interfejsu wiersza polecenia platformy Azure.](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

1. Zaloguj się do [portalu](https://portal.azure.com).
2. Wybierz **pozycję Wszystkie usługi**, a następnie wpisz *sieciowe grupy zabezpieczeń*. Gdy w wynikach wyszukiwania pojawią się **sieciowe grupy zabezpieczeń,** wybierz je.
3. Wybierz nsg, dla którego chcesz włączyć rejestrowanie.
4. W obszarze **MONITOROWANIE**wybierz **pozycję Dzienniki diagnostyczne**, a następnie wybierz **pozycję Włącz diagnostykę**, jak pokazano na poniższej ilustracji:

   ![Włączanie diagnostyki](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. W obszarze **Ustawienia diagnostyki**wprowadź lub wybierz następujące informacje, a następnie wybierz pozycję **Zapisz:**

    | Ustawienie                                                                                     | Wartość                                                          |
    | ---------                                                                                   |---------                                                       |
    | Nazwa                                                                                        | Nazwa do wyboru.  Na przykład: *myNsgDiagnostics*      |
    | **Archiwizuj na koncie magazynu**, **Strumień do centrum zdarzeń**i Wyślij do usługi Log **Analytics** | Możesz wybrać dowolną liczbę miejsc docelowych. Aby dowiedzieć się więcej o każdym z nich, zobacz [Rejestrowanie miejsc docelowych](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Wybierz jedną lub obie kategorie dziennika. Aby dowiedzieć się więcej o danych zarejestrowanych dla każdej kategorii, zobacz [Kategorie dzienników](#log-categories).                                                                                                                                             |
6. Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i analizowanie dzienników](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można uruchomić polecenia, które należy wykonać w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchamiając program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli program PowerShell jest uruchamiany z komputera, potrzebny jest moduł programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom `Get-Module -ListAvailable Az` na komputerze, aby znaleźć zainstalowaną wersję. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, `Connect-AzAccount` musisz również uruchomić, aby zalogować się na platformę Azure przy za pomocą konta, które ma [niezbędne uprawnienia.](virtual-network-network-interface.md#permissions)

Aby włączyć rejestrowanie diagnostyczne, potrzebny jest identyfikator istniejącej sieciowej sieciowej sieciowej. Jeśli nie masz istniejącej sieciowej grupy zabezpieczeń, możesz ją utworzyć za pomocą [aplikacji New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Pobierz grupę zabezpieczeń sieci, dla której chcesz włączyć rejestrowanie diagnostyczne za pomocą [get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Na przykład, aby pobrać grupę sieciową o nazwie *myNsg,* która istnieje w grupie zasobów o nazwie *myResourceGroup*, wprowadź następujące polecenie:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Dzienniki diagnostyczne można zapisywać w trzech typach docelowych. Aby uzyskać więcej informacji, zobacz [Rejestrowanie miejsc docelowych](#log-destinations). W tym artykule dzienniki są wysyłane do miejsca docelowego *usługi Log Analytics,* na przykład. Pobierz istniejący obszar roboczy usługi Log Analytics za pomocą [programu Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Na przykład, aby pobrać istniejący obszar roboczy o nazwie *myWorkspace* w grupie zasobów o nazwie *myWorkspaces,* wprowadź następujące polecenie:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Jeśli nie masz istniejącego obszaru roboczego, możesz go utworzyć za pomocą [programu New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Istnieją dwie kategorie rejestrowania, dla których można włączyć dzienniki. Aby uzyskać więcej informacji, zobacz [Kategorie dziennika](#log-categories). Włącz rejestrowanie diagnostyczne sieciowej sieciowej sieciowej z [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). Poniższy przykład rejestruje dane zarówno zdarzeń, jak i kategorii liczników do obszaru roboczego dla administratora nsg, przy użyciu identyfikatorów dla bazy danych nsg i obszaru roboczego, które zostały pobrane wcześniej:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Jeśli chcesz rejestrować dane tylko dla jednej kategorii lub dla `-Categories` drugiej, a nie dla obu, dodaj opcję do poprzedniego polecenia, a następnie *NetworkSecurityGroupEvent* lub *NetworkSecurityGroupRuleCounter*. Jeśli chcesz zalogować się do innego [miejsca docelowego](#log-destinations) niż obszar roboczy usługi Log Analytics, użyj odpowiednich parametrów dla konta usługi Azure [Storage](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Centrum zdarzeń.](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i analizowanie dzienników](#view-and-analyze-logs).

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Można uruchomić polecenia, które należy wykonać w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchamiając narzędzie interfejsu wiersza polecenia platformy Azure z komputera. Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Jeśli komputer poprzetajny jest uruchamiany z komputera, potrzebujesz wersji 2.0.38 lub nowszej. Uruchom `az --version` na komputerze, aby znaleźć zainstalowaną wersję. Jeśli chcesz przeprowadzić uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Jeśli używasz interfejsu wiersza polecenia lokalnie, musisz również uruchomić, `az login` aby zalogować się na platformę Azure przy za pomocą konta, które ma niezbędne [uprawnienia](virtual-network-network-interface.md#permissions).

Aby włączyć rejestrowanie diagnostyczne, potrzebny jest identyfikator istniejącej sieciowej sieciowej sieciowej. Jeśli nie masz istniejącej sieciowej sieciowej, możesz ją utworzyć z [siecią nsg az.](/cli/azure/network/nsg#az-network-nsg-create)

Pobierz grupę zabezpieczeń sieci, dla której chcesz włączyć rejestrowanie diagnostyczne za pomocą [programu AZ Network nsg show](/cli/azure/network/nsg#az-network-nsg-show). Na przykład, aby pobrać grupę sieciową o nazwie *myNsg,* która istnieje w grupie zasobów o nazwie *myResourceGroup*, wprowadź następujące polecenie:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Dzienniki diagnostyczne można zapisywać w trzech typach docelowych. Aby uzyskać więcej informacji, zobacz [Rejestrowanie miejsc docelowych](#log-destinations). W tym artykule dzienniki są wysyłane do miejsca docelowego *usługi Log Analytics,* na przykład. Aby uzyskać więcej informacji, zobacz [Kategorie dziennika](#log-categories).

Włącz rejestrowanie diagnostyczne sieciowej sieciowej sieciowej z [funkcją tworzenia ustawień diagnostycznych monitora AZ](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). Poniższy przykład rejestruje zarówno dane zdarzeń, jak i kategorii liczników do istniejącego obszaru roboczego o nazwie *myWorkspace*, który istnieje w grupie zasobów o nazwie *myWorkspaces,* oraz identyfikatorowi grupy NSG pobranej wcześniej:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Jeśli nie masz istniejącego obszaru roboczego, możesz go utworzyć za pomocą [portalu Azure lub](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) programu [PowerShell.](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) Istnieją dwie kategorie rejestrowania, dla których można włączyć dzienniki.

Jeśli chcesz rejestrować dane tylko dla jednej lub drugiej kategorii, usuń kategorię, dla której nie chcesz rejestrować danych w poprzednim poleceniu. Jeśli chcesz zalogować się do innego [miejsca docelowego](#log-destinations) niż obszar roboczy usługi Log Analytics, użyj odpowiednich parametrów dla konta usługi Azure [Storage](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Centrum zdarzeń.](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Wyświetlanie i analizowanie dzienników. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i analizowanie dzienników](#view-and-analyze-logs).

## <a name="log-destinations"></a>Rejestrowanie miejsc docelowych

Dane diagnostyczne mogą być:
- [Napisane na koncie usługi Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)w celu inspekcji lub ręcznej inspekcji. Można określić czas przechowywania (w dniach) przy użyciu ustawień diagnostycznych zasobów.
- [Przesyłane strumieniowo do Centrum zdarzeń w](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) celu pozyskiwania przez usługę innej firmy lub niestandardowe rozwiązanie analityczne, takie jak PowerBI.
- [Napisane w dziennikach usługi Azure Monitor](../azure-monitor/platform/resource-logs-collect-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-categories"></a>Kategorie dzienników

Dane w formacie JSON są zapisywane dla następujących kategorii dziennika:

### <a name="event"></a>Wydarzenie

Dziennik zdarzeń zawiera informacje o tym, które reguły sieciowej grupy płciowych są stosowane do maszyn wirtualnych na podstawie adresu MAC. Następujące dane są rejestrowane dla każdego zdarzenia. W poniższym przykładzie dane są rejestrowane dla maszyny wirtualnej o adresie IP 192.168.1.4 i adresie MAC 00-0D-3A-92-6A-7C:

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

### <a name="rule-counter"></a>Licznik reguł

Dziennik licznika reguł zawiera informacje o każdej regule zastosowanej do zasobów. Poniższe przykładowe dane są rejestrowane za każdym razem, gdy reguła jest stosowana. W poniższym przykładzie dane są rejestrowane dla maszyny wirtualnej o adresie IP 192.168.1.4 i adresie MAC 00-0D-3A-92-6A-7C:

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
> Źródłowy adres IP komunikacji nie jest rejestrowany. Można jednak włączyć [rejestrowanie przepływu sieciowej](../network-watcher/network-watcher-nsg-flow-logging-portal.md) grupy sieciowej dla sieciowej grupy sieciowej, która rejestruje wszystkie informacje o liczniku reguł, a także źródłowy adres IP, który zainicjował komunikację. Dane dziennika przepływu sieciowej grupy zabezpieczeń są zapisywane na koncie usługi Azure Storage. Można analizować dane za pomocą funkcji [analizy ruchu](../network-watcher/traffic-analytics.md) Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Wyświetlanie i analizowanie dzienników

Aby dowiedzieć się, jak wyświetlić dane dziennika diagnostycznego, zobacz [omówienie dzienników diagnostycznych platformy Azure](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). W przypadku wysyłania danych diagnostycznych do:
- **Dzienniki usługi Azure Monitor:** Można użyć rozwiązania [do analizy sieciowej grupy zabezpieczeń](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) w celu uzyskania szczegółowych informacji. Rozwiązanie udostępnia wizualizacje reguł sieciowej listy właścicieli sieciowej, które zezwalają lub odmawiają ruchu interfejsu sieciowego na komputerze WIRTUALNYM lub odmawiają go.
- **Konto usługi Azure Storage:** Dane są zapisywane w pliku PT1H.json. Możesz znaleźć:
  - Logowanie zdarzeń w następującej ścieżce:`insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Log licznika reguł w następującej ścieżce:`insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [rejestrowaniu aktywności](../azure-monitor/platform/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), wcześniej nazywanym inspekcją lub dziennikami operacyjnymi. Rejestrowanie aktywności jest domyślnie włączone dla grup zabezpieczeń utworzonych za pośrednictwem modelu wdrażania platformy Azure. Aby ustalić, które operacje zostały zakończone na grupach NSG w dzienniku działań, poszukaj wpisów, które zawierają następujące typy zasobów:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Aby dowiedzieć się, jak rejestrować informacje diagnostyczne, aby uwzględnić źródłowy adres IP dla każdego przepływu, zobacz [Rejestrowanie przepływu sieciowej sieciowej](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
