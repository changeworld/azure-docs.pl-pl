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
ms.openlocfilehash: fc19c77e90ffd2d300497fc496da02b2d2234d3a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244393"
---
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Wdrażanie obszaru roboczego usługi Machine Learning przy użyciu usługi Azure Resource Manager
## <a name="introduction"></a>Wprowadzenie
Za pomocą usługi Azure Resource Manager Szablon wdrożenia zapisuje czas, oferując skalowalne sposób wdrażania składników połączonych ze sobą za pomocą weryfikacji i mechanizm ponawiania prób. Aby skonfigurować obszarów roboczych uczenia maszynowego Azure, na przykład, należy najpierw skonfigurować konto magazynu platformy Azure, a następnie wdrożyć obszaru roboczego. Wyobraź sobie zrobić to ręcznie setek obszarów roboczych. Alternatywą łatwiej jest używać szablonu usługi Azure Resource Manager do wdrożenia obszaru roboczego usługi Azure Machine Learning i wszystkich jego zależności. Ten artykuł przeprowadzi Cię przez ten proces krok po kroku. Aby uzyskać doskonałe Omówienie usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Krok po kroku: tworzenie obszaru roboczego uczenia maszynowego
Firma Microsoft będzie utworzyć grupę zasobów platformy Azure, a następnie wdrożyć nowe konto usługi Azure storage i nowego Azure obszaru roboczego uczenia maszynowego przy użyciu szablonu usługi Resource Manager. Po zakończeniu wdrażania możemy zostanie wydrukowana ważne informacje na temat obszarów roboczych, które zostały utworzone (klucz podstawowy, identyfikator workspaceID i adres URL do obszaru roboczego).

### <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu usługi Azure Resource Manager
Obszaru roboczego uczenia maszynowego wymaga konta usługi Azure storage do przechowywania zestawu danych, połączone z tym rekordem.
Następujący szablon używa nazwy grupy zasobów, aby wygenerować nazwę konta magazynu i nazwa obszaru roboczego.  Korzysta również nazwę konta magazynu jako właściwość podczas tworzenia obszaru roboczego.

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

### <a name="deploy-the-resource-group-based-on-the-template"></a>Wdrażanie grupy zasobów, na podstawie szablonu
* Otwórz program PowerShell.
* Zainstaluj moduły dla usługi Azure Resource Manager i usługi Azure Service Management  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Następujące kroki, Pobierz i zainstaluj moduły, które należy wykonać pozostałe kroki. To tylko musi odbywać się jeden raz w środowisku, w którym są wykonywane polecenia programu PowerShell.   

* Uwierzytelnianie na platformie Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Connect-AzureRmAccount
```
Ten krok należy powtórzyć dla każdej sesji. Po uwierzytelnieniu powinny być wyświetlane informacje o subskrypcji.

![Azure Account][1]

Skoro mamy już dostęp do platformy Azure, możemy utworzyć grupę zasobów.

* Tworzenie grupy zasobów

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Upewnij się, że grupa zasobów jest prawidłowo aprowizowane. **ProvisioningState** powinien być "powiodło się."
Nazwa grupy zasobów jest używany przez szablon do generowania nazwy konta magazynu. Nazwa konta magazynu musi mieć długość od 3 do 24 znaków długości i użyć tylko cyfry i małe litery.

![Grupa zasobów][2]

* Przy użyciu wdrożenia grupy zasobów, wdrażanie nowego obszaru roboczego uczenia maszynowego.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Po ukończeniu wdrażania jest bardzo proste przejdź do właściwości obszaru roboczego, który został wdrożony. Na przykład możesz uzyskać dostęp podstawowego klucza tokenu.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Innym sposobem pobierania tokenów istniejący obszar roboczy jest korzystania z polecenia Invoke-AzureRmResourceAction. Na przykład można wyświetlić listę podstawowych i pomocniczych tokenów wszystkich obszarów roboczych.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Po zaaprowizowaniu obszaru roboczego, możesz też zautomatyzować wiele zadań usługi Azure Machine Learning Studio, za pomocą [modułu programu PowerShell dla usługi Azure Machine Learning](https://aka.ms/amlps).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [tworzenia szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 
* Zapoznaj się z [repozytorium szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates). 
* Obejrzyj ten film wideo o [usługi Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 

<!--Image references-->
[1]: ./media/deploy-with-resource-manager-template/azuresubscription.png
[2]: ./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
