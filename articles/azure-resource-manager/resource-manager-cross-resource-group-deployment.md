---
title: Wdrażanie zasobów między subskrypcjami & grupy zasobów
description: Pokazuje, w jaki sposób można określić więcej niż jedną subskrypcję platformy Azure i grupę zasobów podczas wdrażania.
ms.topic: conceptual
ms.date: 06/02/2018
ms.openlocfilehash: 99c534e1c51dcdf32c2b3a3b779c01d71b8d0c24
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149556"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Wdrażanie zasobów platformy Azure w więcej niż jednej subskrypcji lub grupie zasobów

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zwykle wszystkie zasoby w szablonie są wdrażane w jednej [grupie zasobów](resource-group-overview.md). Istnieją jednak scenariusze, w których należy wspólnie wdrożyć zestaw zasobów, ale umieścić je w różnych grupach zasobów lub subskrypcjach. Na przykład możesz chcieć wdrożyć maszynę wirtualną kopii zapasowej dla Azure Site Recovery w oddzielnej grupie zasobów i lokalizacji. Menedżer zasobów umożliwia korzystanie z szablonów zagnieżdżonych jako docelowych dla różnych subskrypcji i grup zasobów niż subskrypcja i Grupa zasobów używana dla szablonu nadrzędnego.

> [!NOTE]
> W jednym wdrożeniu można wdrożyć tylko pięć grup zasobów. Zwykle to ograniczenie oznacza, że można wdrożyć do jednej grupy zasobów określonej dla szablonu nadrzędnego oraz do czterech grup zasobów w ramach wdrożeń zagnieżdżonych lub połączonych. Jeśli jednak szablon nadrzędny zawiera tylko szablony zagnieżdżone lub połączone i nie wdraża żadnego zasobu, można dołączyć do pięciu grup zasobów w ramach wdrożeń zagnieżdżonych lub połączonych.

## <a name="specify-a-subscription-and-resource-group"></a>Określ subskrypcję i grupę zasobów

Aby określić obiekt docelowy innego zasobu, użyj szablonu zagnieżdżone lub połączone. Typ zasobu `Microsoft.Resources/deployments` zapewnia parametry dla `subscriptionId` i `resourceGroup`. Te właściwości pozwalają określić inną subskrypcję i grupę zasobów dla wdrożenia zagnieżdżonego. Przed uruchomieniem wdrożenia muszą istnieć wszystkie grupy zasobów. Jeśli nie określisz identyfikatora subskrypcji lub grupy zasobów, zostanie użyta subskrypcja i Grupa zasobów z szablonu nadrzędnego.

Konto używane do wdrożenia szablonu musi mieć uprawnienia do wdrożenia określonego identyfikatora subskrypcji. Jeśli określona subskrypcja istnieje w innej dzierżawie Azure Active Directory, należy [dodać użytkowników-Gości z innego katalogu](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Aby określić inną grupę zasobów i subskrypcję, użyj:

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

Jeśli grupy zasobów znajdują się w tej samej subskrypcji, możesz usunąć wartość identyfikatora **subskrypcji** .

W poniższym przykładzie zostały wdrożone dwa konta magazynu — jeden w grupie zasobów określonej podczas wdrażania i jeden w grupie zasobów określonej w `secondResourceGroup` parametr:

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

Jeśli ustawisz `resourceGroup` na nazwę grupy zasobów, która nie istnieje, wdrożenie nie powiedzie się.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>Korzystanie z funkcji Resources () i subskrypcji ()

W przypadku wdrożeń między grupami zasobów można rozróżnić usługi [()](resource-group-template-functions-resource.md#resourcegroup) i [subskrypcje ()](resource-group-template-functions-resource.md#subscription) w różny sposób w zależności od sposobu określania zagnieżdżonego szablonu. 

Jeśli osadzisz jeden szablon w innym szablonie, funkcje w szablonie zagnieżdżonym rozpoznają się z nadrzędną grupą zasobów i subskrypcją. Szablon osadzony używa następującego formatu:

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

Jeśli połączysz się z osobnym szablonem, funkcje w połączonym szablonie rozpoznają się z zagnieżdżoną grupą zasobów i subskrypcją. Połączony szablon używa następującego formatu:

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

Poniższe szablony przedstawiają wiele wdrożeń grup zasobów. Skrypty do wdrożenia szablonów są wyświetlane po tabeli.

|Szablon  |Opis  |
|---------|---------|
|[Szablon dla wielu subskrypcji](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Wdraża jedno konto magazynu w jednej grupie zasobów i jedno konto magazynu w drugiej grupie zasobów. Dołącz wartość identyfikatora subskrypcji, gdy druga grupa zasobów znajduje się w innej subskrypcji. |
|[Szablon właściwości grupy zasobów krzyżowych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Demonstruje sposób rozwiązywania `resourceGroup()` funkcji. Nie wdraża żadnych zasobów. |

### <a name="powershell"></a>PowerShell

Aby wdrożyć dwa konta magazynu dla programu PowerShell w ramach **jednej subskrypcji**, należy użyć:

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

W celu wdrożenia dwóch kont magazynu w programie PowerShell **należy użyć**następujących poleceń:

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

W przypadku programu PowerShell, aby sprawdzić, jak **obiekt grupy zasobów** jest rozpoznawany dla szablonu nadrzędnego, szablonu wbudowanego i połączonego szablonu, użyj:

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

W poprzednim przykładzie zarówno **parentRG** , jak i **inlineRG** są rozpoznawane jako **nadrzędne**. **linkedRG** jest rozpoznawana jako **połączona**. Dane wyjściowe z poprzedniego przykładu to:

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

W przypadku interfejsu wiersza polecenia platformy Azure w celu wdrożenia dwóch kont magazynu w dwóch grupach zasobów w ramach **tej samej subskrypcji**Użyj:

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

Aby wdrożyć dwa konta magazynu w ramach interfejsu wiersza polecenia platformy **Azure, użyj**polecenia:

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

W przypadku interfejsu wiersza polecenia platformy Azure w celu przetestowania sposobu, w jaki **obiekt grupy zasobów** jest rozpoznawany dla szablonu nadrzędnego, szablonu wbudowanego i połączonego szablonu, użyj:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

W poprzednim przykładzie zarówno **parentRG** , jak i **inlineRG** są rozpoznawane jako **nadrzędne**. **linkedRG** jest rozpoznawana jako **połączona**. Dane wyjściowe z poprzedniego przykładu to:

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

## <a name="next-steps"></a>Następne kroki

* Aby zrozumieć, jak definiować parametry w szablonie, zobacz [Opis struktury i składni szablonów Azure Resource Manager](resource-group-authoring-templates.md).
* Aby uzyskać wskazówki dotyczące rozwiązywania typowych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Informacje o wdrażaniu szablonu wymagającego tokenu SAS można znaleźć w temacie [Deploy Private Template with SAS token](resource-manager-powershell-sas-token.md).
