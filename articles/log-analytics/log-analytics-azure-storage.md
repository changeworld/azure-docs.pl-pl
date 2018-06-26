---
title: Zbierać dzienniki usługi Azure i metryki dla Log Analytics | Dokumentacja firmy Microsoft
description: Skonfiguruj diagnostyki na zasobów platformy Azure można zapisać dzienników i metryk do analizy dzienników.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9e8c930acd12b5197238be48722947dac5380be6
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751415"
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Zbieranie dzienników usługi Azure i metryk do użycia w analizy dzienników

Istnieją cztery różne sposoby zbierania dzienników i metryki dla usług Azure:

1. Diagnostyka Azure bezpośrednio do analizy dzienników (*diagnostyki* w tabeli poniżej)
2. Diagnostyka Azure do magazynu Azure do analizy dzienników (*magazynu* w tabeli poniżej)
3. Łączniki dla usług Azure (*łączniki* w tabeli poniżej)
4. Skrypty do zbierania danych do analizy dzienników (puste wartości w poniższej tabeli oraz usług, które nie są wymienione)


| Usługa                 | Typ zasobu                           | Dzienniki        | Metryki     | Rozwiązanie |
| --- | --- | --- | --- | --- |
| Bramy aplikacji    | Microsoft.Network/applicationGateways   | Diagnostyka | Diagnostyka | [Analiza bramy aplikacji Azure](log-analytics-azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Application Insights    |                                         | Łącznik   | Łącznik   | [Application Insights łącznik](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (wersja zapoznawcza) |
| Konta usługi Automation     | Microsoft.Automation/AutomationAccounts | Diagnostyka |             | [Więcej informacji](../automation/automation-manage-send-joblogs-log-analytics.md)|
| Konta usługi Batch          | Microsoft.Batch/batchAccounts           | Diagnostyka | Diagnostyka | |
| Usługi w chmurze klasycznego  |                                         | Magazyn     |             | [Więcej informacji](log-analytics-azure-storage-iis-table.md) |
| Usługi poznawcze      | Microsoft.CognitiveServices/accounts    |             | Diagnostyka | |
| Data Lake analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnostyka |             | |
| Data Lake store         | Microsoft.DataLakeStore/accounts        | Diagnostyka |             | |
| Przestrzeń nazw centrum zdarzeń     | Microsoft.EventHub/namespaces           | Diagnostyka | Diagnostyka | |
| Centra IoT Hub                | Microsoft.Devices/IotHubs               |             | Diagnostyka | |
| Usługa Key Vault               | Microsoft.KeyVault/vaults               | Diagnostyka |             | [KeyVault Analytics](log-analytics-azure-key-vault.md) |
| Moduły równoważenia obciążenia          | Microsoft.Network/loadBalancers         | Diagnostyka |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnostyka | Diagnostyka | |
| Grupy zabezpieczeń sieci | Microsoft.Network/networksecuritygroups | Diagnostyka |             | [Grupy zabezpieczeń sieci Azure analityka](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Magazyny odzyskiwania         | Microsoft.RecoveryServices/vaults       |             |             | [Usługa Azure Recovery usługi Analytics (wersja zapoznawcza)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Usługi wyszukiwania         | Microsoft.Search/searchServices         | Diagnostyka | Diagnostyka | |
| Przestrzeń nazw magistrali usług   | Microsoft.ServiceBus/namespaces         | Diagnostyka | Diagnostyka | [Analiza magistrali usług (wersja zapoznawcza)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Magazyn     |             | [Usługa sieci szkieletowej Analytics (wersja zapoznawcza)](log-analytics-service-fabric.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnostyka | [Analiza Azure SQL (wersja zapoznawcza)](log-analytics-azure-sql.md) |
| Magazyn                 |                                         |             | Skrypt      | [Usługa Azure Storage Analytics (wersja zapoznawcza)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Maszyny wirtualne        | Microsoft.Compute/virtualMachines       | Wewnętrzny   | Wewnętrzny <br> Diagnostyka  | |
| Zestawy skalowania maszyn wirtualnych | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnostyka | |
| Farmach serwerów sieci Web        | Microsoft.Web/serverfarms               |             | Diagnostyka | |
| Witryny sieci Web               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnostyka | [Analiza aplikacji sieci Web platformy Azure (wersja zapoznawcza)](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> Do monitorowania maszyn wirtualnych platformy Azure (Linux i Windows), zaleca się zainstalowanie [rozszerzenia maszyny Wirtualnej analizy dziennika](log-analytics-azure-vm-extension.md). Agent zapewnia szczegółowe informacje zebrane w maszynach wirtualnych. Można również użyć rozszerzenia dla zestawy skalowania maszyny wirtualnej.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Diagnostyka Azure bezpośrednio do analizy dzienników
Możliwość zapisywania dzienników diagnostycznych są wielu zasobów platformy Azure i metryki bezpośrednio do analizy dzienników i to jest preferowany sposób zbierania danych do analizy. Podczas korzystania z diagnostyki Azure, dane są zapisywane bezpośrednio do analizy dzienników i nie musi najpierw zapisać danych do magazynu.

Zasobów platformy Azure, które obsługują [Azure monitor](../monitoring-and-diagnostics/monitoring-overview.md) wysłać ich dzienniki i metryki bezpośrednio do analizy dzienników.

> [!NOTE]
> Wysyłanie metryk wielowymiarowych do usługi Log Analytics za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak podczas eksportowania za pomocą ustawień diagnostycznych metryka jest odzwierciedlona jako komunikaty przychodzące we wszystkich kolejek zdarzeń koncentratora.
>
>

* Aby uzyskać szczegółowe informacje dostępne metryki, zapoznaj się [obsługiwane metryki z monitorem Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md).
* Szczegóły dostępne dzienniki, można znaleźć w [obsługiwanych usług i schematu dla dzienników diagnostycznych](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Włączanie diagnostyki przy użyciu programu PowerShell
Należy listopada 2016 (v2.3.0) lub nowszej wersji usługi [programu Azure PowerShell](/powershell/azure/overview).

W poniższym przykładzie programu PowerShell pokazano, jak używać [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting) włączyć diagnostyki w grupie zabezpieczeń sieci. Te same podejście działa w przypadku wszystkich obsługiwanych zasobów — ustaw `$resourceId` do identyfikatora zasobu zasobu, aby włączyć diagnostyki.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzureRmDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Włącz diagnostykę z szablonami usługi Resource Manager

Umożliwia diagnostyki w zasobie, gdy jest tworzona i diagnostyki użytkowników do swojego obszaru roboczego analizy dzienników za pomocą szablonu podobny do przedstawionego poniżej. W tym przykładzie jest dla konta automatyzacji, ale działa dla wszystkich obsługiwanych typów zasobów.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Diagnostyka Azure do magazynu, a następnie do analizy dzienników

Do zbierania dzienników z w niektórych zasobów, użytkownik może wysłać dzienniki do magazynu Azure, a następnie skonfiguruj analizy dzienników odczytać dzienniki z magazynu.

Analiza dzienników umożliwia zbieranie danych diagnostycznych z usługi Azure storage dla następujących zasobów i dzienniki tego podejścia:

| Zasób | Dzienniki |
| --- | --- |
| Service Fabric |ETWEvent <br> Zdarzeń operacyjnych <br> Zdarzenie niezawodnego aktora <br> Zdarzenie niezawodne usługi |
| Maszyny wirtualne |Systemu Linux <br> Zdarzenie systemu Windows <br> Dziennik IIS <br> ETWEvent systemu Windows |
| Role sieci Web <br> Role procesów roboczych |Systemu Linux <br> Zdarzenie systemu Windows <br> Dziennik IIS <br> ETWEvent systemu Windows |

> [!NOTE]
> Naliczane są opłaty szybkości normalne danych platformy Azure dla magazynu i transakcji wysyłanie danych diagnostycznych na konto magazynu i podczas analizy dzienników odczytuje dane z konta magazynu.
>
>

Zobacz [magazyn obiektów blob służy do przechowywania usług IIS i tabeli dla zdarzeń](log-analytics-azure-storage-iis-table.md) Aby dowiedzieć się więcej na temat sposobu analizy dzienników mogą zbierać Dzienniki te.

## <a name="connectors-for-azure-services"></a>Łączniki dla usług Azure

Jest łącznikiem usługi Application Insights, dzięki czemu dane zebrane przez usługę Application Insights do wysłania do analizy dzienników.

Dowiedz się więcej o [łącznika usługi Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>Skrypty w celu zbierania, a następnie wysyłać danych do analizy dzienników

Dla usług Azure, które nie mają bezpośredniego sposób wysyłania dzienników i metryk do analizy dzienników skryptu automatyzacji Azure służy do zbierania dzienników i metryki. Skrypt może następnie wysyłać dane analizy dzienników przy użyciu [modułów zbierających dane interfejsu API](log-analytics-data-collector-api.md)

Galeria szablonu Azure ma [przykłady użycia usługi Automatyzacja Azure](https://azure.microsoft.com/resources/templates/?term=OMS) zbierania danych z usług i wysyłania go do analizy dzienników.

## <a name="next-steps"></a>Kolejne kroki

* [Użyj magazynu obiektów blob dla usług IIS i tabeli magazynu dla zdarzeń](log-analytics-azure-storage-iis-table.md) odczytać dzienniki dla tej diagnostyki zapisu w magazynie tabel lub dzienniki programu IIS zapisywane do magazynu obiektów blob usług Azure.
* [Włącz rozwiązań](log-analytics-add-solutions.md) zapewnienie wglądu w dane.
* [Użyj zapytań wyszukiwania](log-analytics-log-searches.md) do analizowania danych.
