---
title: Zarządzanie dziennikami przepływu grupy zabezpieczeń w sieci za pomocą usługi Azure Network Watcher — interfejs wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Ta strona wyjaśnia, jak Zarządzanie dziennikami przepływu grupy zabezpieczeń w sieci w usłudze Azure Network Watcher przy użyciu wiersza polecenia platformy Azure
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 7f964fd78845e663c1ebcde7e79bfb7295c72f31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730572"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurowanie dzienników przepływu grupy zabezpieczeń w sieci przy użyciu wiersza polecenia platformy Azure

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowej grupy zabezpieczeń są funkcją usługi Network Watcher, który służy do wyświetlania informacji na temat przychodzący i wychodzący ruch IP sieciowej grupy zabezpieczeń. Te dzienniki przepływu są zapisywane w formacie json i Pokaż przepływy wychodzące i przychodzące na podstawie reguły w poszczególnych kart Sieciowych, przepływ, który ma zastosowanie do 5-elementowych informacji o przepływie (źródłowy/docelowy adres IP, Port źródłowy i docelowy, Protocol), a jeśli zezwolenie lub odrzucenie ruchu sieciowego.

Aby wykonać kroki opisane w tym artykule, musisz [zainstalować interfejs wiersza polecenia platformy Azure dla systemów Mac, Linux i Windows (CLI)](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Rejestrowanie dostawcy usługi Insights

Aby rejestrowanie działało poprawnie, przepływ **Microsoft.Insights** dostawca musi być zarejestrowana. Jeśli nie masz pewności Jeśli **Microsoft.Insights** dostawca jest zarejestrowany, uruchom następujący skrypt.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Dzienniki przepływów Włączanie sieciowych grup zabezpieczeń

Na poniższym przykładzie przedstawiono polecenie, aby włączyć dzienniki przepływu:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

Konto magazynu, które określisz, że nie ma skonfigurowanych reguł sieci, które ograniczają dostęp do sieci tylko do usług firmy Microsoft lub określonych sieci wirtualnej. Konto magazynu może być w tym samym lub różnych subskrypcji platformy Azure, niż sieciowej grupy zabezpieczeń, pozwalających na korzystanie z usługi flow w dzienniku. Jeśli używasz różnych subskrypcji, one muszą być skojarzone z tą samą dzierżawą usługi Azure Active Directory. Konto używane dla każdej subskrypcji musi mieć [niezbędne uprawnienia](required-rbac-permissions.md). 

Jeśli konto magazynu jest w innej grupie zasobów lub subskrypcji, niż sieciowej grupy zabezpieczeń, należy określić pełny identyfikator konta magazynu, a nie jego nazwę. Na przykład, jeśli konto magazynu jest w grupie zasobów o nazwie *magazynu RG*, zamiast określać *storageAccountName* w poprzednim poleceniu, należy określić   */subscriptions / { SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName*.

## <a name="disable-network-security-group-flow-logs"></a>Dzienniki przepływów wyłączyć grupy zabezpieczeń sieci

Skorzystaj z następującego przykładu, aby wyłączyć dzienników przepływu:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Pobierz dziennik przepływu

Lokalizacja magazynu dziennika przepływu jest zdefiniowany podczas tworzenia. Wygodne narzędzie dostępu do tych dzienników przepływu zapisane na koncie magazynu jest Microsoft Azure Storage Explorer, który można pobrać tutaj:  https://storageexplorer.com/

Jeśli określono konto magazynu, przepływ pliki dziennika są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Aby uzyskać informacje o strukturze dziennika można znaleźć [dziennika przepływu grupy zabezpieczeń w sieci — omówienie](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wizualizowanie dzienników przepływów sieciowych grup zabezpieczeń przy użyciu usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizowanie dzienników przepływów sieciowych grup zabezpieczeń przy użyciu narzędzi typu open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
