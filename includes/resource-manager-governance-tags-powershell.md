---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 5dc4ce00685c74b2974cf1bfb5e8606eb3063e8d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38740583"
---
Aby dodać dwa tagi do grupy zasobów, użyj polecenia [Set-AzureRmResourceGroup](/powershell/module/azurerm.resources/set-azurermresourcegroup):

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ Dept="IT"; Environment="Test" }
```

Załóżmy, że chcesz dodać trzeci tag. Za każdym razem, gdy stosujesz tagi do zasobu lub grupy zasobów, istniejące tagi tego zasobu lub tej grupy zasobów są zastępowane. Aby dodać nowy tag bez utraty istniejących tagów, musisz pobrać istniejące tagi, dodać nowy tag i ponownie zastosować kolekcję tagów:

```azurepowershell-interactive
# Get existing tags and add a new tag
$tags = (Get-AzureRmResourceGroup -Name myResourceGroup).Tags
$tags.Add("Project", "Documentation")

# Reapply the updated set of tags 
Set-AzureRmResourceGroup -Tag $tags -Name myResourceGroup
```

Zasoby nie dziedziczą tagów z grupy zasobów. Obecnie Twoja grupa zasobów zawiera trzy tagi, ale zasoby nie mają żadnych tagów. Aby zastosować wszystkie tagi z grupy zasobów do jej zasobów z zachowaniem tagów istniejących w zasobach, które nie są duplikatami, użyj następującego skryptu:

```azurepowershell-interactive
# Get the resource group
$group = Get-AzureRmResourceGroup myResourceGroup

if ($group.Tags -ne $null) {
    # Get the resources in the resource group
    $resources = Get-AzureRmResource -ResourceGroupName $group.ResourceGroupName

    # Loop through each resource
    foreach ($r in $resources)
    {
        # Get the tags for this resource
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        
        # If the resource has existing tags, add new ones
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }

            # Reapply the updated tags to the resource 
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzureRmResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Alternatywnie możesz zastosować tagi z grupy zasobów do zasobów bez zachowania istniejących tagów:

```azurepowershell-interactive
# Get the resource group
$g = Get-AzureRmResourceGroup -Name myResourceGroup

# Find all the resources in the resource group, and for each resource apply the tags from the resource group
Get-AzureRmResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
```

Aby połączyć kilka wartości w jednym tagu, użyj ciągu JSON.

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ CostCenter="{`"Dept`":`"IT`",`"Environment`":`"Test`"}" }
```

Aby usunąć wszystkie tagi, należy przekazać pustą tablicę skrótów.

```azurepowershell-interactive
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ }
```
