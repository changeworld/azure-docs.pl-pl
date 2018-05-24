---
title: Szablon usługi Azure Resource Manager z rozszerzeniem programu VS Code | Microsoft Docs
description: Rozszerzenie Narzędzia usługi Azure Resource Manager służy do pracy z szablonami usługi Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/06/2017
ms.topic: quickstart
ms.author: tomfitz
ms.openlocfilehash: f05b0baee3f11f498976377c69c38b3118f3c922
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358663"
---
# <a name="use-visual-studio-code-extension-to-create-azure-resource-manager-template"></a>Tworzenie szablonów usługi Azure Resource Manager przy użyciu rozszerzenia programu Visual Studio Code
W tym artykule przedstawiono korzyści płynące z zainstalowania i używania rozszerzenia Narzędzia usługi Azure Resource Manager w programie Visual Studio Code. Można tworzyć szablony usługi Resource Manager w programie VS Code bez korzystania z rozszerzenia, ale rozszerzenie udostępnia opcje autouzupełniania, które upraszczają proces tworzenia szablonu. Sugerują one funkcje szablonu, parametry i zmienne, które są dostępne w szablonie.

Aby ukończyć pracę z tym artykułem, potrzebny jest program [Visual Studio Code](https://code.visualstudio.com/).

Aby zrozumieć pojęcia związane z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Usługa Azure Resource Manager — omówienie](resource-group-overview.md).

## <a name="create-the-template"></a>Tworzenie szablonu

Ten artykuł wykorzystuje szablon utworzony w samouczku [Tworzenie i wdrażanie pierwszego szablonu usługi Azure Resource Manager](resource-manager-create-first-template.md). Jeśli masz już ten szablon, możesz pominąć tę sekcję.

1. Jeśli musisz utworzyć szablon, uruchom program VS Code. Wybierz pozycję **Plik** > **Nowy plik**. 

   ![Nowy plik](./media/resource-manager-vscode-extension/new-file.png)

