---
title: Szablon usługi Azure Resource Manager umożliwia tworzenie obszaru roboczego
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak utworzyć nowy obszar roboczy usługi Azure Machine Learning za pomocą szablonu usługi Azure Resource Manager.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 7349998325e56d5ebb78de5ca30c0127f09102aa
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883194"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Użyj szablonu usługi Azure Resource Manager utworzyć obszar roboczy usługi Azure Machine Learning

W tym artykule dowiesz się, Utwórz obszar roboczy usługi Azure Machine Learning, za pomocą szablonów usługi Azure Resource Manager na kilka sposobów. Szablon usługi Resource Manager ułatwia tworzenie zasobów jako jednej, skoordynowanej operacji. Szablon to dokument JSON, który definiuje zasoby, które są potrzebne do wdrożenia. Może on również określać parametrów wdrożenia. Parametry umożliwiają podawanie wartości wejściowych, korzystając z tego szablonu.

Aby uzyskać więcej informacji, zobacz [wdrażania aplikacji przy użyciu szablonu usługi Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcji platformy Azure**. Jeśli nie masz, spróbuj [bezpłatnej i płatnej wersji usługi Azure Machine Learning](https://aka.ms/AMLFree).

* Aby użyć szablonu z interfejsu wiersza polecenia, musisz mieć albo [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Następujący szablon usługi Resource Manager może służyć do tworzenia obszaru roboczego usługi Azure Machine Learning i skojarzonych zasobów platformy Azure:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "The name of the Azure Machine Learning service workspace."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "eastus2",
                "southcentralus",
                "southeastasia",
                "westcentralus",
                "westeurope",
                "westus2"
            ],
            "metadata": {
                "description": "The location where the workspace will be created."
            }
        }
    },
    "variables": {
        "storageAccount": {
            "name": "[concat('sa',uniqueString(resourceGroup().id))]",
            "type": "Standard_LRS"
        },
        "keyVault": {
            "name": "[concat('kv',uniqueString(resourceGroup().id))]",
            "tenantId": "[subscription().tenantId]"
        },
        "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
        "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "name": "[variables('storageAccount').name]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2018-07-01",
            "sku": {
                "name": "[variables('storageAccount').type]"
            },
            "kind": "StorageV2",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "supportHttpsTrafficOnly": true
            }
        },
        {
            "name": "[variables('keyVault').name]",
            "type": "Microsoft.KeyVault/vaults",
            "apiVersion": "2018-02-14",
            "location": "[parameters('location')]",
            "properties": {
                "tenantId": "[variables('keyVault').tenantId]",
                "sku": {
                    "name": "standard",
                    "family": "A"
                },
                "accessPolicies": []
            }
        },
        {
            "name": "[variables('applicationInsightsName')]",
            "type": "Microsoft.Insights/components",
            "apiVersion": "2015-05-01",
            "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
            "kind": "web",
            "properties": {
                "Application_Type": "web"
            }
        },
        {
            "name": "[variables('containerRegistryName')]",
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2017-10-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "adminUserEnabled": true
            }
        },
        {
            "name": "[parameters('workspaceName')]",
            "type": "Microsoft.MachineLearningServices/workspaces",
            "apiVersion": "2018-11-19",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('storageAccount').name]",
                "[variables('keyVault').name]",
                "[variables('applicationInsightsName')]",
                "[variables('containerRegistryName')]"
            ],
            "identity": {
                "type": "systemAssigned"
            },
            "properties": {
                "friendlyName": "[parameters('workspaceName')]",
                "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVault').name)]",
                "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
                "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
                "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccount').name)]"
            }
        }
    ]
}
```

Ten szablon umożliwia utworzenie następujących usług platformy Azure:

* Grupa zasobów platformy Azure
* Konto usługi Azure Storage
* W usłudze Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Obszar roboczy usługi Azure Machine Learning

Grupa zasobów to kontener, który zawiera usługi. Różne usługi są wymagane przez obszar roboczy usługi Azure Machine Learning.

Przykładowy szablon zawiera dwa parametry:

* **Lokalizacji** której zostanie utworzona grupa zasobów i usług.

    Szablon będzie używać lokalizacji, którą wybierzesz dla większości zasobów. Wyjątkiem jest usługi Application Insights, która nie jest dostępna we wszystkich lokalizacjach, które są inne usługi. Wybranie lokalizacji, gdy nie są dostępne, usługa zostanie utworzony w lokalizacji południowo-środkowe stany USA.

* **Nazwa obszaru roboczego**, czyli przyjazną nazwę obszaru roboczego usługi Azure Machine Learning.

    Nazwy inne usługi są generowane losowo.

Aby uzyskać więcej informacji dotyczących szablonów zobacz następujące artykuły:

* [Tworzenie szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Wdrażanie aplikacji przy użyciu szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md)
* [Typy zasobów Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Postępuj zgodnie z instrukcjami w [wdrażanie zasobów za pomocą szablonu niestandardowej](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Po przyjeździe do biura __Edytuj szablon__ ekranu, Wklej w szablonie z tego dokumentu.
1. Wybierz __Zapisz__ używać szablonu. Należy podać następujące informacje i zgody na liście warunków i postanowień:

   * Subskrypcja: Wybierz subskrypcję platformy Azure do użycia dla tych zasobów.
   * Grupa zasobów: Wybierz lub Utwórz grupę zasobów w celu uwzględnienia usług.
   * Nazwa obszaru roboczego: Nazwa do użycia dla obszaru roboczego usługi Azure Machine Learning, która zostanie utworzona. Nazwa obszaru roboczego musi być od 3 do 33 znaków. Może zawierać tylko znaki alfanumeryczne i "-".
   * Lokalizacja: Wybierz lokalizację, w którym zostanie utworzone zasoby.

     ![Parametry szablonu w witrynie Azure portal](media/how-to-create-workspace-template/template-parameters.png)

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonu niestandardowej](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

W tym przykładzie założono, że zapisano szablon do pliku o nazwie `azuredeploy.json` w bieżącym katalogu:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i programu Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) i [wdrażanie prywatnego szablonu usługi Resource Manager przy użyciu tokenu sygnatury dostępu Współdzielonego i programu Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W tym przykładzie założono, że zapisano szablon do pliku o nazwie `azuredeploy.json` w bieżącym katalogu:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace
```

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md) i [wdrażanie prywatnego szablonu usługi Resource Manager przy użyciu tokenu sygnatury dostępu Współdzielonego i wiersza polecenia platformy Azure](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="next-steps"></a>Kolejne kroki

* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu REST API usługi Resource Manager](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
