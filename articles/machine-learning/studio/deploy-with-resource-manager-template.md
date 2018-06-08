---
title: Wdrażanie obszaru roboczego usługi Machine Learning z usługą Azure Resource Manager | Dokumentacja firmy Microsoft
description: Jak wdrożyć obszaru roboczego dla usługi Azure Machine Learning przy użyciu szablonu usługi Azure Resource Manager
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 4955ac4d-ff99-4908-aa27-69b6bfcc8e85
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/05/2018
ms.openlocfilehash: 82d2316b3f72fbb0c5c3ee1ea9424afcc7661361
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833975"
---
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Wdrażanie obszaru roboczego usługi Machine Learning przy użyciu usługi Azure Resource Manager
## <a name="introduction"></a>Wprowadzenie
Za pomocą usługi Azure Resource Manager Szablon wdrożenia zapisuje czas, zapewniając skalowalne sposób wdrażania składników połączonych ze sprawdzaniem poprawności, a następnie spróbuj ponownie mechanizmu. Aby konfigurowanie obszarów roboczych do programu Azure Machine Learning, na przykład, należy najpierw skonfigurować konto usługi Azure storage, a następnie wdrożyć obszaru roboczego. Wyobraź sobie zrobić to ręcznie setek obszarów roboczych. Łatwiejsze zamiast jest szablon usługi Azure Resource Manager umożliwia wdrażanie obszar roboczy usługi Azure Machine Learning i jego zależności. W tym artykule przeprowadza użytkownika przez proces ten krok. Aby uzyskać wszechstronne Omówienie usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Krok po kroku: tworzenie obszaru roboczego Machine Learning
Firma Microsoft będzie utworzyć grupy zasobów platformy Azure, a następnie wdrożyć nowe konto magazynu Azure i nowy Azure Machine Learning obszar roboczy za pomocą szablonu usługi Resource Manager. Po zakończeniu wdrażania, firma Microsoft będzie drukować ważne informacje na temat obszarów roboczych, które zostały utworzone (klucz podstawowy, workspaceID i adres URL do obszaru roboczego).

### <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu usługi Azure Resource Manager
Obszaru roboczego uczenia maszyny wymaga konta magazynu Azure do przechowywania zestawu danych z nim połączone.
Następujący szablon użyje nazwy grupy zasobów można wygenerować nazwy konta magazynu i nazwa obszaru roboczego.  Ponadto użyto nazwy konta magazynu jako właściwość podczas tworzenia obszaru roboczego.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Zapisz ten szablon jako plik mlworkspace.json pod c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Wdrożenie grupy zasobów, na podstawie szablonu
* Otwórz program PowerShell.
* Instalowanie modułów dla usługi Azure Resource Manager i zarządzania usługą Azure  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Te kroki, Pobierz i zainstaluj moduły, które należy wykonać pozostałe etapy. To tylko musi odbywać się raz w środowisku, w którym są wykonywane poleceń programu PowerShell.   

* Uwierzytelnianie na platformie Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Connect-AzureRmAccount
```
Ten krok należy powtórzyć dla każdej sesji. Po uwierzytelnieniu powinna być wyświetlana informacji o subskrypcji.

![Azure Account][1]

Teraz, gdy będziemy mieć dostęp do platformy Azure, można utworzyć grupy zasobów.

* Tworzenie grupy zasobów

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Upewnij się, że grupa zasobów jest poprawnie zainicjowane. **ProvisioningState** powinien być "powiodło się."
Nazwa grupy zasobów jest używany przez szablon można wygenerować nazwy konta magazynu. Nazwa konta magazynu musi należeć do zakresu od 3 do 24 znaków i korzystać tylko cyfry i małe litery.

![Grupa zasobów][2]

* Przy użyciu wdrożenia grupy zasobów, Wdróż nowy obszar roboczy Machine Learning.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Po zakończeniu wdrożenia jest prosta do dostępu do właściwości obszaru roboczego wdrożone. Na przykład możesz uzyskać dostęp podstawowego tokenu klucza.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Innym sposobem na pobranie tokenów z istniejącym obszarem roboczym jest Użyj polecenia Invoke-AzureRmResourceAction. Na przykład można wyświetlić listę głównych i dodatkowych tokenów wszystkich obszarów roboczych.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Po udostępnieniu obszaru roboczego można również zautomatyzować wiele zadań Azure Machine Learning Studio za pomocą [modułu programu PowerShell dla usługi Azure Machine Learning](http://aka.ms/amlps).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [authoring Azure Resource Manager szablony](../../azure-resource-manager/resource-group-authoring-templates.md). 
* Obejrzyj [repozytorium szablonów Szybki Start Azure](https://github.com/Azure/azure-quickstart-templates). 
* Obejrzyj ten film o [usługi Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 

<!--Image references-->
[1]: ./media/deploy-with-resource-manager-template/azuresubscription.png
[2]: ./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
