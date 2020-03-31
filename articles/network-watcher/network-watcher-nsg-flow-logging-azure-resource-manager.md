---
title: Obserwator sieci — tworzenie dzienników przepływu sieciowej grupy sieciowej przy użyciu szablonu usługi Azure Resource Manager
description: Użyj szablonu usługi Azure Resource Manager i programu PowerShell, aby łatwo skonfigurować dzienniki przepływu sieciowych.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 35d185a625a81a259c366a45999769ecf76c6a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538162"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Konfigurowanie dzienników przepływu sieciowych z szablonu usługi Azure Resource Manager

> [!div class="op_single_selector"]
> - [Portal Azure](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [INTERFEJS API ODPOCZYNKU](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Usługa Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) to natywny i zaawansowany sposób zarządzania [infrastrukturą jako kodem](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)platformy Azure.

W tym artykule pokazano, jak programowo włączać [dzienniki przepływu sieciowej sieciowej](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) przy użyciu szablonu usługi Azure Resource Manager i programu Azure PowerShell. Zaczynamy od zapewnienia przeglądu właściwości obiektu dziennika przepływu sieciowej sieciowej, a następnie kilku przykładowych szablonów. Następnie wdrożyć szablon przy użyciu lokalnego wystąpienia programu PowerShell.


## <a name="nsg-flow-logs-object"></a>Obiekt dzienników przepływu nsg

Obiekt Dzienniki przepływu NSG ze wszystkimi parametrami przedstawiono poniżej.
Aby uzyskać pełny przegląd właściwości, można przeczytać [odwołanie do szablonu dzienników przepływu sieciowej](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Aby utworzyć zasób Microsoft.Network/networkWatchers/flowLogs, dodaj powyższy JSON do sekcji zasobów szablonu.


## <a name="creating-your-template"></a>Tworzenie szablonu

Jeśli używasz szablonów usługi Azure Resource Manager po raz pierwszy, możesz dowiedzieć się więcej o nich, korzystając z poniższych łączy.

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Samouczek: Tworzenie i wdrażanie pierwszego szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Poniżej znajdują się dwa przykłady kompletnych szablonów do konfigurowania dzienników przepływu nsg.

**Przykład 1**: Najprostsza wersja powyższego z minimalnymi parametrami przekazanymi. Poniższy szablon umożliwia dzienniki przepływu nsg na docelowej grupy nsg i przechowuje je na danym koncie magazynu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * Nazwa zasobu ma format "Zasób nadrzędny>/podrzędny zasób". W tym miejscu zasób nadrzędny jest regionalnym wystąpieniem<RegionName>Obserwatora sieci (Format: NetworkWatcher_ . Przykład: NetworkWatcher_centraluseuap)
> * targetResourceId jest identyfikatorem zasobu docelowej grupy ndg
> * identyfikator magazynu jest identyfikatorem zasobu docelowego konta magazynu

**Przykład 2:** Następujące szablony umożliwiające dzienniki przepływu nsg (wersja 2) z przechowywaniem przez 5 dni. Włączanie analizy ruchu z interwałem przetwarzania wynoszącym 10 minut.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {
        "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
                }
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 2            
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Wdrażanie szablonu usługi Azure Resource Manager

W tym samouczku przyjęto założenie, że masz istniejącą grupę zasobów i grupę ndg, na której można włączyć rejestrowanie przepływu.
Lokalne zapisywanie dowolnego z powyższych przykładowych szablonów można zapisać jako `azuredeploy.json`. Zaktualizuj wartości właściwości, tak aby wskazywały prawidłowe zasoby w ramach subskrypcji.

Aby wdrożyć szablon, uruchom następujące polecenie w programie PowerShell.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>Weryfikowanie wdrożenia

Istnieje kilka sposobów, aby sprawdzić, czy wdrożenie zakończyło się pomyślnie. Konsola programu PowerShell powinna być pouczona jako "Stan obsługi administracyjnej" jako "Powodzenie". Ponadto można odwiedzić [stronę portalu dzienników przepływu nsg,](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) aby potwierdzić zmiany. Jeśli wystąpiły problemy z wdrożeniem, zapoznaj się z [rozwiązywaniem typowych błędów wdrażania platformy Azure za pomocą usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wizualizować dane przepływu nsg za pomocą:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Narzędzia open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
