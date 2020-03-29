---
title: Zarządzanie dziennikami przepływu sieciowej — Azure PowerShell
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak zarządzać dziennikami przepływu grupy zabezpieczeń sieciowych w usłudze Azure Network Watcher za pomocą programu PowerShell
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 9612afdb63c6988c0027f003caeacd456b5e50e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840948"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń za pomocą programu PowerShell

> [!div class="op_single_selector"]
> - [Portal Azure](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [INTERFEJS API ODPOCZYNKU](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowej grupy zabezpieczeń są funkcją obserwatora sieci, która umożliwia wyświetlanie informacji o ruchu IP przychodzących i wychodzących za pośrednictwem sieciowej grupy zabezpieczeń. Te dzienniki przepływu są zapisywane w formacie json i pokazują przepływy wychodzące i przychodzące na podstawie reguły, karty sieciowej, do których ma zastosowanie przepływ, 5-krotki informacje o przepływie (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) i jeśli ruch był dozwolony lub odrzucony.

## <a name="register-insights-provider"></a>Rejestrowanie dostawcy usługi Insights

Aby rejestrowanie przepływu działało pomyślnie, dostawca **microsoft.insights** musi być zarejestrowany. Jeśli nie masz pewności, czy dostawca **usługi Microsoft.Insights** jest zarejestrowany, uruchom następujący skrypt.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Włączanie dzienników przepływu grupy zabezpieczeń sieciowych i analizy ruchu

Polecenie włączania dzienników przepływu jest wyświetlane w poniższym przykładzie:

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

Określone konto magazynu nie może mieć skonfigurowanych dla niego reguł sieciowych, które ograniczają dostęp do sieci tylko do usług firmy Microsoft lub określonych sieci wirtualnych. Konto magazynu może być w tej samej lub innej subskrypcji platformy Azure niż nsg, dla którego można włączyć dziennik przepływu. Jeśli używasz różnych subskrypcji, oba muszą być skojarzone z tą samą dzierżawą usługi Azure Active Directory. Konto używane dla każdej subskrypcji musi mieć [niezbędne uprawnienia](required-rbac-permissions.md).

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Wyłączanie dzienników przepływu analizy ruchu i sieciowej grupy zabezpieczeń

Użyj następującego przykładu, aby wyłączyć analizy ruchu i dzienniki przepływu:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Pobieranie dziennika przepływu

Lokalizacja przechowywania dziennika przepływu jest zdefiniowana podczas tworzenia. Wygodnym narzędziem dostępu do tych dzienników przepływu zapisanych na koncie magazynu jest Microsoft Azure Storage Explorer, który można pobrać tutaj:https://storageexplorer.com/

Jeśli określono konto magazynu, pliki dziennika przepływu są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Aby uzyskać informacje na temat struktury dziennika, odwiedź [przegląd dziennika przepływu sieciowej grupy zabezpieczeń](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowych sieci płciowych za pomocą usługi PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizować dzienniki przepływu nsg za pomocą narzędzi open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
