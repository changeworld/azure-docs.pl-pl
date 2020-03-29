---
title: Zarządzanie dziennikami przepływu nsg — narzędzie interfejsu wiersza polecenia platformy Azure
titleSuffix: Azure Network Watcher
description: Na tej stronie dowiesz się, jak zarządzać dziennikami przepływu grupy zabezpieczeń sieciowych w usłudze Azure Network Watcher za pomocą interfejsu wiersza polecenia platformy Azure
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
ms.openlocfilehash: 285d19dbd0e7b8a94eada66f837d33b787006f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840965"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń za pomocą interfejsu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> - [Portal Azure](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [INTERFEJS API ODPOCZYNKU](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowej grupy zabezpieczeń są funkcją obserwatora sieci, która umożliwia wyświetlanie informacji o ruchu IP przychodzących i wychodzących za pośrednictwem sieciowej grupy zabezpieczeń. Te dzienniki przepływu są zapisywane w formacie json i pokazują przepływy wychodzące i przychodzące na podstawie reguły, karty sieciowej, do których ma zastosowanie przepływ, 5-krotki informacje o przepływie (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) i jeśli ruch był dozwolony lub odrzucony.

Aby wykonać kroki opisane w tym artykule, należy [zainstalować interfejs wiersza polecenia platformy Azure dla komputerów Mac, Linux i Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Rejestrowanie dostawcy usługi Insights

Aby rejestrowanie przepływu działało pomyślnie, dostawca **microsoft.insights** musi być zarejestrowany. Jeśli nie masz pewności, czy dostawca **usługi Microsoft.Insights** jest zarejestrowany, uruchom następujący skrypt.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Włączanie dzienników przepływu sieciowej grupy zabezpieczeń

Polecenie włączania dzienników przepływu jest wyświetlane w poniższym przykładzie:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

Określone konto magazynu nie może mieć skonfigurowanych dla niego reguł sieciowych, które ograniczają dostęp do sieci tylko do usług firmy Microsoft lub określonych sieci wirtualnych. Konto magazynu może być w tej samej lub innej subskrypcji platformy Azure niż nsg, dla którego można włączyć dziennik przepływu. Jeśli używasz różnych subskrypcji, oba muszą być skojarzone z tą samą dzierżawą usługi Azure Active Directory. Konto używane dla każdej subskrypcji musi mieć [niezbędne uprawnienia](required-rbac-permissions.md). 

Jeśli konto magazynu znajduje się w innej grupie zasobów lub subskrypcji niż sieciowa grupa zabezpieczeń, określ pełny identyfikator konta magazynu, a nie jego nazwę. Na przykład, jeśli konto magazynu znajduje się w grupie zasobów o nazwie *RG-Storage*, zamiast *określać storageAccountName* w poprzednim poleceniu, należy określić */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccounts/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Wyłączanie dzienników przepływu sieciowej grupy zabezpieczeń

Aby wyłączyć dzienniki przepływu, użyj następującego przykładu:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Pobieranie dziennika przepływu

Lokalizacja przechowywania dziennika przepływu jest zdefiniowana podczas tworzenia. Wygodnym narzędziem dostępu do tych dzienników przepływu zapisanych na koncie magazynu jest Microsoft Azure Storage Explorer, który można pobrać tutaj:https://storageexplorer.com/

Jeśli określono konto magazynu, pliki dziennika przepływu są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowych sieci płciowych za pomocą usługi PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizować dzienniki przepływu nsg za pomocą narzędzi open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
