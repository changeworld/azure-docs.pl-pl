---
title: Wdrażanie zasobów między subskrypcjami & grupy zasobów
description: Pokazuje, jak kierować więcej niż jedną subskrypcję platformy Azure i grupę zasobów podczas wdrażania.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 70868f5a3598c26ffff81f0ad3536a6c5c0a7e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460351"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Wdrażanie zasobów platformy Azure w więcej niż jednej subskrypcji lub grupie zasobów

Zazwyczaj wszystkie zasoby w szablonie można wdrożyć w jednej [grupie zasobów](../management/overview.md). Istnieją jednak scenariusze, w których chcesz wdrożyć zestaw zasobów razem, ale umieścić je w różnych grupach zasobów lub subskrypcji. Na przykład można wdrożyć maszynę wirtualną kopii zapasowej dla usługi Azure Site Recovery do oddzielnej grupy zasobów i lokalizacji. Menedżer zasobów umożliwia używanie szablonów zagnieżdżonych do kierowania na więcej niż jedną subskrypcję i grupę zasobów.

> [!NOTE]
> Można wdrożyć tylko do pięciu grup zasobów w jednym wdrożeniu. Zazwyczaj to ograniczenie oznacza, że można wdrożyć do jednej grupy zasobów określonej dla szablonu nadrzędnego i maksymalnie czterech grup zasobów w zagnieżdżonych lub połączonych wdrożeniach. Jeśli jednak szablon nadrzędny zawiera tylko szablony zagnieżdżone lub połączone i sam nie wdraża żadnych zasobów, można uwzględnić maksymalnie pięć grup zasobów w wdrożeniach zagnieżdżonych lub połączonych.

## <a name="specify-subscription-and-resource-group"></a>Określanie subskrypcji i grupy zasobów

Aby kierować reklamy na inną grupę zasobów lub subskrypcję, użyj [szablonu zagnieżdżonego lub połączonego](linked-templates.md). Typ `Microsoft.Resources/deployments` zasobu zawiera `subscriptionId` `resourceGroup`parametry i , które umożliwiają określenie subskrypcji i grupy zasobów dla wdrożenia zagnieżdżonego. Jeśli nie określisz identyfikatora subskrypcji lub grupy zasobów, używana jest grupa subskrypcji i zasobów z szablonu nadrzędnego. Wszystkie grupy zasobów muszą istnieć przed uruchomieniem wdrożenia.

Konto używane do wdrażania szablonu musi mieć uprawnienia do wdrażania do określonego identyfikatora subskrypcji. Jeśli określona subskrypcja istnieje w innej dzierżawie usługi Azure Active Directory, należy [dodać użytkowników-gościa z innego katalogu.](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)

Aby określić inną grupę zasobów i subskrypcję, należy użyć:

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

Jeśli grupy zasobów są w tej samej subskrypcji, można usunąć **subscriptionId** wartość.

