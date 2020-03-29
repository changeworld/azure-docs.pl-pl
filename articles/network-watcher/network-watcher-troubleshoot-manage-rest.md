---
title: Rozwiązywanie problemów z bramą sieci wirtualnej i połączeniami — interfejs API rest platformy Azure
titleSuffix: Azure Network Watcher
description: Na tej stronie wyjaśniono, jak rozwiązywać problemy z bramami sieci wirtualnej i połączeniami za pomocą usługi Azure Network Watcher przy użyciu restu
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: ab9f7fd95d7081b66e05dfd3d6a5ef47eb3c4053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840676"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Rozwiązywanie problemów z bramą sieci wirtualnej i połączeniami przy użyciu usługi Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Powershell](network-watcher-troubleshoot-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-troubleshoot-manage-cli.md)
> - [INTERFEJS API ODPOCZYNKU](network-watcher-troubleshoot-manage-rest.md)

Kontrola sieci zapewnia wiele możliwości, ponieważ odnosi się do zrozumienia zasobów sieciowych na platformie Azure. Jedną z tych możliwości jest rozwiązywanie problemów z zasobami. Rozwiązywanie problemów z zasobami można wywołać za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Po wywołaniu, Network Watcher sprawdza kondycję bramy sieci wirtualnej lub połączenia i zwraca jego wyniki.

W tym artykule oprowadza cię przez różne zadania zarządzania, które są obecnie dostępne do rozwiązywania problemów z zasobami.

