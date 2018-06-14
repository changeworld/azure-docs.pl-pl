---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b9484336add0719749e9f0af56bdd70fa3906ef5
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2018
ms.locfileid: "29532345"
---
Aby dodać dwa tagi do grupy zasobów, użyj [Aktualizacja grupy az](/cli/azure/group#az_group_update) polecenia:

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

Załóżmy, że chcesz dodać tag trzecich. Ponownie uruchom polecenie z nowego tagu. Zostanie ono dodane do istniejących tagów.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

Zasoby nie dziedziczy tagi grupy zasobów. Obecnie grupa zasobów zawiera trzy znaczniki, ale zasoby nie ma żadnych znaczników. Aby zastosować wszystkie tagi z grupy zasobów do jej zasobów i zachować istniejące znaczniki zasobów, użyj następującego skryptu:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Get the tags for this resource
  jsonrtag=$(az resource show --id $resid --query tags)
  
  # Reformat from JSON to space-delimited and equals sign
  rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
  
  # Reapply the updated tags to this resource
  az resource tag --tags $t$rt --id $resid
done
```

Alternatywnie można zastosować tagi z grupy zasobów do zasobów bez zachować istniejące znaczniki:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Apply tags from resource group to this resource
  az resource tag --tags $t --id $resid
done
```

Aby połączyć kilka wartości w jeden tag, użyj ciągu JSON.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

Aby usunąć wszystkie tagi na grupę zasobów, należy użyć:

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
