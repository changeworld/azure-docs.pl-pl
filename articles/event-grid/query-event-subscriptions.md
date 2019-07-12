---
title: Kwerenda subskrypcji usługi Azure Event Grid
description: Opisuje sposób wyświetlenia listy subskrypcji usługi Azure Event Grid.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: spelluru
ms.openlocfilehash: 08fa9ada97c652ca609a499da1c8e87f7e4487db
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786625"
---
# <a name="query-event-grid-subscriptions"></a>Kwerenda subskrypcji usługi Event Grid 

W tym artykule opisano sposób wyświetlenia listy subskrypcji usługi Event Grid w subskrypcji platformy Azure. Podczas wykonywania zapytań Twojej istniejącej subskrypcji usługi Event Grid, ważne jest zrozumienie różnych typów subskrypcji. Możesz podać różnych parametrów, w zależności od typu subskrypcji, którą chcesz pobrać.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Grupy zasobów i subskrypcji platformy Azure

Subskrypcje platformy Azure i grup zasobów nie są zasobami platformy Azure. W związku z tym subskrypcje usługi event grid do grup zasobów lub subskrypcji platformy Azure nie mają te same właściwości co subskrypcje usługi event grid do zasobów platformy Azure. Subskrypcje usługi Event grid do grup zasobów lub subskrypcji platformy Azure są traktowane jako globalne.

Aby uzyskać subskrypcje usługi event grid dla subskrypcji platformy Azure i jej grupy zasobów, nie trzeba podać parametry. Upewnij się, że wybrano subskrypcji platformy Azure, który chcesz zbadać. Poniższe przykłady nie uzyskasz subskrypcje usługi event grid tematy niestandardowe lub zasobów platformy Azure.

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

Aby uzyskać subskrypcje usługi event grid dla subskrypcji platformy Azure, należy podać typ tematu **Microsoft.Resources.Subscriptions**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions" --location global
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Aby uzyskać subskrypcje usługi event grid dla wszystkich grup zasobów w ramach subskrypcji platformy Azure, należy podać typ tematu **Microsoft.Resources.ResourceGroups**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups" --location global
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Aby uzyskać subskrypcje usługi event grid dla określonej grupie zasobów, należy podać nazwę grupy zasobów jako parametr.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup --location global
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Tematy niestandardowe i zasobów platformy Azure

Niestandardowe tematy usługi Event grid to zasoby platformy Azure. W związku z tym zapytania subskrypcje usługi event grid tematy niestandardowe i inne zasoby, takie jak konta usługi Blob storage, w taki sam sposób. Aby uzyskać subskrypcje usługi event grid dla tematy niestandardowe, należy podać parametry, które zidentyfikowanie zasobu lub określić lokalizację zasobu. Nie jest możliwe szeroko zapytania subskrypcje usługi event grid dla zasobów w ramach subskrypcji platformy Azure.

Aby uzyskać subskrypcje usługi event grid tematy niestandardowe i inne zasoby w lokalizacji, należy podać nazwę lokalizacji.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Aby uzyskać subskrypcji tematy niestandardowe dla lokalizacji, podaj lokalizację i Typ tematu **Microsoft.EventGrid.Topics**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Aby uzyskać subskrypcji do konta magazynu dla lokalizacji, podaj lokalizację i Typ tematu **Microsoft.Storage.StorageAccounts**.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Aby uzyskać subskrypcje usługi event grid dla tematu niestandardowego, podaj nazwę tematu niestandardowego i nazwę jej grupy zasobów.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Aby uzyskać subskrypcje usługi event grid dla określonego zasobu, należy podać identyfikator zasobu.

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

* Informacje o dostarczanie zdarzeń i ponownych prób [dostarczanie komunikatów usługi Event Grid i ponów próbę](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć pracę, przy użyciu usługi Event Grid, zobacz [Utwórz i wyznaczać trasy zdarzeń niestandardowych za pomocą usługi Azure Event Grid](custom-event-quickstart.md).
