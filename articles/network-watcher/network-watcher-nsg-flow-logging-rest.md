---
title: Zarządzanie dziennikami przepływu nsg — interfejs API REST platformy Azure
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak zarządzać dziennikami przepływu sieciowej grupy zabezpieczeń w usłudze Azure Network Watcher za pomocą interfejsu API REST
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
ms.openlocfilehash: 7cc47414dc985f6fc2fff3c57d809f307b142e30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840931"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Konfigurowanie dzienników przepływu sieciowej grupy zabezpieczeń przy użyciu interfejsu API REST

> [!div class="op_single_selector"]
> - [Portal Azure](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [INTERFEJS API ODPOCZYNKU](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowej grupy zabezpieczeń są funkcją obserwatora sieci, która umożliwia wyświetlanie informacji o ruchu IP przychodzących i wychodzących za pośrednictwem sieciowej grupy zabezpieczeń. Te dzienniki przepływu są zapisywane w formacie json i pokazują przepływy wychodzące i przychodzące na podstawie reguły, karty sieciowej, do których ma zastosowanie przepływ, 5-krotki informacje o przepływie (źródłowy/docelowy adres IP, port źródłowy/docelowy, protokół) i jeśli ruch był dozwolony lub odrzucony.

## <a name="before-you-begin"></a>Przed rozpoczęciem

ARMclient jest używany do wywoływania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na chocolatey w [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu przyjęto założenie, że postępuje już kroki w [Tworzenie obserwatora sieci,](network-watcher-create.md) aby utworzyć obserwatora sieci.

> [!Important]
> Dla interfejsu API REST obserwatora sieciowego wywołuje nazwę grupy zasobów w identyfikatorze żądania URI jest grupa zasobów, która zawiera Obserwatora sieci, a nie zasoby, które wykonują akcje diagnostyczne.

## <a name="scenario"></a>Scenariusz

Scenariusz omówiony w tym artykule pokazuje, jak włączyć, wyłączyć i dzienniki przepływu zapytań przy użyciu interfejsu API REST. Aby dowiedzieć się więcej o rejestrowaniu przepływu sieciowej grupy zabezpieczeń, odwiedź stronę [Rejestrowanie przepływu sieciowej grupy zabezpieczeń — omówienie](network-watcher-nsg-flow-logging-overview.md).

W tym scenariuszu:

* Włącz dzienniki przepływu (wersja 2)
* Wyłączanie dzienników przepływu
* Stan dzienników przepływu kwerendy

## <a name="log-in-with-armclient"></a>Zaloguj się za pomocą ARMClient

Zaloguj się do armclient przy użyciu poświadczeń platformy Azure.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Rejestrowanie dostawcy usługi Insights

Aby rejestrowanie przepływu działało pomyślnie, dostawca **microsoft.insights** musi być zarejestrowany. Jeśli nie masz pewności, czy dostawca **usługi Microsoft.Insights** jest zarejestrowany, uruchom następujący skrypt.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Włączanie dzienników przepływu sieciowej grupy zabezpieczeń

Polecenie włączania dzienników przepływu w wersji 2 jest pokazane w poniższym przykładzie. W przypadku wersji 1 zastąp pole "wersja" na "1":

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Odpowiedź zwrócona z poprzedniego przykładu jest następująca:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
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
```

## <a name="disable-network-security-group-flow-logs"></a>Wyłączanie dzienników przepływu sieciowej grupy zabezpieczeń

Użyj poniższego przykładu, aby wyłączyć dzienniki przepływu. Wywołanie jest taka sama jak włączanie dzienników przepływu, z wyjątkiem **false** jest ustawiona dla włączonej właściwości.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Odpowiedź zwrócona z poprzedniego przykładu jest następująca:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
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
```

## <a name="query-flow-logs"></a>Dzienniki przepływu kwerendy

Następujące wywołanie REST pyta o stan dzienników przepływu w sieciowej grupie zabezpieczeń.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

Poniżej przedstawiono przykład zwracanej odpowiedzi:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
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
```

## <a name="download-a-flow-log"></a>Pobieranie dziennika przepływu

Lokalizacja przechowywania dziennika przepływu jest zdefiniowana podczas tworzenia. Wygodnym narzędziem dostępu do tych dzienników przepływu zapisanych na koncie magazynu jest Microsoft Azure Storage Explorer, który można pobrać tutaj:https://storageexplorer.com/

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [wizualizować dzienniki przepływu sieciowych sieci płciowych za pomocą usługi PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizować dzienniki przepływu nsg za pomocą narzędzi open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
