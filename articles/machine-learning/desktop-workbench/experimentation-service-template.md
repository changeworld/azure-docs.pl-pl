---
title: Tworzenie eksperymentowanie w usłudze Machine Learning platformy Azure przy użyciu szablonu usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono przykład tworzenia konta eksperymentowanie w usłudze Machine Learning platformy Azure przy użyciu szablonu usługi Azure Resource Manager.
services: machine-learning
author: hning86
ms.author: haining
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/14/2017
ms.openlocfilehash: 7938eaa0e06c9a33034a7388d02845d60967774e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "42057092"
---
# <a name="configure-the-azure-machine-learning-experimentation-service"></a>Konfigurowanie usługi eksperymentowanie w usłudze Azure Machine Learning

## <a name="overview"></a>Przegląd
Konto usługi eksperymentowanie w usłudze Machine Learning platformy Azure, obszaru roboczego i projekt są zasobami platformy Azure. W efekcie można je wdrażać przy użyciu szablonów Menedżera zasobów. Szablony usługi Resource Manager są plikami JSON definiującymi zasoby, które należy wdrożyć dla danego rozwiązania. Aby zrozumieć pojęcia związane z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Usługa Azure Resource Manager — omówienie](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="deploy-a-template"></a>Wdrażanie szablonu
Wdrażanie szablonu wymaga tylko kilku kroków w interfejsu wiersza polecenia platformy Azure lub w witrynie Azure portal.

### <a name="deploy-a-template-from-the-command-line"></a>Wdróż szablon z poziomu wiersza polecenia
Przy użyciu interfejsu wiersza polecenia, pojedyncze polecenie szablon można wdrożyć do istniejącej grupy zasobów.
Zawierają następujące tematy, aby uzyskać informacje dotyczące tworzenia szablonu.

```sh
# Login and validate your are in the right subscription context
az login

# Create a new resource group (you can use an existing one)
az group create --name <resource group name> --location <supported Azure region>
az group deployment create -n testdeploy -g <resource group name> --template-file <template-file.json> --parameters <parameters.json>
```

### <a name="deploy-a-template-from-the-azure-portal"></a>Wdróż szablon z witryny Azure portal
Jeśli wolisz, umożliwia także witryny Azure portal do tworzenia i wdrażania szablonu. Wykonaj następujące czynności:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** i poszukaj pozycji "templates".
3. Wybierz **szablony**.
4. Kliknij pozycję **+ Dodaj** i skopiować informacje o szablonie. 
5. Wybierz szablon utworzony w kroku #4, a następnie kliknij przycisk **Wdróż**.


## <a name="create-a-template-from-an-existing-azure-resource-in-the-azure-portal"></a>Tworzenie szablonu z istniejącego zasobu platformy Azure w witrynie Azure portal
Jeśli masz już eksperymentowanie w usłudze Azure Machine konta dostępne w [witryny Azure portal](https://portal.azure.com), można wygenerować szablonu z tego zasobu. 

1. Przejdź do konta eksperymentowania w usłudze Azure w [witryny Azure portal](https://portal.azure.com).
2. W obszarze **ustawienia**, kliknij pozycję **skrypt automatyzacji**.
3. Pobierz szablon. 

Alternatywnie można ręcznie edytować pliki szablonów. Zobacz następujące przykładowy szablon i parametry pliki. 

### <a name="template-file-example"></a>Przykładowy plik szablonu
Utwórz plik o nazwie "szablon file.json" z poniżej zawartości. 

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
Utwórz plik o poniżej zawartości i zapisz go jako < parameters.json >. 

Istnieją trzy wartości, które mogą być zmieniane. 
* Nazwa konta: Nazwa konta eksperymentowania.
* Lokalizacja: Jeden z obsługiwanych regionów platformy Azure.
* Jednostki SKU konta magazynu: Uczenie Maszynowe Azure obsługuje tylko magazynu w warstwie standardowa, premium nie. Aby uzyskać więcej informacji na temat magazynu zobacz [wprowadzenie magazynu](https://docs.microsoft.com/azure/storage/common/storage-introduction). 

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
* [Tworzenie i instalowanie usługi Azure Machine Learning](../service/quickstart-installation.md)
