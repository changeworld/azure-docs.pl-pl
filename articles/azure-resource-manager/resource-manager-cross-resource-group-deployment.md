---
title: Wdrażanie zasobów platformy Azure do wielu grup subskrypcji i zasobu | Dokumentacja firmy Microsoft
description: Pokazuje, jak docelowy kilku grup Azure subskrypcji i zasobu podczas wdrażania.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2018
ms.author: tomfitz
ms.openlocfilehash: 5e67c60828467cce7c3b40ba17f15f44ad045920
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735674"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Wdrażanie zasobów platformy Azure na więcej niż jedną subskrypcję lub grupy zasobów

Zazwyczaj jest wdrażany, wszystkie zasoby w szablonie do pojedynczego [grupy zasobów](resource-group-overview.md). Istnieją jednak scenariuszy, w której chcesz wdrożyć razem zestaw zasobów, ale umieszczenie ich w różnych grupach zasobów lub subskrypcji. Na przykład możesz wdrażanie kopii zapasowej maszyny wirtualnej dla usługi Azure Site Recovery na oddzielnej grupie zasobów i lokalizacji. Menedżer zasobów pozwala na użycie zagnieżdżone szablony dla różnych subskrypcji docelowej i grup zasobów niż subskrypcji i grupy zasobów używany do szablonu nadrzędnego.

> [!NOTE]
> Można wdrożyć tylko pięć grup zasobów w ramach jednego wdrożenia. Zwykle to ograniczenie oznacza, że można wdrożyć do jednej grupy zasobów określony dla szablonu nadrzędnego i maksymalnie czterech grup zasobów w przypadku wdrożeń zagnieżdżonych lub połączony. Jednak jeśli szablon nadrzędnego zawiera tylko szablony zagnieżdżonych lub połączony i nie powoduje wdrożenia wszystkich zasobów, można dołączyć maksymalnie pięć grup zasobów w przypadku wdrożeń zagnieżdżonych lub połączony.

## <a name="specify-a-subscription-and-resource-group"></a>Określ grupę subskrypcji i zasobu

Aby skierować je do różnych zasobów, użyj szablonu zagnieżdżonych lub połączony. `Microsoft.Resources/deployments` Typu zasobu zawiera parametry `subscriptionId` i `resourceGroup`. Te właściwości umożliwiają określenie innej subskrypcji i zasobu grupy wdrożenia zagnieżdżonego. Wszystkie grupy zasobów musi istnieć przed uruchomieniem wdrożenia. Jeśli nie określisz subskrypcji identyfikator lub grupy zasobów, subskrypcji i grupy zasobów z szablonu nadrzędnego jest używany.

Konto, którego używasz do wdrożenia szablonu musi mieć uprawnienia do wdrażania na identyfikator określonej subskrypcji. Jeśli określona subskrypcja istnieje w innej dzierżawie usługi Azure Active Directory, należy najpierw [Dodaj gości z innego katalogu](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Aby określić innej grupie zasobów i subskrypcji, należy użyć:

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

W przypadku grup zasobów w tej samej subskrypcji, możesz usunąć **subscriptionId** wartości.

Poniższy przykład wdraża dwóch kont magazynu — w grupie zasobów określony podczas wdrażania, i określić jedną w grupie zasobów w `secondResourceGroup` parametru:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

Jeśli ustawisz `resourceGroup` Nazwa grupy zasobów, która nie istnieje, wdrożenie zakończy się niepowodzeniem.

Aby wdrożyć przykładowy szablon, użyj programu Azure PowerShell 4.0.0 lub nowszej lub Azure CLI 2.0.0 lub nowszym.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>Użyj funkcji resourceGroup() i subscription()

Dla wielu wdrożenia grupy zasobów, [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) i [subscription()](resource-group-template-functions-resource.md#subscription) funkcje rozwiązać uzależnione określania szablon zagnieżdżony. 

Po osadzeniu jeden szablon w ramach innego szablonu funkcji w szablonie zagnieżdżonym rozpoznać subskrypcji i grupy zasobów nadrzędnej. Osadzony szablonu używany następujący format:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() and subscription() refer to parent resource group/subscription
    }
}
```

Jeśli możesz połączyć się z oddzielnych szablonu, funkcje w szablonie połączonego rozpoznać subskrypcji i grupy zagnieżdżone zasobów. Połączone szablonu używany następujący format:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() and subscription() in linked template refer to linked resource group/subscription
    }
}
```