W poniższym przykładzie wdraża dwa konta magazynu. Pierwsze konto magazynu jest wdrażane w grupie zasobów określonej podczas wdrażania. Drugie konto magazynu jest wdrażane w grupie `secondResourceGroup` `secondSubscriptionID` zasobów określonej w parametrach i:

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
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2017-06-01",
      "name": "[variables('firstStorageName')]",
      "location": "[resourceGroup().location]",
      "sku":{
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate",
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
            "apiVersion": "2017-06-01",
            "name": "[variables('secondStorageName')]",
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
    }
  ]
}
```

Jeśli ustawiono `resourceGroup` nazwę grupy zasobów, która nie istnieje, wdrożenie zakończy się niepowodzeniem.

Aby przetestować poprzedni szablon i wyświetlić wyniki, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Aby wdrożyć dwa konta magazynu w dwóch grupach zasobów w **tej samej subskrypcji,** należy użyć:

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

Aby wdrożyć dwa konta magazynu w **dwóch subskrypcjach,** należy użyć:

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

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby wdrożyć dwa konta magazynu w dwóch grupach zasobów w **tej samej subskrypcji,** należy użyć:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Aby wdrożyć dwa konta magazynu w **dwóch subskrypcjach,** należy użyć:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Korzystanie z funkcji

Funkcje [resourceGroup()](template-functions-resource.md#resourcegroup) i [subscription()](template-functions-resource.md#subscription) rozwiązują się w różny sposób w zależności od sposobu określania szablonu. Podczas łączenia z szablonem zewnętrznym funkcje zawsze są rozpoznawane w zakresie tego szablonu. Podczas zagnieżdżania szablonu `expressionEvaluationOptions` w szablonie nadrzędnym należy użyć właściwości, aby określić, czy funkcje są rozpoznawane w grupie zasobów i subskrypcji szablonu nadrzędnego lub szablonu zagnieżdżonego. Ustaw właściwość, aby `inner` rozpoznać zakres dla szablonu zagnieżdżonego. Ustaw właściwość, aby `outer` rozpoznać zakres szablonu nadrzędnego.

W poniższej tabeli pokazano, czy funkcje są rozpoznawane w nadrzędnej lub osadzonej grupie zasobów i subskrypcji.

| Typ szablonu | Zakres | Rozwiązanie |
| ------------- | ----- | ---------- |
| Zagnieżdżone        | zewnętrzne (domyślnie) | Nadrzędna grupa zasobów |
| Zagnieżdżone        | Wewnętrzny | Podgrupa zasobów |
| Połączone        | Nie dotyczy   | Podgrupa zasobów |

Poniższy [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) pokazuje:

* szablon zagnieżdżony z domyślnym (zewnętrznym) zakresem
* szablon zagnieżdżony z zakresem wewnętrznym
* szablon połączony

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "defaultScopeTemplate",
      "resourceGroup": "inlineGroup",
      "properties": {
      "mode": "Incremental",
      "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
          ],
          "outputs": {
          "resourceGroupOutput": {
            "type": "string",
            "value": "[resourceGroup().name]"
          }
          }
      },
      "parameters": {}
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "innerScopeTemplate",
      "resourceGroup": "inlineGroup",
      "properties": {
      "expressionEvaluationOptions": {
          "scope": "inner"
      },
      "mode": "Incremental",
      "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
          ],
          "outputs": {
          "resourceGroupOutput": {
            "type": "string",
            "value": "[resourceGroup().name]"
          }
          }
      },
      "parameters": {}
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "resourceGroup": "linkedGroup",
      "properties": {
      "mode": "Incremental",
      "templateLink": {
          "contentVersion": "1.0.0.0",
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/resourceGroupName.json"
      },
      "parameters": {}
      }
    }
  ],
  "outputs": {
    "parentRG": {
      "type": "string",
      "value": "[concat('Parent resource group is ', resourceGroup().name)]"
    },
    "defaultScopeRG": {
      "type": "string",
      "value": "[concat('Default scope resource group is ', reference('defaultScopeTemplate').outputs.resourceGroupOutput.value)]"
    },
    "innerScopeRG": {
      "type": "string",
      "value": "[concat('Inner scope resource group is ', reference('innerScopeTemplate').outputs.resourceGroupOutput.value)]"
    },
    "linkedRG": {
      "type": "string",
      "value": "[concat('Linked resource group is ', reference('linkedTemplate').outputs.resourceGroupOutput.value)]"
    }
  }
}
```

Aby przetestować poprzedni szablon i wyświetlić wyniki, należy użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Dane wyjściowe z poprzedniego przykładu to:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Dane wyjściowe z poprzedniego przykładu to:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak zdefiniować parametry w szablonie, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](template-syntax.md).
* Aby uzyskać wskazówki dotyczące rozwiązywania typowych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure za pomocą usługi Azure Resource Manager](common-deployment-errors.md).
* Aby uzyskać informacje dotyczące wdrażania szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego, zobacz [Wdrażanie szablonu prywatnego za pomocą tokenu sygnatury dostępu Współdzielonego](secure-template-with-sas-token.md).
