---
title: Wdrażanie obszar roboczy Studio za pomocą usługi Azure Resource Manager
titleSuffix: Azure Machine Learning Studio
description: Jak wdrożyć obszaru roboczego usługi Azure Machine Learning Studio przy użyciu szablonu usługi Azure Resource Manager
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: 83ae58e4a86d3bc2ffb2197f48d2c641790e8524
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648286"
---
# <a name="deploy-azure-machine-learning-studio-workspace-using-azure-resource-manager"></a>Wdrażanie usługi Azure Machine Learning Studio obszaru roboczego przy użyciu usługi Azure Resource Manager

Za pomocą usługi Azure Resource Manager Szablon wdrożenia zapisuje czas, oferując skalowalne sposób wdrażania składników połączonych ze sobą za pomocą weryfikacji i mechanizm ponawiania prób. Aby skonfigurować usługi Azure Machine Learning Studio w obszarach roboczych, na przykład, należy najpierw skonfigurować konto magazynu platformy Azure, a następnie wdrożyć obszaru roboczego. Wyobraź sobie zrobić to ręcznie setek obszarów roboczych. Alternatywą łatwiej jest używać szablonu usługi Azure Resource Manager do wdrażania obszar roboczy Studio i wszystkich jego zależności. Ten artykuł przeprowadzi Cię przez ten proces krok po kroku. Aby uzyskać doskonałe Omówienie usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Krok po kroku: tworzenie obszaru roboczego uczenia maszynowego
Firma Microsoft będzie utworzyć grupę zasobów platformy Azure, a następnie wdrożyć nowe konto usługi Azure storage i nowego Azure Studio obszaru roboczego uczenia maszynowego przy użyciu szablonu usługi Resource Manager. Po zakończeniu wdrażania możemy zostanie wydrukowana ważne informacje na temat obszarów roboczych, które zostały utworzone (klucz podstawowy, identyfikator workspaceID i adres URL do obszaru roboczego).

### <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu usługi Azure Resource Manager

Obszaru roboczego uczenia maszynowego wymaga konta usługi Azure storage do przechowywania zestawu danych, połączone z tym rekordem.
Następujący szablon używa nazwy grupy zasobów, aby wygenerować nazwę konta magazynu i nazwa obszaru roboczego.  Korzysta również nazwę konta magazynu jako właściwość podczas tworzenia obszaru roboczego.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Następujące kroki, Pobierz i zainstaluj moduły, które należy wykonać pozostałe kroki. To tylko musi odbywać się jeden raz w środowisku, w którym są wykonywane polecenia programu PowerShell.

* Uwierzytelnianie na platformie Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzureRmAccount
```
Ten krok należy powtórzyć dla każdej sesji. Po uwierzytelnieniu powinny być wyświetlane informacje o subskrypcji.

![Azure Account](./media/deploy-with-resource-manager-template/azuresubscription.png)

Skoro mamy już dostęp do platformy Azure, możemy utworzyć grupę zasobów.

* Tworzenie grupy zasobów

```powershell
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Upewnij się, że grupa zasobów jest prawidłowo aprowizowane. **ProvisioningState** powinien być "powiodło się."
Nazwa grupy zasobów jest używany przez szablon do generowania nazwy konta magazynu. Nazwa konta magazynu musi mieć długość od 3 do 24 znaków długości i użyć tylko cyfry i małe litery.

![Grupa zasobów](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Przy użyciu wdrożenia grupy zasobów, wdrażanie nowego obszaru roboczego uczenia maszynowego.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Po ukończeniu wdrażania jest bardzo proste przejdź do właściwości obszaru roboczego, który został wdrożony. Na przykład możesz uzyskać dostęp podstawowego klucza tokenu.

```powershell
# Access Azure Machine Learning studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Innym sposobem pobierania tokenów istniejący obszar roboczy jest korzystania z polecenia Invoke-AzureRmResourceAction. Na przykład można wyświetlić listę podstawowych i pomocniczych tokenów wszystkich obszarów roboczych.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Po zaaprowizowaniu obszaru roboczego, możesz też zautomatyzować wiele zadań usługi Azure Machine Learning Studio, za pomocą [modułu programu PowerShell dla usługi Azure Machine Learning Studio](https://aka.ms/amlps).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [tworzenia szablonów usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
* Zapoznaj się z [repozytorium szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates).
* Obejrzyj ten film wideo o [usługi Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Zobacz [pomoc odwołanie do szablonu usługi Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->
