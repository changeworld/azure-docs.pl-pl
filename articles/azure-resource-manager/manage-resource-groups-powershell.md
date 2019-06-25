---
title: Zarządzanie grupami usługi Azure Resource Manager przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Zarządzanie grupami usługi Azure Resource Manager za pomocą programu Azure PowerShell.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 3d6a102b794ca9c43e1dd18f923f6ce224596499
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296257"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Zarządzanie grupami zasobów usługi Azure Resource Manager przy użyciu programu Azure PowerShell

Dowiedz się, jak używać programu Azure PowerShell za pomocą [usługi Azure Resource Manager](resource-group-overview.md) na zarządzanie grupami zasobów platformy Azure. Aby zarządzać zasobami platformy Azure, zobacz [zarządzanie zasobami platformy Azure przy użyciu programu Azure PowerShell](./manage-resources-powershell.md).

Inne artykuły o zarządzaniu grupami zasobów:

- [Zarządzanie grupami zasobów platformy Azure przy użyciu witryny Azure portal](./manage-resources-portal.md)
- [Zarządzanie grupami zasobów platformy Azure przy użyciu wiersza polecenia platformy Azure](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Co to jest grupa zasobów

Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji. Ogólnie rzecz biorąc Dodaj zasoby, które współużytkują ten sam cykl życia w tej samej grupie zasobów, więc łatwe wdrażanie, aktualizowanie i usuwanie ich jako grupa.

Grupa zasobów przechowuje metadane dotyczące zasobów. Z tego powodu określając lokalizację dla grupy zasobów, określasz miejsce, w którym przechowywane są metadane. Dla zachowania zgodności może być konieczne upewnienie się, że dane są przechowywane w odpowiednim regionie.

Grupa zasobów przechowuje metadane dotyczące zasobów. Po określeniu lokalizacji grupy zasobów, określasz, gdzie są przechowywane metadane.

## <a name="create-resource-groups"></a>Tworzenie grupy zasobów

Poniższy skrypt programu PowerShell tworzy grupę zasobów, a następnie wyświetla grupy zasobów.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Listy grup zasobów

Poniższy skrypt programu PowerShell Wyświetla listę grup zasobów w ramach Twojej subskrypcji.

```azurepowershell-interactive
Get-AzResourceGroup
```

Aby uzyskać jedna grupa zasobów:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Usuwanie grupy zasobów

Poniższy skrypt programu PowerShell usuwa grupę zasobów:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Aby uzyskać więcej informacji o jak usługi Azure Resource Manager porządkuje usuwania zasobów, zobacz [Usuwanie grupy zasobów usługi Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Wdrażanie zasobów do istniejącej grupy zasobów

Zobacz [wdrażania zasobów istniejącą grupę zasobów](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Aby sprawdzić poprawność wdrożenia grupy zasobów, zobacz [AzResourceGroupDeployment testu](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Wdrażanie grupy zasobów i zasobów

Można utworzyć grupę zasobów i wdrażania zasobów w grupie przy użyciu szablonu usługi Resource Manager. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy zasobów i wdrażanie zasobów](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Wdróż ponownie, gdy wdrożenie zakończy się niepowodzeniem

Ta funkcja jest nazywana *wycofywania w przypadku błędu*. Aby uzyskać więcej informacji, zobacz [ponownego wdrażania w przypadku niepowodzenia wdrożenia](./resource-group-template-deploy.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Przenieś do innej grupy zasobów lub subskrypcji

Możesz przenieść zasoby w grupie, do innej grupy zasobów. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](./resource-group-move-resources.md#move-resources) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

## <a name="lock-resource-groups"></a>Zablokuj grup zasobów

Zablokowanie uniemożliwia innym użytkownikom w organizacji przypadkowo usuwanie i modyfikowanie krytyczne zasoby, takie jak subskrypcji platformy Azure, grupę zasobów lub zasobu. 

Poniższy skrypt blokuje grupę zasobów, dlatego nie można usunąć grupy zasobów.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Poniższy skrypt pobiera wszystkie blokady dla grupy zasobów:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](resource-group-lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="tag-resource-groups"></a>Tag grupy zasobów

Znaczniki można dodawać do grup zasobów i zasobów w celu logicznego uporządkowania Twoich zasobów. Aby uzyskać informacje, zobacz [porządkowanie zasobów na platformie Azure za pomocą tagów](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Eksportowanie grupy zasobów do szablonów

Po skonfigurowaniu grupy zasobów, można wyświetlić szablon usługi Resource Manager dla grupy zasobów. Eksportowanie szablonu oferuje dwie korzyści:

- Zautomatyzuj przyszłych wdrożeń rozwiązania, ponieważ szablon zawiera całej infrastruktury.
- Dowiedz się składni szablonu, spoglądając na JavaScript Object Notation (JSON) reprezentujący rozwiązania.

Aby wyeksportować wszystkie zasoby w grupie zasobów, należy użyć [AzResourceGroup eksportu](/powershell/module/az.resources/Export-AzResourceGroup) polecenia cmdlet i podaj nazwę grupy zasobów.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Zapisuje szablon jako pliku lokalnego.

Zamiast eksportować wszystkie zasoby w grupie zasobów, możesz wybrać zasoby do wyeksportowania.

Aby wyeksportować jeden zasób, należy przekazać tego identyfikatora zasobu.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Aby wyeksportować więcej niż jeden zasób, należy przekazać identyfikatorów zasobów w tablicy.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Podczas eksportowania szablonu, można określić, czy parametry są używane w szablonie. Domyślnie uwzględniono parametrów dla nazwy zasobów, ale nie ma wartości domyślnej. Podczas wdrażania należy przekazać wartość tego parametru.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

W zasobie zostanie użyty parametr dla nazwy.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Jeśli używasz `-IncludeParameterDefaultValue` parametru podczas eksportowania szablonu parametru szablonu zawiera wartość domyślną, który jest ustawiony na wartość bieżącą. Możesz użyć tej wartości domyślne lub zastąpić wartość domyślną, przekazując inną wartość.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Jeśli używasz `-SkipResourceNameParameterization` parametru podczas eksportowania szablonu, parametry dla nazwy zasobów nie są uwzględnione w szablonie. Zamiast tego Nazwa zasobu jest ustawiona, bezpośrednio w zasobie jej bieżącą wartość. Nie można dostosować nazwę podczas wdrażania.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Aby uzyskać więcej informacji, zobacz [eksportu pojedynczych i wielu zasobów do szablonu w witrynie Azure portal](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Zarządzanie dostępem do grup zasobów

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](../role-based-access-control/overview.md) to sposób zarządzania dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [zarządzanie dostępem przy użyciu RBAC i programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby poznać usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](./resource-group-overview.md).
- Aby dowiedzieć się składni szablonu usługi Resource Manager, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](./resource-group-authoring-templates.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [samouczki krok po kroku](/azure/azure-resource-manager/).
- Aby wyświetlić schematy szablonów usługi Azure Resource Manager, zobacz [odwołanie do szablonu](/azure/templates/).