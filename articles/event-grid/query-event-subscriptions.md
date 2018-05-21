---
title: Kwerenda subskrypcji Azure zdarzeń siatki
description: Opisuje sposób listy subskrypcji Azure zdarzeń siatki.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: tomfitz
ms.openlocfilehash: 2b46cde4a352e647ee97669f116a6c1926879fa0
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="query-event-grid-subscriptions"></a>Kwerenda subskrypcji zdarzeń siatki 

W tym artykule opisano sposób wyświetlania subskrypcji zdarzeń siatki w Twojej subskrypcji platformy Azure. Podczas wykonywania zapytania istniejących subskrypcji zdarzeń siatki, ważne jest zrozumienie różnych typów subskrypcji. Musisz podać innych parametrów, na podstawie typu subskrypcji, który chcesz pobrać.

## <a name="resource-groups-and-azure-subscriptions"></a>Grupy zasobów i subskrypcje platformy Azure

Subskrypcje platformy Azure i grup zasobów nie są zasobów platformy Azure. W związku z tym subskrypcji siatki zdarzeń z grupami zasobów lub subskrypcji platformy Azure ma takie same właściwości jako zdarzenie subskrypcje siatki zasobów platformy Azure. Subskrypcje siatki zdarzeń z grupami zasobów lub subskrypcji platformy Azure są traktowane jako globalnego.

Aby uzyskać subskrypcji siatki zdarzeń dla subskrypcji platformy Azure i grup zasobów, nie trzeba podać parametry. Upewnij się, że wybrano subskrypcji platformy Azure, który chcesz zbadać. Poniższe przykłady nie pobieraj subskrypcji siatki zdarzeń tematy niestandardowej lub zasobów platformy Azure.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Set-AzureRmContext -Subscription "My Azure Subscription"
Get-AzureRmEventGridSubscription
```

Aby uzyskać subskrypcji siatki zdarzeń dla subskrypcji platformy Azure, należy podać typu tematu **Microsoft.Resources.Subscriptions**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions"
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Aby uzyskać subskrypcji zdarzeń siatki dla wszystkich grup zasobów w ramach subskrypcji platformy Azure, należy podać typu tematu **Microsoft.Resources.ResourceGroups**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups"
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Aby uzyskać subskrypcji zdarzeń siatki dla określonej grupy zasobów, należy podać nazwę grupy zasobów jako parametr.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Niestandardowe tematów i zasobów platformy Azure

Tematy niestandardowych zdarzeń siatki są zasobów platformy Azure. W związku z tym zapytania subskrypcji siatki zdarzeń niestandardowych tematów i innych zasobów, takich jak konta magazynu obiektów Blob, w taki sam sposób. Aby uzyskać subskrypcji siatki zdarzeń niestandardowych tematów, należy podać parametry, które identyfikacji zasobu lub określić lokalizację zasobu. Nie jest możliwe szeroko zapytania siatki subskrypcji zdarzeń dla zasobów w Twojej subskrypcji platformy Azure.

Można uzyskać subskrypcji siatki zdarzeń niestandardowych tematów i innych zasobów w miejscu, należy podać nazwę lokalizacji.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -Location westus2
```

Aby uzyskać subskrypcji do tematów niestandardowych dla lokalizacji, należy podać lokalizację i Typ tematu **Microsoft.EventGrid.Topics**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Aby uzyskać subskrypcji do kont magazynu dla lokalizacji, należy podać lokalizację i Typ tematu **Microsoft.Storage.StorageAccounts**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Aby uzyskać subskrypcji zdarzeń siatki dla niestandardowego tematu, podaj nazwę niestandardowego tematu oraz nazwę jego grupa zasobów.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzureRmEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Aby uzyskać subskrypcji siatki zdarzeń dla określonego zasobu, podaj identyfikator zasobu.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
$resourceid = (Get-AzureRmResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzureRmEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Kolejne kroki

* Informacje dotyczące dostarczania zdarzeń i ponownych prób [dostarczanie komunikatów zdarzeń siatki i ponów próbę](delivery-and-retry.md).
* Aby obejrzeć wprowadzenie do siatki zdarzeń, zobacz [o siatki zdarzeń](overview.md).
* Aby szybko rozpocząć korzystanie z siatki zdarzeń, zobacz [tworzenie i tras niestandardowych zdarzeń siatki zdarzeń Azure](custom-event-quickstart.md).
