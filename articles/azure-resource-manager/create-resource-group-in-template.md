---
title: Utwórz grupę zasobów w szablonie usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia nowej grupy zasobów w szablonie usługi Azure Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: 87f81c6b6568cd63eaf10840043511669d634062
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079334"
---
# <a name="create-resource-groups-in-azure-resource-manager-templates"></a>Tworzenie grupy zasobów w szablonach usługi Azure Resource Manager

Aby utworzyć grupę zasobów w szablonie usługi Azure Resource Manager, należy zdefiniować **Microsoft.Resources/resourceGroups** zasób mający nazwę i lokalizację grupy zasobów. Wdróż szablon z subskrypcją platformy Azure. Aby uzyskać więcej informacji o wdrożeniach poziomu subskrypcji, zobacz [wdrażania zasobów w subskrypcji platformy Azure](deploy-to-subscription.md).

Można także wdrożyć zasoby w tej grupie zasobów, w tym samym szablonie.

W tym artykule korzysta z wiersza polecenia platformy Azure i programu PowerShell, aby wdrożyć szablony.

## <a name="create-empty-resource-group"></a>Utwórz pustą grupę zasobów

Poniższy przykład tworzy pustą grupę zasobów.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Aby wdrożyć ten szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment create \
  -n demoEmptyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoRG rgLocation=northcentralus
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name demoEmptyRG `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demogroup `
  -rgLocation northcentralus
```

## <a name="create-several-resource-groups"></a>Utwórz wiele grup zasobów

Użyj [copy element](resource-group-create-multiple.md) z grupami zasobów, aby utworzyć więcej niż jednej grupy zasobów. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgNamePrefix": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "instanceCount": {
            "type": "int"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
            "copy": {
                "name": "rgCopy",
                "count": "[parameters('instanceCount')]"
            },
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Aby wdrożyć ten szablon przy użyciu wiersza polecenia platformy Azure i utworzyć trzy grupy zasobów, należy użyć:

```azurecli-interactive
az deployment create \
  -n demoCopyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json \
  --parameters rgNamePrefix=demoRG rgLocation=northcentralus instanceCount=3
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name demoCopyRG `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json `
  -rgNamePrefix demogroup `
  -rgLocation northcentralus `
  -instanceCount 3
```

## <a name="create-resource-group-and-deploy-resource"></a>Utwórz grupę zasobów i wdrażanie zasobów

Aby utworzyć grupę zasobów i wdrażania zasobów, użyj zagnieżdżonych szablonów. Zagnieżdżony szablon definiuje zasoby do wdrożenia w grupie zasobów. Ustaw zagnieżdżonych szablonów jako zależny od grupy zasobów, aby upewnić się, że grupa zasobów znajduje się przed przystąpieniem do wdrażania zasobów.

Poniższy przykład tworzy grupę zasobów, a następnie wdraża konto magazynu do grupy zasobów.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
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
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

Aby wdrożyć ten szablon przy użyciu wiersza polecenia platformy Azure, należy użyć:

```azurecli-interactive
az deployment create \
  -n demoRGStorage \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json \
  --parameters rgName=rgStorage rgLocation=northcentralus storagePrefix=storage
```

Aby wdrożyć ten szablon przy użyciu programu PowerShell, należy użyć:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name demoRGStorage `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json `
  -rgName rgStorage `
  -rgLocation northcentralus `
  -storagePrefix storage
```

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat wdrożeń poziomu subskrypcji, zobacz [wdrażania zasobów w subskrypcji platformy Azure](deploy-to-subscription.md).
* Aby uzyskać informacje dotyczące rozwiązywania problemów zależności podczas wdrażania, zobacz [Rozwiązywanie typowych problemów wdrażania na platformie Azure przy użyciu usługi Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Aby dowiedzieć się więcej na temat tworzenia szablonów usługi Azure Resource Manager, zobacz [Tworzenie szablonów](resource-group-authoring-templates.md). 
* Aby uzyskać listę dostępnych funkcji w szablonie, zobacz [funkcje szablonu](resource-group-template-functions.md).