2. Skopiuj i wklej następującą składnię JSON do pliku:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "storageSKU": {
         "type": "string",
         "allowedValues": [
           "Standard_LRS",
           "Standard_ZRS",
           "Standard_GRS",
           "Standard_RAGRS",
           "Premium_LRS"
         ],
         "defaultValue": "Standard_LRS",
         "metadata": {
           "description": "The type of replication to use for the storage account."
         }
       },   
       "storageNamePrefix": {
         "type": "string",
         "maxLength": 11,
         "defaultValue": "storage",
         "metadata": {
           "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
         }
       }
     },
     "variables": {
       "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
     },
     "resources": [
       {
         "name": "[variables('storageName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "[parameters('storageSKU')]"
         },
         "kind": "Storage",
         "location": "[resourceGroup().location]",
         "tags": {},
         "properties": {
           "encryption":{
             "services":{
               "blob":{
                 "enabled":true
               }
             },
             "keySource":"Microsoft.Storage"
           }
         }
       }
     ],
     "outputs": {  }
   }
   ```

3. Zapisz ten plik pod nazwą **azuredeploy.json** w folderze lokalnym.

   ![Zapisywanie szablonu](./media/resource-manager-vscode-extension/save-template.png)

## <a name="install-the-extension"></a>Instalowanie rozszerzenia

1. W programie VS Code wybierz pozycję **Rozszerzenia**.

   ![Wybieranie rozszerzenia](./media/resource-manager-vscode-extension/select-extensions.png)

2. Wyszukaj pozycję **Narzędzia usługi Azure Resource Manager** i wybierz pozycję **Zainstaluj**.

   ![Instalowanie rozszerzenia](./media/resource-manager-vscode-extension/install-extension.png)

3. Aby zakończyć instalację rozszerzenia, wybierz pozycję **Załaduj ponownie**.

## <a name="edit-the-template"></a>Edytowanie szablonu

1. Otwórz plik azuredeploy.json.

2. Rozszerzenie pobiera wszystkie dostępne [funkcje szablonu](resource-group-template-functions.md). Odczytuje również parametry i zmienne, które zostały zdefiniowane w szablonie. Aby wyświetlić tę funkcję, należy dodać dwie wartości do sekcji danych wyjściowych. W szablonie zastąp sekcję danych wyjściowych następującym kodem:

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": ""
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

3. Umieść kursor w cudzysłowach dla wartości w elemencie **groupLocation**. Wpisz lewy nawias kwadratowy (`[`). Zwróć uwagę, że rozszerzenie natychmiast sugeruje dostępne funkcje szablonu.

   ![Wyświetlanie dostępnych funkcji](./media/resource-manager-vscode-extension/available-functions.png)

4. Zacznij wpisywać nazwę **resourceGroup**. Po wyświetleniu funkcji `resourceGroup()` naciśnij klawisz Tab lub Enter.

   ![Wybieranie funkcji resourceGroup](./media/resource-manager-vscode-extension/show-resourcegroup.png)

5. Rozszerzenie wypełnia składnię funkcji. Funkcja [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) nie akceptuje parametrów. Dodaj kropkę po prawym nawiasie okrągłym. Rozszerzenie udostępnia właściwości, które są dostępne dla obiektu zwróconego przez funkcję `resourceGroup()`. Wybierz pozycję `location`.

   ![Wybieranie właściwości](./media/resource-manager-vscode-extension/resourcegroup-properties.png)

6. Po elemencie **location** dodaj prawy nawias zamykający.

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": ""
       }
   }
   ```

7. Teraz umieść kursor wewnątrz cudzysłowów dla elementu **storageUri**. Ponownie wpisz lewy nawias kwadratowy. Zacznij wpisywać nazwę **reference**. Po zaznaczeniu tej funkcji naciśnij klawisz Tab lub Enter.

   ![Wybieranie funkcji reference](./media/resource-manager-vscode-extension/add-reference.png)

8. Funkcja [reference](resource-group-template-functions-resource.md#reference) akceptuje identyfikator zasobu lub nazwę zasobu jako parametr. Masz już nazwę konta magazynu w zmiennej. Wpisz **var**, a następnie wybierz kombinację klawiszy Ctrl+spacja. Rozszerzenie sugeruje funkcję variables.

   ![Wybieranie zmiennej](./media/resource-manager-vscode-extension/add-variable.png)

   Naciśnij klawisz Tab lub Enter.

9. Funkcja [variables](resource-group-template-functions-deployment.md#variables) wymaga nazwy zmiennej. Wewnątrz nawiasów dodaj znak pojedynczego cudzysłowu. Rozszerzenie udostępnia nazwy zmiennych zdefiniowanych w szablonie.

   ![Wyświetlanie zmiennych](./media/resource-manager-vscode-extension/show-variables.png) 

10. Wybierz zmienną **storageName**. Dodaj prawy nawias kwadratowy. Poniższy przykład przedstawia sekcję danych wyjściowych:

   ```json
   "outputs": { 
       "groupLocation": {
         "type": "string",
         "value": "[resourceGroup().location]"
       },
       "storageUri": {
         "type": "string",
         "value": "[reference(concat('Microsoft.Storage/storageAccounts/',variables('storageName'))).primaryEndpoints.blob]"
       }
   }
   ```

Ostateczny szablon wygląda tak:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "encryption":{
          "services":{
            "blob":{
              "enabled":true
            }
          },
          "keySource":"Microsoft.Storage"
        }
      }
    }
  ],
  "outputs": { 
    "groupLocation": {
      "type": "string",
      "value": "[resourceGroup().location]"
    },
    "storageUri": {
      "type": "string",
      "value": "[reference(concat('Microsoft.Storage/storageAccounts/',variables('storageName'))).primaryEndpoints.blob]"
    }
  }
}
```

## <a name="deploy-template"></a>Wdrażanie szablonu

Wszystko jest teraz gotowe do wdrożenia tego szablonu. Użyjesz programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby utworzyć grupę zasobów. Następnie wdrożysz konto magazynu w tej grupie zasobów.

* W przypadku programu PowerShell użyj następujących poleceń z poziomu folderu zawierającego szablon:

   ```powershell
   Connect-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* W przypadku lokalnej instalacji interfejsu wiersza polecenia platformy Azure użyj następujących poleceń z poziomu folderu zawierającego szablon:

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

Po zakończeniu wdrożenia zwracane są wartości danych wyjściowych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

W przypadku programu PowerShell użyj polecenia:

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji o strukturze szablonu, zobacz [Tworzenie szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).
* Aby poznać właściwości konta magazynu, zobacz [dokumentację szablonu kont magazynu](/azure/templates/microsoft.storage/storageaccounts).
* Aby wyświetlić pełną listę szablonów dla wielu różnych rozwiązań, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/).
