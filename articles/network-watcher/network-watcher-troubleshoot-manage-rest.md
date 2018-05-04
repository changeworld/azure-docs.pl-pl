---
title: Rozwiązywanie problemów z Brama sieci wirtualnej i połączeń za pomocą Monitora sieci Azure - REST | Dokumentacja firmy Microsoft
description: Ta strona wyjaśniono, jak rozwiązywanie problemów z bramy sieci wirtualnej i połączenia z obserwatora sieciowego Azure za pomocą usługi REST
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e4d5f195-b839-4394-94ef-a04192766e55
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: b2c6d75c1e52b4e7265380cdf8fd3462a3d93e33
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Rozwiązywanie problemów z bramy sieci wirtualnej i połączeń za pomocą Monitora sieci platformy Azure

> [!div class="op_single_selector"]
> - [Portal](network-watcher-troubleshoot-manage-portal.md)
> - [Program PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [Interfejs API REST](network-watcher-troubleshoot-manage-rest.md)

Obserwatora sieciowego zawiera wiele funkcji w odniesieniu do zrozumienia zasobów sieciowych na platformie Azure. Jeden z tych funkcji jest zasobem rozwiązywania problemów. Rozwiązywanie problemów z zasobów można wywołać za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Po wywołaniu, obserwatora sieciowego sprawdza kondycję Brama sieci wirtualnej lub połączenie i zwraca jego wyniki.

Ten artykuł przedstawia za pomocą zadań zarządzania różnych, które są obecnie dostępne dla zasobu Rozwiązywanie problemów.

- [**Rozwiązywanie problemów z bramy sieci wirtualnej**](#troubleshoot-a-virtual-network-gateway)
- [**Rozwiązywanie problemów z połączeniem**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Przed rozpoczęciem

ARMclient służy do wywołania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na chocolatey w [ARMClient na Chocolatey](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego.

Lista odwiedziny typy obsługiwanych bramy [bramy obsługiwane typy](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Przegląd

Rozwiązywanie problemów z siecią obserwatora umożliwia rozwiązywanie problemów, które wynikają z połączeniami i bram sieci wirtualnej. Po wysłaniu żądania do zasobu, rozwiązywania problemów, dzienniki wykonywania zapytania dotyczącego i inspekcji. Po zakończeniu kontroli wyniki są zwracane. Rozwiązywanie problemów z interfejsu API żądania są długie uruchomionych żądań, które może potrwać kilka minut do zwrócenia wyniku. Dzienniki są przechowywane w kontenerze na koncie magazynu.

## <a name="log-in-with-armclient"></a>Zaloguj się za pomocą ARMClient

```PowerShell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Rozwiązywanie problemów z bramy sieci wirtualnej


### <a name="post-the-troubleshoot-request"></a>Rozwiązywanie problemów dotyczących żądania POST

Poniższy przykład zapytanie o stan bramy sieci wirtualnej.

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


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" -verbose
```

Ponieważ ta operacja jest długa uruchomiona, identyfikator URI dla zapytań operacji i identyfikator URI dla wynik zostanie zwrócony w nagłówku odpowiedzi, jak pokazano w poniższych odpowiedzi:

**Ważne wartości**

* **Azure AsyncOperation** — ta właściwość zawiera identyfikator URI do zapytania Async Rozwiązywanie problemów z operacji
* **Lokalizacja** — ta właściwość zawiera identyfikator URI, których wyniki są po zakończeniu operacji

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

### <a name="query-the-async-operation-for-completion"></a>Zapytanie na zakończenie operacji asynchronicznej

Użyj operacje na identyfikator URI zapytania dla postępu operacji, jak pokazano w poniższym przykładzie:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Operacja jest w toku, odpowiedzi zawiera **w toku** jak pokazano w poniższym przykładzie:

```json
{
  "status": "InProgress"
}
```

Po zakończeniu operacji stan zmienia się na **zakończyło się pomyślnie**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Pobieranie wyników

Gdy zwrócony stan **zakończyło się pomyślnie**, wywołanie metody GET na klasy operationResult identyfikator URI, aby pobrać wyniki.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Przykłady typowych odpowiedzi obniżeniem zwrócony podczas wykonywania zapytania wyniki Rozwiązywanie problemów z bramą są następujące odpowiedzi. Zobacz [opis wyników](#understanding-the-results) uzyskać wyjaśnienie na znaczenie właściwości w odpowiedzi.

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
          "actionUri": "http://azure.microsoft.com/support",
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
          "actionUri": "http://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Rozwiązywanie problemów z połączeniami

Poniższy przykład zapytanie o stan połączenia.

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
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 "
```

> [!NOTE]
> Rozwiązywanie problemów dotyczących operacji nie można uruchomić równolegle na połączenie i jego odpowiednich bram. Przed uruchomieniem go na poprzedni zasób musi ukończyć operacji.

Ponieważ jest to długo działającą transakcję, nagłówka odpowiedzi identyfikator URI dla zapytań operacji oraz identyfikatora URI dla wyniku jest zwracany, jak pokazano w odpowiedzi na następujące:

**Ważne wartości**

* **Azure AsyncOperation** — ta właściwość zawiera identyfikator URI do zapytania Async Rozwiązywanie problemów z operacji
* **Lokalizacja** — ta właściwość zawiera identyfikator URI, których wyniki są po zakończeniu operacji

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

### <a name="query-the-async-operation-for-completion"></a>Zapytanie na zakończenie operacji asynchronicznej

Użyj operacje na identyfikator URI zapytania dla postępu operacji, jak pokazano w poniższym przykładzie:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Operacja jest w toku, odpowiedzi zawiera **w toku** jak pokazano w poniższym przykładzie:

```json
{
  "status": "InProgress"
}
```

Po zakończeniu operacji stan zmienia się na **zakończyło się pomyślnie**.

```json
{
  "status": "Succeeded"
}
```

Przykłady typowych odpowiedzi zwracany podczas wykonywania zapytania wyniki Rozwiązywanie problemów z połączeniem są następujące odpowiedzi.

### <a name="retrieve-the-results"></a>Pobieranie wyników

Gdy zwrócony stan **zakończyło się pomyślnie**, wywołanie metody GET na klasy operationResult identyfikator URI, aby pobrać wyniki.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Przykłady typowych odpowiedzi zwracany podczas wykonywania zapytania wyniki Rozwiązywanie problemów z połączeniem są następujące odpowiedzi.

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
          "actionUri": "http://azure.microsoft.com/support",
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
          "actionUri": "http://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Opis wyników

Tekst akcji zawiera ogólne wskazówki na temat sposobu rozwiązania problemu. Jeśli dla problemu można podjąć akcję, link jest dostarczany z dodatkowych wskazówek. W przypadku których nie ma dodatkowych wskazówek, odpowiedź zawiera adres url, aby otworzyć sprawę pomocy technicznej.  Aby uzyskać więcej informacji na temat właściwości odpowiedzi i dostępnych odwiedź [Rozwiązywanie problemów z obserwatora sieciowego — omówienie](network-watcher-troubleshoot-overview.md)

Aby uzyskać instrukcje dotyczące pobierania plików z kontami magazynu azure, zapoznaj się [Rozpoczynanie pracy z magazynem obiektów Blob platformy Azure przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Kolejnym narzędziem, który może służyć jest Eksploratora usługi Storage. Więcej informacji na temat Eksploratora usługi Storage można znaleźć tutaj przy użyciu następującego łącza: [Eksploratora usługi Storage](http://storageexplorer.com/)

## <a name="next-steps"></a>Kolejne kroki

Jeśli ustawienia zostały zmienione które zatrzymania połączenia sieci VPN, zobacz [Zarządzaj grupami zabezpieczeń sieci](../virtual-network/manage-network-security-group.md) śledzić reguły zabezpieczeń sieciowych grupy i zabezpieczeń, które mogą być zagrożona.
