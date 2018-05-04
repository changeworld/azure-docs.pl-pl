---
title: Tworzenie usługi Azure Machine Learning eksperymenty z szablonem usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono przykład do utworzenia konta Azure Machine Learning eksperymenty przy użyciu szablonu usługi Azure Resource Manager.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: 6f3e08cf36a27d8f42291d13e82458e3fcebc03f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Skonfiguruj usługę Azure Machine Learning eksperymenty

## <a name="overview"></a>Przegląd
Konto usługi Azure Machine Learning eksperymenty usługi, obszar roboczy i projektu są zasobów Azure. Tak można je wdrażać za pomocą szablonów Menedżera zasobów. Szablony usługi Resource Manager są plikami JSON definiującymi zasoby, które należy wdrożyć dla danego rozwiązania. Aby zrozumieć pojęcia związane z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Usługa Azure Resource Manager — omówienie](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>Wdrażanie szablonu
Wdrażanie szablonu wymaga tylko kilka kroków w interfejsu wiersza polecenia platformy Azure lub w portalu Azure.

### <a name="deploy-a-template-from-the-command-line"></a>Wdrażanie szablonu z wiersza polecenia
Przy użyciu interfejsu wiersza polecenia, pojedynczego polecenia, można wdrożyć szablon do istniejącej grupy zasobów.
Zobacz poniższe informacje dotyczące tworzenia szablonu.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Wdrażanie szablonu z portalu Azure
Jeśli wolisz, umożliwia także portalu Azure do tworzenia i wdrażania szablonu. Wykonaj następujące czynności:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** i wyszukaj "Szablony".
3. Wybierz **szablony**.
4. Polecenie **+ Dodaj** i skopiuj informacje o szablonie. 
5. Wybierz szablon utworzony w kroku #4, a następnie kliknij przycisk **Wdróż**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Tworzenie szablonu z istniejących zasobów platformy Azure w portalu Azure
Jeśli masz już eksperymenty maszyny Azure konta dostępne w [portalu Azure](https://portal.azure.com), można wygenerować szablonu z tego zasobu. 

1. Przejdź do konta Azure eksperymenty w [portalu Azure](https://portal.azure.com).
2. W obszarze **ustawienia**, kliknij **skryptu automatyzacji**.
3. Pobierz szablon. 

Alternatywnie można ręcznie edytować pliki szablonu. Zobacz następujące przykład szablonu i parametrów pliki. 

### <a name="template-file-example"></a>Przykładowy plik szablonu
Utwórz plik o nazwie "szablonu file.json" z poniżej zawartości. 

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the machine learning experimentation team account"
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location of the machine learning experimentation account and other dependent resources."
            }
        },
        "storageAccountSku": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "metadata": {
                "description": "Sku of the storage account"
            }
        }
    },
    "variables": {
        "mlexpVersion": "2017-05-01-preview",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
    },
    "resources": [
        {
            "name": "[parameters('accountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2016-12-01",
            "tags": {
                "mlteamAccount": "[parameters('accountName')]"
            },
            "sku": {
                "name": "[parameters('storageAccountSku')]"
            },
            "kind": "Storage",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            }
        },
        {
            "apiVersion": "[variables('mlexpVersion')]",
            "type": "Microsoft.MachineLearningExperimentation/accounts",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
            ],
            "properties": {
                "storageAccount": {
                    "storageAccountId": "[variables('stgResourceId')]",
                    "accessKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName')), '2016-12-01').keys[0].value]"
                }
            }
        }
    ]
}
```

### <a name="parameters"></a>Parametry 
Utwórz plik o poniżej zawartości i zapisz go jako < parameters.JSON następującym kodem >. 

Istnieją trzy wartości, które można zmienić. 
* Nazwa konta: Nazwa konta eksperymenty.
* Lokalizacja: Jeden z obsługiwanych regionów platformy Azure.
* Konto magazynu SKU: Azure ML obsługuje tylko magazynu w warstwie standardowa, nie w warstwie premium. Aby uzyskać więcej informacji na temat magazynowania, zobacz [wprowadzenie magazynu](https://docs.microsoft.com/azure/storage/common/storage-introduction). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "accountName": {
         "value": "MyExperimentationAccount"
     },
     "location": {
         "value": "eastus2"
     },
     "storageAccountSku": {
         "value": "Standard_LRS"
     }
  }
}
```

## <a name="next-steps"></a>Kolejne kroki
* [Utwórz i zainstaluj usługi Azure Machine Learning](../service/quickstart-installation.md)
