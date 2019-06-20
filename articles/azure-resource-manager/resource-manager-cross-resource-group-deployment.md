---
title: Wdrażaj zasoby platformy Azure do wielu subskrypcji i grup zasobów | Dokumentacja firmy Microsoft
description: Pokazuje, jak pod kątem więcej niż jeden Azure subskrypcji i grupie zasobów podczas wdrażania.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/02/2018
ms.author: tomfitz
ms.openlocfilehash: 33b0a998206b68f1807f5bfa3c3f39164798842c
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205486"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Wdrażaj zasoby platformy Azure do więcej niż jedną subskrypcję lub grupę zasobów

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zazwyczaj można wdrażać wszystkich zasobów w szablonie do pojedynczego [grupy zasobów](resource-group-overview.md). Jednak istnieją scenariusze, w której chcesz wdrożyć zestaw zasobów ze sobą, ale umieścić je w różnych grupach zasobów lub subskrypcji. Na przykład można wdrożyć kopii zapasowej maszyny wirtualnej dla usługi Azure Site Recovery do oddzielnej grupie zasobów i lokalizacji. Usługi Resource Manager umożliwia korzystanie z szablonów zagnieżdżonych do różnych subskrypcji docelowej i grup zasobów niż subskrypcję i grupę zasobów używaną dla szablonu nadrzędnego.

> [!NOTE]
> Można wdrożyć tylko pięć grup zasobów w ramach pojedynczego wdrożenia. Zwykle to ograniczenie oznacza, że można wdrożyć do jednej grupy zasobów określona dla szablonu nadrzędnego i maksymalnie czterech grup zasobów w połączonych lub zagnieżdżonych wdrożeń. Jednak jeśli sama nie jest wdrażana żadnych zasobów szablonu nadrzędnego zawiera tylko połączonych lub zagnieżdżonych szablonów, można dołączyć maksymalnie pięć grup zasobów w połączonych lub zagnieżdżonych wdrożeń.

## <a name="specify-a-subscription-and-resource-group"></a>Określ subskrypcję i grupę zasobów

Aby skierować je do innego zasobu, należy użyć szablonu połączonych lub zagnieżdżonych. `Microsoft.Resources/deployments` Typ zasobu zawiera parametry `subscriptionId` i `resourceGroup`. Właściwości te umożliwiają określenie różnych subskrypcji i grupy zasobów dla wdrożenia zagnieżdżonego. Wszystkie grupy zasobów musi istnieć przed uruchomieniem wdrożenia. Jeśli nie określisz subskrypcji identyfikator lub grupy zasobów, subskrypcji i grupy zasobów z szablonu nadrzędnego jest używany.

Konto, którego używasz do wdrożenia szablonu, musi mieć uprawnienia do wdrażania do identyfikatora określonej subskrypcji. Jeśli wybrana subskrypcja istnieje w innej dzierżawie usługi Azure Active Directory, musisz najpierw [dodać użytkowników-gości z innego katalogu](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Aby określić inną grupę zasobów i subskrypcji, należy użyć:

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

Jeśli grup zasobów znajdują się w tej samej subskrypcji, możesz usunąć **subscriptionId** wartości.

Poniższy przykład służy do wdrażania dwóch kont magazynu — jedną w grupie zasobów, określony podczas wdrażania i jeden w grupie zasobów określonej w `secondResourceGroup` parametru:

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

Jeśli ustawisz `resourceGroup` nazwę grupy zasobów, która nie istnieje, wdrożenie zakończy się niepowodzeniem.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>Użyj funkcji resourceGroup() i subscription()

Dla wielu wdrożeniach grupy zasobów, [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) i [subscription()](resource-group-template-functions-resource.md#subscription) funkcji rozwiązania uzależnione określania zagnieżdżonych szablonów. 

Jeśli osadzasz jednego szablonu w ramach innego szablonu, funkcje w zagnieżdżonych szablonów prowadzić do nadrzędnej grupy zasobów i subskrypcji. Osadzony szablonu posługuje się następującym formatem:

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

Jeśli łączysz się do oddzielnych szablonu funkcji w szablonie połączone prowadzić do grupy zagnieżdżone zasobów i subskrypcji. Połączony szablon posługuje się następującym formatem:

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

## <a name="example-templates"></a>Przykładowe szablony

Poniższe szablony pokazują wielu wdrożeń będących częścią grupy zasobów. Skrypty, aby wdrożyć szablony są wyświetlane pod tabelą.

|Szablon  |Opis  |
|---------|---------|
|[Wiele szablonów subskrypcji](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Wdraża jedno konto magazynu do jednej grupy zasobów i jedno konto magazynu do drugiej grupy zasobów. Dołączyć wartość dla Identyfikatora subskrypcji, gdy druga grupa zasobów znajduje się w innej subskrypcji. |
|[Obejmujące wiele zasobów grupy właściwości szablonu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Pokazuje, jak `resourceGroup()` funkcja rozwiązuje. Go nie wdrażają żadnych zasobów. |

### <a name="powershell"></a>PowerShell

Dla programu PowerShell, aby wdrożyć dwa konta magazynu na dwie grupy zasobów w **tej samej subskrypcji**, użyj:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Dla programu PowerShell, aby wdrożyć dwa konta magazynu do **dwiema subskrypcjami**, użyj:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

Dla programu PowerShell, aby przetestować sposób, w jaki **obiektu grupy zasobów** jest rozpoznawana jako na użytek szablonu nadrzędnego, wbudowany szablon i połączony szablon:

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

W poprzednim przykładzie zarówno **parentRG** i **inlineRG** rozpoznać **parentGroup**. **linkedRG** jest rozpoznawana jako **linkedGroup**. Dane wyjściowe z poprzedniego przykładu są:

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

Wiersza polecenia platformy Azure, aby wdrożyć dwa konta magazynu na dwie grupy zasobów w **tej samej subskrypcji**, użyj:

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

Wiersza polecenia platformy Azure, aby wdrożyć dwa konta magazynu do **dwiema subskrypcjami**, użyj:

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

Wiersza polecenia platformy Azure, aby przetestować sposób, w jaki **obiektu grupy zasobów** jest rozpoznawana jako na użytek szablonu nadrzędnego, wbudowany szablon i połączony szablon:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

W poprzednim przykładzie zarówno **parentRG** i **inlineRG** rozpoznać **parentGroup**. **linkedRG** jest rozpoznawana jako **linkedGroup**. Dane wyjściowe z poprzedniego przykładu są:

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

* Aby dowiedzieć się, jak zdefiniować parametry w szablonie, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby uzyskać porady dotyczące rozwiązywania typowych problemów wdrażania, zobacz [Rozwiązywanie typowych problemów wdrażania na platformie Azure przy użyciu usługi Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Aby uzyskać informacje o wdrażaniu szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego, zobacz [wdrażanie prywatnego szablonu przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-powershell-sas-token.md).