- [**Rozwiązywanie problemów z bramą sieci wirtualnej**](#troubleshoot-a-virtual-network-gateway)
- [**Rozwiązywanie problemów z połączeniem**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Przed rozpoczęciem

ARMclient jest używany do wywoływania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na chocolatey w [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu przyjęto założenie, że postępuje już kroki w [Tworzenie obserwatora sieci,](network-watcher-create.md) aby utworzyć obserwatora sieci.

Aby uzyskać listę obsługiwanych typów bram, odwiedź stronę [Obsługiwane typy bramy](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Omówienie

Rozwiązywanie problemów z obserwatorem sieci zapewnia możliwość rozwiązywania problemów, które pojawiają się z bram sieci wirtualnej i połączeń. Po nałożeniu żądania do rozwiązywania problemów z zasobami dzienniki są kwerendy i kontrolowane. Po zakończeniu kontroli wyniki są zwracane. Rozwiązywanie problemów z żądaniami interfejsu API są długotrwałe żądania, które mogą potrwać wiele minut, aby zwrócić wynik. Dzienniki są przechowywane w kontenerze na koncie magazynu.

## <a name="log-in-with-armclient"></a>Zaloguj się za pomocą ARMClient

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Rozwiązywanie problemów z bramą sieci wirtualnej


### <a name="post-the-troubleshoot-request"></a>OPUBLIKUJ żądanie rozwiązywania problemów

Poniższy przykład pyta o stan bramy sieci wirtualnej.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$vnetGatewayName = "ContosoVNETGateway"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @"
{
'TargetResourceId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/virtualNetworkGateways/${vnetGatewayName}',
'Properties': {
'StorageId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}',
'StoragePath': 'https://${storageAccountName}.blob.core.windows.net/${containerName}'
}
}
"@


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" $requestBody -verbose
```

Ponieważ ta operacja jest długotrwała, identyfikator URI do wykonywania zapytań o operację i identyfikator URI dla wyniku jest zwracany w nagłówku odpowiedzi, jak pokazano w następującej odpowiedzi:

**Ważne wartości**

* **Azure-AsyncOperation** — ta właściwość zawiera identyfikator URI do kwerendy async rozwiązywania problemów operacji
* **Lokalizacja** — ta właściwość zawiera identyfikator URI, w którym wyniki są po zakończeniu operacji

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Zapytanie o operację asynchronicznej w celu ukończenia

Użyj operacji URI do kwerendy dla postępu operacji, jak pokazano w poniższym przykładzie:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Gdy operacja jest w toku, odpowiedź pokazuje **InProgress,** jak pokazano w poniższym przykładzie:

```json
{
  "status": "InProgress"
}
```

Po zakończeniu operacji stan zmieni się na **Pomyślnie**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Pobieranie wyników

Po zwrócony stan jest **powodem**, wywołać GET Metoda na operationResult identyfikator URI, aby pobrać wyniki.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Poniższe odpowiedzi są przykłady typowej odpowiedzi zdegradowanej zwracane podczas wykonywania zapytań o wyniki rozwiązywania problemów z bramą. Zobacz [Opis wyników,](#understanding-the-results) aby uzyskać wyjaśnienie, co oznaczają właściwości w odpowiedzi.

```json
{
  "startTime": "2017-01-12T10:31:41.562646-08:00",
  "endTime": "2017-01-12T18:31:48.677Z",
  "code": "Degraded",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN Gateway"
        },
        {
          "actionText": "If your VPN gateway isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN gateway is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Rozwiązywanie problemów z połączeniami

Poniższy przykład pyta o stan połączenia.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$connectionName = "VNET2toVNET1Connection"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @{
"TargetResourceId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/connections/${connectionName}",
"Properties": {
"StorageId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}",
"StoragePath": "https://${storageAccountName}.blob.core.windows.net/${containerName}"
}

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 $requestBody"
```

> [!NOTE]
> Operacja rozwiązywania problemów nie może być uruchamiana równolegle na połączeniu i jego odpowiednich bramach. Operacja musi zostać ukończona przed uruchomieniem go na poprzednim zasobie.

Ponieważ jest to długotrwała transakcja, w nagłówku odpowiedzi identyfikator URI do wykonywania zapytań o operację i identyfikator URI dla wyniku jest zwracany, jak pokazano w następującej odpowiedzi:

**Ważne wartości**

* **Azure-AsyncOperation** — ta właściwość zawiera identyfikator URI do kwerendy async rozwiązywania problemów operacji
* **Lokalizacja** — ta właściwość zawiera identyfikator URI, w którym wyniki są po zakończeniu operacji

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Zapytanie o operację asynchronicznej w celu ukończenia

Użyj operacji URI do kwerendy dla postępu operacji, jak pokazano w poniższym przykładzie:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Gdy operacja jest w toku, odpowiedź pokazuje **InProgress,** jak pokazano w poniższym przykładzie:

```json
{
  "status": "InProgress"
}
```

Po zakończeniu operacji stan zmieni się na **Zakończony .**

```json
{
  "status": "Succeeded"
}
```

Poniższe odpowiedzi są przykłady typowej odpowiedzi zwracanej podczas wykonywania zapytań o wyniki rozwiązywania problemów z połączeniem.

### <a name="retrieve-the-results"></a>Pobieranie wyników

Po zwrócony stan jest **powodem**, wywołać GET Metoda na operationResult identyfikator URI, aby pobrać wyniki.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Poniższe odpowiedzi są przykłady typowej odpowiedzi zwracanej podczas wykonywania zapytań o wyniki rozwiązywania problemów z połączeniem.

```json
{
  "startTime": "2017-01-12T14:09:19.1215346-08:00",
  "endTime": "2017-01-12T22:09:23.747Z",
  "code": "UnHealthy",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This 
is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN gateway"
        },
        {
          "actionText": "If your VPN Connection isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN Connection is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Zrozumienie wyników

Tekst akcji zawiera ogólne wskazówki dotyczące sposobu rozwiązania problemu. Jeśli można podjąć działanie w przypadku problemu, łącze jest dostarczane z dodatkowymi wskazówkami. W przypadku, gdy nie ma żadnych dodatkowych wskazówek, odpowiedź zawiera adres URL, aby otworzyć przypadek pomocy technicznej.  Aby uzyskać więcej informacji na temat właściwości odpowiedzi i co jest zawarte, odwiedź [omówienie rozwiązywania problemów z obserwatorem sieci](network-watcher-troubleshoot-overview.md)

Instrukcje dotyczące pobierania plików z kont usługi Azure Storage można znaleźć w [obszarze Wprowadzenie do usługi Azure Blob Storage przy użyciu programu .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Innym narzędziem, które może być używane, jest Eksplorator magazynu. Więcej informacji na temat Eksploratora magazynu można znaleźć tutaj pod następującym linkiem: [Eksplorator magazynu](https://storageexplorer.com/)

## <a name="next-steps"></a>Następne kroki

Jeśli ustawienia zostały zmienione, które zatrzymują łączność sieci VPN, zobacz [Zarządzanie grupami zabezpieczeń sieci,](../virtual-network/manage-network-security-group.md) aby wyśledzić grupę zabezpieczeń sieci i reguły zabezpieczeń, które mogą być kwestionowane.
