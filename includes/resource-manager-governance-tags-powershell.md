---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 21216d19fb8a37d3e9e02e410d39b2a999d3935e
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2018
---
Aby dodać dwa tagi do grupy zasobów, użyj [Set-AzureRmResourceGroup](/powershell/module/azurerm.resources/set-azurermresourcegroup) polecenia:

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ Dept="IT"; Environment="Test" }
```

Załóżmy, że chcesz dodać tag trzecich. Za każdym razem, gdy stosujesz tagi do zasobu lub grupy zasobów, istniejące tagi tego zasobu lub tej grupy zasobów są zastępowane. Aby dodać nowy znacznik bez utraty istniejących tagi, należy pobrać istniejące znaczniki, Dodaj nowy znacznik i zastosuj je ponownie zbiór znaczników:

```azurepowershell-interactive
# Get existing tags and add a new tag
$tags = (Get-AzureRmResourceGroup -Name myResourceGroup).Tags
$tags += @{Project="Documentation"}

# Reapply the updated set of tags 
Set-AzureRmResourceGroup -Tag $tags -Name myResourceGroup
```

Zasoby nie dziedziczy tagi grupy zasobów. Obecnie grupa zasobów zawiera trzy znaczniki, ale zasoby nie ma żadnych znaczników. Aby zastosować wszystkie tagi z grupy zasobów, do jej zasobów i zachować istniejące znaczniki na zasoby, które nie są duplikatami, użyj następującego skryptu:

```azurepowershell-interactive
# Get the resource group
$group = Get-AzureRmResourceGroup myResourceGroup

if ($group.Tags -ne $null) {
    # Get the resources in the resource group
    $resources = $group | Find-AzureRmResource

    # Loop through each resource
    foreach ($r in $resources)
    {
        # Get the tags for this resource
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags

        # Loop through each tag from the resource group
        foreach ($key in $group.Tags.Keys)
        {
            # Check if the resource already has a tag for the key from the resource group. If so, remove it from the resource
            if (($resourcetags) -AND ($resourcetags.ContainsKey($key))) { $resourcetags.Remove($key) }
        }

        # Add the tags from the resource group to the resource tags
        $resourcetags += $group.Tags

        # Reapply the updated tags to the resource 
        Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
    }
}
```

Alternatywnie można zastosować tagi z grupy zasobów do zasobów bez zachować istniejące znaczniki:

```azurepowershell-interactive
# Get the resource group
$g = Get-AzureRmResourceGroup -Name myResourceGroup

# Find all the resources in the resource group, and for each resource apply the tags from the resource group
Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
```

Aby połączyć kilka wartości w jeden tag, użyj ciągu JSON.

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ CostCenter="{`"Dept`":`"IT`",`"Environment`":`"Test`"}" }
```

Aby usunąć wszystkie tagi, należy przekazać tablicy skrótów puste.

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ }
```
