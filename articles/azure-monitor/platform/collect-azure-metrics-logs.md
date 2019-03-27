---
title: Zbieraj dzienniki usługi platformy Azure i metryki dla usługi Log Analytics | Dokumentacja firmy Microsoft
description: Skonfigurować diagnostykę dla zasobów platformy Azure do zapisywania dzienników i metryk do usługi Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: 034abe4e3c37c94afbe431a51efd9493b707fa89
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498540"
---
# <a name="collect-azure-service-logs-and-metrics-for-use-in-log-analytics"></a>Usługi platformy Azure zbieranie dzienników i metryk do użycia w usłudze Log Analytics

Istnieją cztery różne sposoby pobierania dzienników i metryk dla usług platformy Azure:

1. Narzędzie diagnostyczne systemu Azure bezpośrednio do usługi Log Analytics (*diagnostyki* w poniższej tabeli)
2. Diagnostyka Azure do usługi Azure storage do usługi Log Analytics (*magazynu* w poniższej tabeli)
3. Łączniki dla usług platformy Azure (*łączników* w poniższej tabeli)
4. Skrypty można zbierać, a następnie publikują dane w usłudze Log Analytics (puste w poniższej tabeli, jak i dla usług, które nie są wyświetlane)


| Usługa                 | Typ zasobu                           | Dzienniki        | Metryki     | Rozwiązanie |
| --- | --- | --- | --- | --- |
| Bramy aplikacji    | Microsoft.Network/applicationGateways   | Diagnostyka | Diagnostyka | [Usługi Azure Application Gateway Analytics](../../azure-monitor/insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-log-analytics) |
| Usługa Application insights    |                                         | Łącznik   | Łącznik   | [Łącznik usługi Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (wersja zapoznawcza) |
| Konta usługi Automation     | Microsoft.Automation/AutomationAccounts | Diagnostyka |             | [Więcej informacji](../../automation/automation-manage-send-joblogs-log-analytics.md)|
| Konta usługi Batch          | Microsoft.Batch/batchAccounts           | Diagnostyka | Diagnostyka | |
| Usługi w chmurze klasycznego  |                                         | Magazyn     |             | [Więcej informacji](azure-storage-iis-table.md) |
| Usługi poznawcze      | Microsoft.CognitiveServices/accounts    |             | Diagnostyka | |
| Data Lake analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnostyka |             | |
| Usługa Data Lake store         | Microsoft.DataLakeStore/accounts        | Diagnostyka |             | |
| Przestrzeń nazw centrum zdarzeń     | Microsoft.EventHub/namespaces           | Diagnostyka | Diagnostyka | |
| Centra IoT Hub                | Microsoft.Devices/IotHubs               |             | Diagnostyka | |
| Usługa Key Vault               | Microsoft.KeyVault/vaults               | Diagnostyka |             | [KeyVault Analytics](../../azure-monitor/insights/azure-key-vault.md) |
| Moduły równoważenia obciążenia          | Microsoft.Network/loadBalancers         | Diagnostyka |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnostyka | Diagnostyka | |
| Grupy zabezpieczeń sieci | Microsoft.Network/networksecuritygroups | Diagnostyka |             | [Analiza sieciowej grupy zabezpieczeń platformy Azure](../../azure-monitor/insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) |
| Magazyny usługi Recovery         | Microsoft.RecoveryServices/vaults       |             |             | [Usługa Azure Recovery Services — analiza (Podgląd)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Usługi wyszukiwania         | Microsoft.Search/searchServices         | Diagnostyka | Diagnostyka | |
| Przestrzeń nazw magistrali usług   | Microsoft.ServiceBus/namespaces         | Diagnostyka | Diagnostyka | [Usługa Service Bus analiza (Podgląd)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Magazyn     |             | [Analiza usługi Service Fabric (wersja zapoznawcza)](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnostyka | [Usługi Azure SQL Analytics (wersja zapoznawcza)](../../azure-monitor/insights/azure-sql.md) |
| Magazyn                 |                                         |             | Skrypt      | [Usługa Azure Storage Analytics (wersja zapoznawcza)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Maszyny wirtualne        | Microsoft.Compute/virtualMachines       | Wewnętrzny   | Wewnętrzny <br> Diagnostyka  | |
| Zestawy skalowania maszyn wirtualnych | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnostyka | |
| Farmach serwerów sieci Web        | Microsoft.Web/serverfarms               |             | Diagnostyka | |
| Witryny sieci Web               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnostyka | [Azure Web Apps Analytics (wersja zapoznawcza)](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> Do monitorowania maszyn wirtualnych platformy Azure (Linux i Windows), zaleca się zainstalowanie [rozszerzenia Log Analytics VM](../../azure-monitor/learn/quick-collect-azurevm.md). Agent udostępnia szczegółowe informacje zebrane z w obrębie maszyn wirtualnych. Można również użyć rozszerzenia dla zestawów skalowania maszyn wirtualnych.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Narzędzie diagnostyczne systemu Azure bezpośrednio do usługi Log Analytics
Wiele zasobów platformy Azure są w stanie zapisywać dzienniki diagnostyczne i metryki bezpośrednio do usługi Log Analytics i to jest preferowany sposób zbierania danych w celu analizy. Korzystając z usługi Diagnostyka Azure, dane są zapisywane bezpośrednio do usługi Log Analytics i nie ma potrzeby najpierw zapisać danych do magazynu.

Zasoby platformy Azure, które obsługują [usługa Azure monitor](../../azure-monitor/overview.md) wysłać ich dzienników i metryk bezpośrednio do usługi Log Analytics.

> [!NOTE]
> Wysyłanie metryk wielowymiarowych do usługi Log Analytics za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak podczas eksportowania za pomocą ustawień diagnostycznych metryka jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejek w zdarzeniu koncentratora.
>
>

* Szczegóły dostępne metryki, można znaleźć [metryki obsługiwane z usługą Azure Monitor](../../azure-monitor/platform/metrics-supported.md).
* Szczegóły dostępne dzienniki, można znaleźć [obsługiwane usługi i schematu dla dzienników diagnostycznych](../../azure-monitor/platform/diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Włączanie diagnostyki przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W poniższym przykładzie programu PowerShell pokazuje, jak używać [AzDiagnosticSetting zestaw](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) na włączanie diagnostyki na sieciową grupę zabezpieczeń. To samo podejście działa w przypadku wszystkich obsługiwanych zasobów — Konfigurowanie `$resourceId` do zasobu, aby włączyć diagnostykę dla identyfikatora zasobu.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Włączanie diagnostyki przy użyciu szablonów usługi Resource Manager

Aby włączyć diagnostyki dla zasobu po jego utworzeniu i diagnostyki wysłały do obszaru roboczego usługi Log Analytics można skorzystać z szablonu podobny do przedstawionego poniżej. W tym przykładzie jest dla konta usługi Automation, ale działa dla wszystkich obsługiwanych typów zasobów.

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

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Narzędzie diagnostyczne systemu Azure do magazynu, a następnie do usługi Log Analytics

Do zbierania dzienników z w ramach niektórych zasobów, jest to możliwe wysłać dzienniki do usługi Azure storage, a następnie skonfigurować usługi Log Analytics w celu odczytu dzienników z magazynu.

Tej metody można użyć usługi log Analytics na zbieranie danych diagnostycznych z usługi Azure storage dla następujących zasobów i dzienników:

| Zasób | Dzienniki |
| --- | --- |
| Service Fabric |ETWEvent <br> Zdarzeń operacyjnych <br> Zdarzenie Reliable Actors <br> Zdarzenia usługi Reliable Service |
| Maszyny wirtualne |Dzienników Syslog systemu Linux <br> Zdarzenia Windows <br> Dziennik IIS <br> Windows ETWEvent |
| Role sieci Web <br> Role procesów roboczych |Dzienników Syslog systemu Linux <br> Zdarzenia Windows <br> Dziennik IIS <br> Windows ETWEvent |

> [!NOTE]
> Opłaty są naliczane normalne danych platformy Azure stawek za magazyn i transakcje po wysyłanie danych diagnostycznych na konto magazynu i kiedy usługi Log Analytics odczytuje dane z konta magazynu.
>
>

Zobacz [usługa blob storage dla usług IIS i magazynu table storage zdarzeń](azure-storage-iis-table.md) Aby dowiedzieć się więcej na temat sposobu Log Analytics może zbierać dzienniki.

## <a name="connectors-for-azure-services"></a>Łączniki dla usług platformy Azure

Brak łącznika usługi Application Insights, która zezwala na dane zebrane przez usługę Application Insights do wysłania do usługi Log Analytics.

Dowiedz się więcej o [łącznik usługi Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics"></a>Skrypty do zbierania i dane post do usługi Log Analytics

Dla usług platformy Azure, które nie mają bezpośredni sposób wysyłania dzienników i metryk do usługi Log Analytics skrypt usługi Azure Automation służy do zbierania dzienników i metryk. Skrypt następnie możesz wysłać dane do usługi Log Analytics przy użyciu [interfejs API modułu zbierającego dane](../../azure-monitor/platform/data-collector-api.md)

Galeria szablonów platformy Azure ma [przykłady użycia usługi Azure Automation](https://azure.microsoft.com/resources/templates/?term=OMS) do zbierania danych z usług i wysłanie go do usługi Log Analytics.

## <a name="next-steps"></a>Kolejne kroki

* [Magazyn obiektów blob na użytek usług IIS i magazynu table storage zdarzeń](azure-storage-iis-table.md) do odczytywania dzienników dla tej diagnostyki zapisu do usługi table storage lub dzienniki programu IIS zapisywane do magazynu obiektów blob usługi systemu Azure.
* [Włączanie rozwiązań](../../azure-monitor/insights/solutions.md) zapewniające wgląd w dane.
* [Używają zapytań wyszukiwania](../../azure-monitor/log-query/log-query-overview.md) do analizowania danych.
