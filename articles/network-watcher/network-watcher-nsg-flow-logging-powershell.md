---
title: Zarządzanie dziennikami przepływu grupy zabezpieczeń w sieci za pomocą usługi Azure Network Watcher — PowerShell | Dokumentacja firmy Microsoft
description: Ta strona wyjaśnia, jak Zarządzanie dziennikami przepływu grupy zabezpieczeń w sieci w usłudze Azure Network Watcher przy użyciu programu PowerShell
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 752370564c52513d59e99b18d5343b0575900463
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2018
ms.locfileid: "51819359"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Konfigurowanie dzienników przepływu grupy zabezpieczeń w sieci przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowej grupy zabezpieczeń są funkcją usługi Network Watcher, który służy do wyświetlania informacji na temat przychodzący i wychodzący ruch IP sieciowej grupy zabezpieczeń. Te dzienniki przepływu są zapisywane w formacie json i Pokaż przepływy wychodzące i przychodzące na podstawie reguły w poszczególnych kart Sieciowych, przepływ, który ma zastosowanie do 5-elementowych informacji o przepływie (źródłowy/docelowy adres IP, Port źródłowy i docelowy, Protocol), a jeśli zezwolenie lub odrzucenie ruchu sieciowego.

> [!NOTE] 
> Dzienniki usługi Flow w wersji 2 są tylko są one dostępne w regionie zachodnie stany centralnej USA. Konfiguracja jest dostępna za pośrednictwem witryny Azure Portal i interfejsu API REST. Włączanie w wersji 2 spowoduje dzienniki w nieobsługiwany region dzienników w wersji 1, zwrócone do swojego konta magazynu.

## <a name="register-insights-provider"></a>Rejestrowanie dostawcy usługi Insights

Aby rejestrowanie działało poprawnie, przepływ **Microsoft.Insights** dostawca musi być zarejestrowana. Jeśli nie masz pewności Jeśli **Microsoft.Insights** dostawca jest zarejestrowany, uruchom następujący skrypt.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Dzienniki przepływów Włączanie sieciowych grup zabezpieczeń

Na poniższym przykładzie przedstawiono polecenie, aby włączyć dzienniki przepływu:

```powershell
$NW = Get-AzurermNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzureRmNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true
```

Konto magazynu, które określisz, że nie ma skonfigurowanych reguł sieci, które ograniczają dostęp do sieci tylko do usług firmy Microsoft lub określonych sieci wirtualnej. Konto magazynu może być w tym samym lub różnych subskrypcji platformy Azure, niż sieciowej grupy zabezpieczeń, pozwalających na korzystanie z usługi flow w dzienniku. Jeśli używasz różnych subskrypcji, one muszą być skojarzone z tą samą dzierżawą usługi Azure Active Directory. Konto używane dla każdej subskrypcji musi mieć [niezbędne uprawnienia](required-rbac-permissions.md).

## <a name="disable-network-security-group-flow-logs"></a>Dzienniki przepływów wyłączyć grupy zabezpieczeń sieci

Skorzystaj z następującego przykładu, aby wyłączyć dzienników przepływu:

```powershell
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Pobierz dziennik przepływu

Lokalizacja magazynu dziennika przepływu jest zdefiniowany podczas tworzenia. Wygodne narzędzie dostępu do tych dzienników przepływu zapisane na koncie magazynu jest Microsoft Azure Storage Explorer, który można pobrać tutaj:  http://storageexplorer.com/

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Aby uzyskać informacje o strukturze dziennika można znaleźć [dziennika przepływu grupy zabezpieczeń w sieci — omówienie](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wizualizowanie dzienników przepływów sieciowych grup zabezpieczeń przy użyciu usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizowanie dzienników przepływów sieciowych grup zabezpieczeń przy użyciu narzędzi typu open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
