---
title: Dzienników przepływu sieciowej grupy zabezpieczeń zarządzania przy użyciu usługi Azure Network Watcher — interfejs API REST | Dokumentacja firmy Microsoft
description: Ta strona wyjaśnia, jak zarządzać dzienniki przepływu sieciowych grup zabezpieczeń w usłudze Azure Network Watcher przy użyciu interfejsu API REST
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2ab25379-0fd3-4bfe-9d82-425dfc7ad6bb
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: ab4b283449ec6c0174f380b0231dd2e78dea419d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64688041"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Skonfigurowanie sieciowej grupy zabezpieczeń dzienników przepływów przy użyciu interfejsu API REST

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [Program PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-nsg-flow-logging-cli.md)
> - [Interfejs API REST](network-watcher-nsg-flow-logging-rest.md)

Dzienniki przepływu sieciowej grupy zabezpieczeń są funkcją usługi Network Watcher, który służy do wyświetlania informacji na temat przychodzący i wychodzący ruch IP sieciowej grupy zabezpieczeń. Te dzienniki przepływu są zapisywane w formacie json i Pokaż przepływy wychodzące i przychodzące na podstawie reguły w poszczególnych kart Sieciowych, przepływ, który ma zastosowanie do 5-elementowych informacji o przepływie (źródłowy/docelowy adres IP, Port źródłowy i docelowy, Protocol), a jeśli zezwolenie lub odrzucenie ruchu sieciowego.

## <a name="before-you-begin"></a>Przed rozpoczęciem

ARMclient jest używane do wywołania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na chocolatey na [ARMClient na narzędzia Chocolatey](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu przyjęto założenie, zostały już wykonane czynności opisane w [utworzyć usługę Network Watcher](network-watcher-create.md) utworzyć usługę Network Watcher.

> [!Important]
> Dla wywołań interfejsu API REST obserwatora sieci, że nazwa grupy zasobów w identyfikatorze URI żądania jest grupa zasobów, który zawiera usługi Network Watcher zasoby nie są wykonywane czynności diagnostyczne na.

## <a name="scenario"></a>Scenariusz

Scenariusz, w tym artykule pokazano, jak włączać, wyłączać i dzienników przepływów przy użyciu interfejsu API REST zapytań. Aby dowiedzieć się więcej na temat loggings przepływu sieciowej grupy zabezpieczeń, odwiedź stronę [rejestrowanie przepływu sieciowej grupy zabezpieczeń — omówienie](network-watcher-nsg-flow-logging-overview.md).

W tym scenariuszu wykonasz następujące czynności:

* Włączanie dzienników przepływu (wersja 2)
* Wyłącz dzienników przepływów
* Stan dzienników przepływu zapytania

## <a name="log-in-with-armclient"></a>Zaloguj się przy użyciu ARMClient

Zaloguj się do armclient przy użyciu swoich poświadczeń platformy Azure.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Rejestrowanie dostawcy usługi Insights

Aby rejestrowanie działało poprawnie, przepływ **Microsoft.Insights** dostawca musi być zarejestrowana. Jeśli nie masz pewności Jeśli **Microsoft.Insights** dostawca jest zarejestrowany, uruchom następujący skrypt.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Dzienniki przepływów Włączanie sieciowych grup zabezpieczeń

Na poniższym przykładzie przedstawiono polecenie, aby włączyć dzienniki przepływu w wersji 2. Zastąp pole "version" "1" dla wersji 1:

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

Odpowiedź zwrócona z poprzedniego przykładu, jest następujący:

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

## <a name="disable-network-security-group-flow-logs"></a>Dzienniki przepływów wyłączyć grupy zabezpieczeń sieci

Skorzystaj z następującego przykładu, aby wyłączyć dzienników przepływów. Wywołanie jest taka sama jak Włączanie dzienników przepływu, z wyjątkiem **false** jest ustawiona dla właściwości włączone.

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

Odpowiedź zwrócona z poprzedniego przykładu, jest następujący:

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

## <a name="query-flow-logs"></a>Dzienniki przepływu zapytań

Następujące zapytania wywołania REST logowania stan przepływu sieciowej grupy zabezpieczeń.

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

Oto przykład odpowiedzi zwróconej:

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

## <a name="download-a-flow-log"></a>Pobierz dziennik przepływu

Lokalizacja magazynu dziennika przepływu jest zdefiniowany podczas tworzenia. Wygodne narzędzie dostępu do tych dzienników przepływu zapisane na koncie magazynu jest Microsoft Azure Storage Explorer, który można pobrać tutaj:  https://storageexplorer.com/

Jeśli określono konto magazynu, pliki przechwytywania pakietów są zapisywane na koncie magazynu w następującej lokalizacji:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [wizualizowanie dzienników przepływów sieciowych grup zabezpieczeń przy użyciu usługi Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Dowiedz się, jak [wizualizowanie dzienników przepływów sieciowych grup zabezpieczeń przy użyciu narzędzi typu open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
