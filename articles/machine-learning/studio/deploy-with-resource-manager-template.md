---
title: Wdrażanie obszaru roboczego Studio (klasycznego) za pomocą usługi Azure Resource Manager
titleSuffix: ML Studio (classic) - Azure
description: Jak wdrożyć obszar roboczy dla usługi Azure Machine Learning Studio (klasyczny) przy użyciu szablonu usługi Azure Resource Manager
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: 34333d4fe6e9b34a0c8b56cca8123f4ed93a917a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218114"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Wdrażanie obszaru roboczego usługi Azure Machine Learning Studio (klasycznego) przy użyciu usługi Azure Resource Manager

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Za pomocą szablonu wdrażania usługi Azure Resource Manager oszczędza czas, zapewniając skalowalny sposób wdrażania połączonych składników z mechanizmem sprawdzania poprawności i ponawiania próby. Aby skonfigurować usługi Azure Machine Learning Studio (klasyczne) obszary robocze, na przykład, należy najpierw skonfigurować konto magazynu platformy Azure, a następnie wdrożyć obszar roboczy. Wyobraź sobie, że robisz to ręcznie dla setek obszarów roboczych. Łatwiejszą alternatywą jest użycie szablonu usługi Azure Resource Manager do wdrożenia obszaru roboczego Studio (klasycznego) i wszystkich jego zależności. W tym artykule przechodzi przez ten proces krok po kroku. Aby uzyskać doskonałe omówienie usługi Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Krok po kroku: tworzenie obszaru roboczego uczenia maszynowego
Utworzymy grupę zasobów platformy Azure, a następnie wdrożymy nowe konto magazynu platformy Azure i nowy obszar roboczy usługi Azure Machine Learning Studio (klasyczny) przy użyciu szablonu Menedżera zasobów. Po zakończeniu wdrażania wydrukujemy ważne informacje o utworzonych obszarach roboczych (klucz podstawowy, adres roboczy i adres URL do obszaru roboczego).

### <a name="create-an-azure-resource-manager-template"></a>Tworzenie szablonu usługi Azure Resource Manager

Obszar roboczy uczenia maszynowego wymaga konta magazynu platformy Azure do przechowywania zestawu danych z nim połączonego.
Poniższy szablon używa nazwy grupy zasobów do wygenerowania nazwy konta magazynu i nazwy obszaru roboczego.  Używa również nazwy konta magazynu jako właściwości podczas tworzenia obszaru roboczego.

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
Zapisz ten szablon jako plik mlworkspace.json w obszarze c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Wdrażanie grupy zasobów na podstawie szablonu

* Otwórz program PowerShell.
* Instalowanie modułów dla usługi Azure Resource Manager i usługi Azure Service Management

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   Te kroki pobrać i zainstalować moduły niezbędne do wykonania pozostałych kroków. Należy to zrobić tylko raz w środowisku, w którym wykonujesz polecenia programu PowerShell.

* Uwierzytelnij się na platformie Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Ten krok musi być powtórzony dla każdej sesji. Po uwierzytelnieniu powinny być wyświetlane informacje o subskrypcji.

![Konto platformy Azure](./media/deploy-with-resource-manager-template/azuresubscription.png)

Teraz, gdy mamy dostęp do platformy Azure, możemy utworzyć grupę zasobów.

* Tworzenie grupy zasobów

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Sprawdź, czy grupa zasobów jest poprawnie aprowizowana. **Aprowizuj obsługi administracyjnejStan** powinien być "Pomyślnie".
Nazwa grupy zasobów jest używana przez szablon do generowania nazwy konta magazynu. Nazwa konta magazynu musi mieć długość od 3 do 24 znaków i używać tylko cyfr i liter.

![Grupa zasobów](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Korzystając z wdrożenia grupy zasobów, wdrożyć nowy obszar roboczy uczenia maszynowego.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Po zakończeniu wdrażania dostęp do właściwości wdrożonego obszaru roboczego jest łatwy do uzyskania dostępu. Na przykład można uzyskać dostęp do tokenu klucza podstawowego.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Innym sposobem pobrania tokenów istniejącego obszaru roboczego jest użycie polecenia Invoke-AzResourceAction. Na przykład można wyświetlić listę podstawowych i pomocniczych tokenów wszystkich obszarów roboczych.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Po aprowizowaniu obszaru roboczego można również zautomatyzować wiele zadań usługi Azure Machine Learning Studio (klasycznych) przy użyciu [modułu programu PowerShell dla usługi Azure Machine Learning Studio (klasycznego).](https://aka.ms/amlps)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [tworzenie szablonów usługi Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
* Zapoznaj się z [repozytorium szablonów szybki start platformy Azure](https://github.com/Azure/azure-quickstart-templates).
* Obejrzyj ten film o [usłudze Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Zobacz [pomoc dotyczącą szablonu Menedżera zasobów](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->
