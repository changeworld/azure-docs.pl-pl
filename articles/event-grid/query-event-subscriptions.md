---
title: Zapytanie o subskrypcje usługi Azure Event Grid
description: W tym artykule opisano sposób wystawiania subskrypcji usługi Event Grid w ramach subskrypcji platformy Azure. Podajesz różne parametry na podstawie typu subskrypcji.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2da4ba5946f16092c1d0918aec8dc3109b5fd4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721545"
---
# <a name="query-event-grid-subscriptions"></a>Subskrypcje siatki zdarzeń kwerendy 

W tym artykule opisano sposób listy subskrypcji usługi Event Grid w ramach subskrypcji platformy Azure. Podczas wykonywania zapytań o istniejące subskrypcje usługi Event Grid należy zapoznać się z różnymi typami subskrypcji. Należy podać różne parametry na podstawie typu subskrypcji, które chcesz uzyskać.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Grupy zasobów i subskrypcje platformy Azure

Subskrypcje platformy Azure i grupy zasobów nie są zasobami platformy Azure. W związku z tym subskrypcje siatki zdarzeń do grup zasobów lub subskrypcji platformy Azure nie mają takich samych właściwości jak subskrypcje siatki zdarzeń do zasobów platformy Azure. Subskrypcje siatki zdarzeń do grup zasobów lub subskrypcji platformy Azure są uważane za globalne.

Aby uzyskać subskrypcje siatki zdarzeń dla subskrypcji platformy Azure i jej grup zasobów, nie trzeba podawać żadnych parametrów. Upewnij się, że wybrano subskrypcję platformy Azure, którą chcesz zbadać. Poniższe przykłady nie otrzymują subskrypcji siatki zdarzeń dla tematów niestandardowych lub zasobów platformy Azure.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Aby uzyskać subskrypcje w siatce zdarzeń dla subskrypcji platformy Azure, podaj typ tematu **Microsoft.Resources.Subscriptions**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Aby uzyskać subskrypcje siatki zdarzeń dla wszystkich grup zasobów w ramach subskrypcji platformy Azure, podaj typ tematu **Microsoft.Resources.ResourceGroups**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Aby uzyskać subskrypcje siatki zdarzeń dla określonej grupy zasobów, podaj nazwę grupy zasobów jako parametr.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Tematy niestandardowe i zasoby platformy Azure

Niestandardowe tematy siatki zdarzeń to zasoby platformy Azure. W związku z tym kwerendy subskrypcji siatki zdarzeń dla tematów niestandardowych i innych zasobów, takich jak konto magazynu obiektów Blob, w ten sam sposób. Aby uzyskać subskrypcje siatki zdarzeń dla tematów niestandardowych, należy podać parametry, które identyfikują zasób lub identyfikują lokalizację zasobu. Nie jest możliwe, aby szeroko kwerendy subskrypcji siatki zdarzeń dla zasobów w ramach subskrypcji platformy Azure.

Aby uzyskać subskrypcje siatki zdarzeń dla tematów niestandardowych i innych zasobów w lokalizacji, podaj nazwę lokalizacji.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Aby uzyskać subskrypcje tematów niestandardowych dla lokalizacji, podaj lokalizację i typ tematu **Microsoft.EventGrid.Topics**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Aby uzyskać subskrypcje kont magazynu dla lokalizacji, podaj lokalizację i typ tematu **Microsoft.Storage.StorageAccounts**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Aby uzyskać subskrypcje siatki zdarzeń dla tematu niestandardowego, podaj nazwę tematu niestandardowego i nazwę jego grupy zasobów.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Aby uzyskać subskrypcje siatki zdarzeń dla określonego zasobu, podaj identyfikator zasobu.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o dostarczaniu zdarzeń i ponownych próbach, [w ucho.](delivery-and-retry.md)
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z usługi Event Grid, zobacz [Tworzenie i rozsyłanie zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