## <a name="example-templates"></a>Przykład szablonów

Następujące szablony pokazują wielu wdrożeń grupy zasobów. Skrypty wdrażania szablony są wyświetlane pod tabelą.

|Szablon  |Opis  |
|---------|---------|
|[Krzyżowe szablonu subskrypcji](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Wdraża jedno konto magazynu do jednej grupy zasobów i jedno konto magazynu do drugiej grupy zasobów. Zawierać wartość dla Identyfikatora subskrypcji, gdy drugi grupa zasobów jest w innej subskrypcji. |
|[Krzyżowe szablon właściwości grupy zasobów](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Pokazuje, jak `resourceGroup()` funkcji jest rozpoznawana. Nie powoduje wdrożenia żadnych zasobów. |

### <a name="powershell"></a>PowerShell

Dla programu PowerShell, aby wdrożyć dwa konta magazynu na dwie grupy zasobów w **tej samej subskrypcji**, użyj:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzureRmResourceGroup -Name $firstRG -Location southcentralus
New-AzureRmResourceGroup -Name $secondRG -Location eastus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Dla programu PowerShell, aby wdrożyć dwa konta magazynu do **dwóch subskrypcje**, użyj:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzureRmSubscription -Subscription $secondSub
New-AzureRmResourceGroup -Name $secondRG -Location eastus

Select-AzureRmSubscription -Subscription $firstSub
New-AzureRmResourceGroup -Name $firstRG -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

Dla programu PowerShell, aby przetestować sposób **obiektu grupy zasobów** rozpoznaje użycie szablonu nadrzędnego, wbudowanego szablonu i połączone szablonu:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

W powyższym przykładzie zarówno **parentRG** i **inlineRG** rozpoznać **parentGroup**. **linkedRG** jest rozpoznawana jako **linkedGroup**. Dane wyjściowe z poprzedniego przykładu to:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 inlineRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 linkedRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
                                               "name": "linkedGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Dla interfejsu wiersza polecenia Azure, aby wdrożyć dwa konta magazynu na dwie grupy zasobów w **tej samej subskrypcji**, użyj:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Dla interfejsu wiersza polecenia Azure, aby wdrożyć dwa konta magazynu do **dwóch subskrypcje**, użyj:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

Dla interfejsu wiersza polecenia Azure, aby przetestować sposób **obiektu grupy zasobów** rozpoznaje użycie szablonu nadrzędnego, wbudowanego szablonu i połączone szablonu:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

W powyższym przykładzie zarówno **parentRG** i **inlineRG** rozpoznać **parentGroup**. **linkedRG** jest rozpoznawana jako **linkedGroup**. Dane wyjściowe z poprzedniego przykładu to:

```azurecli
...
"outputs": {
  "inlineRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "linkedRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
      "location": "southcentralus",
      "name": "linkedGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "parentRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  }
},
...
```

## <a name="next-steps"></a>Kolejne kroki

* Aby poznać sposób definiowania parametry w szablonie, zobacz [poznać strukturę i składni szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby uzyskać wskazówki dotyczące rozwiązania typowych błędów wdrażania, zobacz [Rozwiąż typowe błędy wdrożenia usługi Azure z usługą Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Aby uzyskać informacje o wdrażaniu szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego, zobacz [wdrażanie szablonu prywatnej przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-powershell-sas-token.md).
