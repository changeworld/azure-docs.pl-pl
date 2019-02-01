---
title: Eksportowanie szablonu usługi Resource Manager przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Aby wyeksportować szablon z grupy zasobów, należy użyć usługi Azure Resource Manager i programu Azure PowerShell.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: tomfitz
ms.openlocfilehash: 0313266c9e9bf7814d4581dc04d70cf80e1f8172
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55494718"
---
# <a name="export-azure-resource-manager-templates-with-powershell"></a>Eksportowanie szablonów usługi Azure Resource Manager przy użyciu programu PowerShell

Usługa Resource Manager umożliwia wyeksportowanie szablonu usługi Resource Manager z istniejących zasobów w ramach subskrypcji. Możesz użyć wygenerowanego szablonu, aby dowiedzieć się więcej o składni szablonu lub aby zautomatyzować ponowne wdrożenie rozwiązania, w razie potrzeby.

Należy pamiętać, że istnieją dwa różne sposoby eksportowania szablonu jest:

* Możesz wyeksportować **prawdziwy Szablon użyty do wdrożenia**. W wyeksportowanym szablonie wszystkie parametry i zmienne występują dokładnie tak, jak w oryginalnym szablonie. To podejście jest przydatne, gdy trzeba pobrać szablonu.
* Możesz wyeksportować **wygenerowany szablon, który reprezentuje bieżący stan grupy zasobów**. Wyeksportowany szablon nie jest oparty na żadnym szablonie użytym do wdrożenia. Zamiast tego tworzy szablon, który jest "snapshot" lub "Kopia zapasowa" grupy zasobów. W wyeksportowanym szablonie zawartych jest wiele zakodowanych wartości i prawdopodobnie mniej parametrów, niż się zwykle definiuje. Użyj tej opcji, aby przeprowadzić ponowne wdrożenie zasobów w tej samej grupie zasobów. Aby użyć tego szablonu do innej grupy zasobów, może być znacznie go zmodyfikować.

W tym artykule opisano obie te metody.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-solution"></a>Wdrażanie rozwiązania

Aby zilustrować oba podejścia do eksportowania szablonu, Zacznijmy od wdrażanie rozwiązania do Twojej subskrypcji. Jeśli masz już grupę zasobów w ramach subskrypcji, który chcesz wyeksportować, nie trzeba wdrożyć to rozwiązanie. Jednak w pozostałej części tego artykułu odnosi się do szablonu dla tego rozwiązania. Przykładowy skrypt wdraża konto magazynu.

```powershell
New-AzResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -DeploymentName NewStorage
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```  

## <a name="save-template-from-deployment-history"></a>Zapisz szablon z historii wdrożenia

Możesz pobrać szablonu z historii wdrożenia przy użyciu [AzureRmResourceGroupDeploymentTemplate Zapisz](/powershell/module/az.resources/save-azresourcegroupdeploymenttemplate) polecenia. Poniższy przykład zapisuje szablon, który wcześniej wdrażania:

```powershell
Save-AzResourceGroupDeploymentTemplate -ResourceGroupName ExampleGroup -DeploymentName NewStorage
```

Zwraca lokalizację szablonu.

```powershell
Path
----
C:\Users\exampleuser\NewStorage.json
```

Otwórz plik i zwróć uwagę, jest on dokładnie szablon, który można użyć do wdrożenia. Parametry i zmienne dopasowania szablonu z serwisu GitHub. Można ponownie wdrożyć ten szablon.

## <a name="export-resource-group-as-template"></a>Eksportowanie grupy zasobów jako szablon

Zamiast pobierania szablonu z historii wdrożenia, możesz pobrać szablon, który reprezentuje bieżący stan grupy zasobów przy użyciu [Export-AzureRmResourceGroup](/powershell/module/az.resources/export-azresourcegroup) polecenia. Użyj tego polecenia, gdy wprowadzono wiele zmian do grupy zasobów i żaden istniejący szablon reprezentuje wszystkie zmiany. Jest on przeznaczony jako migawkę grupy zasobów, które służy do ponownego wdrożenia w tej samej grupie zasobów. Aby użyć wyeksportowanego szablonu do innych rozwiązań, należy go znacznie zmodyfikować.

```powershell
Export-AzResourceGroup -ResourceGroupName ExampleGroup
```

Zwraca lokalizację szablonu.

```powershell
Path
----
C:\Users\exampleuser\ExampleGroup.json
```

Otwórz plik i Zauważ, że różni się od szablonu w usłudze GitHub. Ma różne parametry i żadnych zmiennych. Jednostkę SKU magazynu i lokalizacja są zakodowane na wartości. W poniższym przykładzie pokazano wyeksportowanego szablonu, ale Twój szablon ma nazwę parametru nieco inne:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccounts_nf3mvst4nqb36standardsa_name": {
      "defaultValue": null,
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[parameters('storageAccounts_nf3mvst4nqb36standardsa_name')]",
      "apiVersion": "2016-01-01",
      "location": "southcentralus",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Można ponownie wdrożyć tego szablonu, ale wymaga zgadywania unikatową nazwę konta magazynu. Nazwa parametru jest nieco inne.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -TemplateFile C:\Users\exampleuser\ExampleGroup.json `
  -storageAccounts_nf3mvst4nqb36standardsa_name tfnewstorage0501
```

## <a name="customize-exported-template"></a>Dostosowywanie wyeksportowanego szablonu

Można zmodyfikować tego szablonu, aby był łatwiejszy w obsłudze i bardziej elastycznym. Aby umożliwić większej liczby lokalizacji, zmień właściwość lokalizacji, aby użyć tej samej lokalizacji co grupa zasobów:

```json
"location": "[resourceGroup().location]",
```

Aby uniknąć konieczności odgadywać uniques nazwy konta magazynu, należy usunąć parametr dla nazwy konta magazynu. Dodaj parametr sufiks nazwy magazynu i magazynu jednostki SKU:

```json
"parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
},
```

Dodaj zmienną, która tworzy nazwę konta magazynu za pomocą funkcji uniqueString:

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

Ustaw nazwę konta magazynu do zmiennej:

```json
"name": "[variables('storageAccountName')]",
```

Ustaw jednostkę SKU do parametru:

```json
"sku": {
    "name": "[parameters('storageAccountType')]",
    "tier": "Standard"
},
```

Twój szablon wygląda teraz następująco:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), parameters('storageSuffix'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "[parameters('storageAccountType')]",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Ponownie wdróż zmodyfikowany szablon.

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się, jak za pomocą portalu, aby wyeksportować szablon, zobacz [Eksportowanie szablonu usługi Azure Resource Manager z istniejących zasobów](resource-manager-export-template.md).
* Aby zdefiniować parametry w szablonie, zobacz [Tworzenie szablonów](resource-group-authoring-templates.md#parameters).
* Aby uzyskać porady dotyczące rozwiązywania typowych problemów wdrażania, zobacz [Rozwiązywanie typowych problemów wdrażania na platformie Azure przy użyciu usługi Azure Resource Manager](resource-manager-common-deployment-errors.md).
