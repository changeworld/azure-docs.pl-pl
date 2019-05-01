---
title: Rozwiązywanie problemów z bramy sieci wirtualnej i połączeń przy użyciu usługi Azure Network Watcher — REST | Dokumentacja firmy Microsoft
description: Tej stronie wyjaśniamy, jak rozwiązywać problemy z bramy sieci wirtualnej i połączeń przy użyciu usługi Azure Network Watcher przy użyciu usługi REST
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: e4d5f195-b839-4394-94ef-a04192766e55
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: kumud
ms.openlocfilehash: 0f10b9b45f63485417685a0826c047725a264772
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64686116"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Rozwiązywanie problemów z bramą sieci wirtualnej i połączeń przy użyciu usługi Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Program PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Interfejs wiersza polecenia platformy Azure](network-watcher-troubleshoot-manage-cli.md)
> - [Interfejs API REST](network-watcher-troubleshoot-manage-rest.md)

Usługa Network Watcher udostępnia wiele możliwości, w odniesieniu do zrozumienia zasobów sieciowych na platformie Azure. Jedną z tych funkcji jest zasobem rozwiązywania problemów. Rozwiązywanie problemów z zasobu może być wywoływany za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Po wywołaniu usługi Network Watcher sprawdza kondycję bramy sieci wirtualnej lub połączenie i zwraca jej ustaleń.

Ten artykuł przeprowadzi Cię przez zadania zarządzania różnych, które są obecnie dostępne dla zasobów rozwiązywania problemów.

- [**Rozwiązywanie problemów z bramą sieci wirtualnej**](#troubleshoot-a-virtual-network-gateway)
- [**Rozwiązywanie problemów z połączeniem**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Przed rozpoczęciem

ARMclient jest używane do wywołania interfejsu API REST przy użyciu programu PowerShell. ARMClient znajduje się na chocolatey na [ARMClient na narzędzia Chocolatey](https://chocolatey.org/packages/ARMClient)

W tym scenariuszu przyjęto założenie, zostały już wykonane czynności opisane w [utworzyć usługę Network Watcher](network-watcher-create.md) utworzyć usługę Network Watcher.

Aby uzyskać listę typów obsługiwanych bramy, zobacz [typy bram obsługiwane](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Omówienie

Rozwiązywanie problemów z siecią obserwatora umożliwia rozwiązywanie problemów, które wynikają z bramy sieci wirtualnej i połączenia. Po wysłaniu żądania do zasobu, rozwiązywania problemów, dzienniki jest wykonywane zapytanie i inspekcji. Po ukończeniu inspekcji wyniki są zwracane. Żądania interfejsu API rozwiązywania problemów są długie uruchomione żądania, może to potrwać kilka minut, aby zwrócony wynik. Dzienniki są przechowywane w kontenerze na koncie magazynu.

## <a name="log-in-with-armclient"></a>Zaloguj się przy użyciu ARMClient

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Rozwiązywanie problemów z bramą sieci wirtualnej


### <a name="post-the-troubleshoot-request"></a>WPIS w żądaniu rozwiązywania problemów

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


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" $requestBody -verbose
```

Ponieważ ta operacja jest długa uruchomiona, identyfikator URI dla wysyłania zapytań do operacji i identyfikator URI dla wynik zostanie zwrócony w nagłówku odpowiedzi, jak pokazano w poniższym odpowiedzi:

**Ważne wartości**

* **Elementu Azure-AsyncOperation** — ta właściwość zawiera identyfikator URI zapytania Async Rozwiązywanie problemów z operacji
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

### <a name="query-the-async-operation-for-completion"></a>Zapytanie operacji asynchronicznych do ukończenia

Użyj operacji identyfikatora URI do wykonywania zapytań dla postępu operacji, jak pokazano w poniższym przykładzie:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Gdy operacja jest w toku, odpowiedź wskazuje **InProgress** jak pokazano w poniższym przykładzie:

```json
{
  "status": "InProgress"
}
```

Po zakończeniu operacji stan zmieni się na **Powodzenie**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Pobieranie wyników

Gdy zwracany stan to **Powodzenie**, wywołania metody UZYSKAĆ klasy operationResult identyfikatora URI do pobierania wyników.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Przykłady typowych odpowiedzi o obniżonym poziomie zwrócony podczas wykonywania zapytania o wyniki Rozwiązywanie problemów z bramą są następujące odpowiedzi. Zobacz [opis wyników](#understanding-the-results) uzyskać objaśnienia na znaczenie właściwości w odpowiedzi.

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

Poniższy przykład wykonuje kwerendę stanu połączenia.

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
> Nie można uruchomić operacji Rozwiązywanie problemów w sposób równoległy, połączenia i jego odpowiednich bram. Operację należy wykonać przed uruchomieniem go na poprzedni zasób.

Ponieważ jest długotrwałą transakcją, w nagłówku odpowiedzi identyfikator URI dla wysyłania zapytań do operacji i identyfikator URI dla wynik jest zwracany, jak pokazano w poniższym odpowiedzi:

**Ważne wartości**

* **Elementu Azure-AsyncOperation** — ta właściwość zawiera identyfikator URI zapytania Async Rozwiązywanie problemów z operacji
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

### <a name="query-the-async-operation-for-completion"></a>Zapytanie operacji asynchronicznych do ukończenia

Użyj operacji identyfikatora URI do wykonywania zapytań dla postępu operacji, jak pokazano w poniższym przykładzie:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Gdy operacja jest w toku, odpowiedź wskazuje **InProgress** jak pokazano w poniższym przykładzie:

```json
{
  "status": "InProgress"
}
```

Po zakończeniu operacji stan zmieni się na **Powodzenie**.

```json
{
  "status": "Succeeded"
}
```

Przykłady typowych odpowiedzi zwracany podczas wykonywania zapytania o wyniki Rozwiązywanie problemów z połączeniem są następujące odpowiedzi.

### <a name="retrieve-the-results"></a>Pobieranie wyników

Gdy zwracany stan to **Powodzenie**, wywołania metody UZYSKAĆ klasy operationResult identyfikatora URI do pobierania wyników.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Przykłady typowych odpowiedzi zwracany podczas wykonywania zapytania o wyniki Rozwiązywanie problemów z połączeniem są następujące odpowiedzi.

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

## <a name="understanding-the-results"></a>Opis wyników

Tekst akcji zawiera ogólne wskazówki na temat sposobu rozwiązania problemu. Jeśli dla problemu można wykonać akcji, łącze jest dostarczana z dodatkowych wskazówek. W przypadku, w przypadku, gdy nie ma żadnych dodatkowych wskazówek, odpowiedź zawiera adres url, aby otworzyć zgłoszenie do pomocy technicznej.  Aby uzyskać więcej informacji na temat właściwości odpowiedzi i co jest zawarte w odwiedzić [rozwiązywania problemów z Network Watcher — omówienie](network-watcher-troubleshoot-overview.md)

Aby uzyskać instrukcje dotyczące pobierania plików z konta usługi azure storage, zapoznaj się [wprowadzenie do usługi Azure Blob storage przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Inne narzędzie, które mogą być używane jest Eksploratora usługi Storage. Więcej informacji na temat Eksploratora usługi Storage można znaleźć tutaj z łącza: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Kolejne kroki

Jeśli zmieniono ustawienia tego połączenia sieci VPN stop, zobacz [Zarządzanie sieciowymi grupami zabezpieczeń](../virtual-network/manage-network-security-group.md) ułatwiają śledzenie reguły zabezpieczeń sieci grupy i zabezpieczeń, które mogą być w danym.
