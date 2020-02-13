---
title: Wdróż obszar roboczy programu Studio (klasyczny) z Azure Resource Manager
titleSuffix: ML Studio (classic) - Azure
description: Jak wdrożyć obszar roboczy dla Azure Machine Learning Studio (klasyczny) przy użyciu szablonu Azure Resource Manager
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: f05ef9472f11a5025e9856cfb207cc0859f24c3e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169365"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Wdróż obszar roboczy Azure Machine Learning Studio (klasyczny) przy użyciu Azure Resource Manager

Za pomocą usługi Azure Resource Manager Szablon wdrożenia zapisuje czas, oferując skalowalne sposób wdrażania składników połączonych ze sobą za pomocą weryfikacji i mechanizm ponawiania prób. Aby skonfigurować obszary robocze Azure Machine Learning Studio (klasyczne), na przykład należy najpierw skonfigurować konto usługi Azure Storage, a następnie wdrożyć obszar roboczy. Wyobraź sobie zrobić to ręcznie setek obszarów roboczych. Łatwiejszym rozwiązaniem jest użycie szablonu Azure Resource Manager do wdrożenia obszaru roboczego programu Studio (klasycznego) i jego wszystkich zależności. Ten artykuł przeprowadzi Cię przez ten proces krok po kroku. Aby uzyskać więcej informacji na temat Azure Resource Manager, zobacz [Azure Resource Manager omówienie](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Krok po kroku: tworzenie obszaru roboczego uczenia maszynowego
Utworzymy grupę zasobów platformy Azure, a następnie wdrożono nowe konto usługi Azure Storage i nowy obszar roboczy Azure Machine Learning Studio (klasyczny) przy użyciu szablonu Menedżer zasobów. Po zakończeniu wdrażania możemy zostanie wydrukowana ważne informacje na temat obszarów roboczych, które zostały utworzone (klucz podstawowy, identyfikator workspaceID i adres URL do obszaru roboczego).

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
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Następujące kroki, Pobierz i zainstaluj moduły, które należy wykonać pozostałe kroki. To tylko musi odbywać się jeden raz w środowisku, w którym są wykonywane polecenia programu PowerShell.

* Uwierzytelnianie na platformie Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Ten krok należy powtórzyć dla każdej sesji. Po uwierzytelnieniu powinny być wyświetlane informacje o subskrypcji.

![Azure Account](./media/deploy-with-resource-manager-template/azuresubscription.png)

Skoro mamy już dostęp do platformy Azure, możemy utworzyć grupę zasobów.

* Utwórz grupę zasobów

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Upewnij się, że grupa zasobów jest prawidłowo aprowizowane. **ProvisioningState** powinna mieć wartość "powodzenie".
Nazwa grupy zasobów jest używany przez szablon do generowania nazwy konta magazynu. Nazwa konta magazynu musi mieć długość od 3 do 24 znaków długości i użyć tylko cyfry i małe litery.

![Grupa zasobów](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Przy użyciu wdrożenia grupy zasobów, wdrażanie nowego obszaru roboczego uczenia maszynowego.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Po ukończeniu wdrażania jest bardzo proste przejdź do właściwości obszaru roboczego, który został wdrożony. Na przykład możesz uzyskać dostęp podstawowego klucza tokenu.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Innym sposobem pobierania tokenów istniejącego obszaru roboczego jest użycie polecenia Invoke-AzResourceAction. Na przykład można wyświetlić listę podstawowych i pomocniczych tokenów wszystkich obszarów roboczych.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
Po aprowizacji obszaru roboczego można także zautomatyzować wiele zadań Azure Machine Learning Studio (klasycznych) przy użyciu [modułu programu PowerShell dla Azure Machine Learning Studio (klasyczny)](https://aka.ms/amlps).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat [tworzenia szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
* Zapoznaj się z [repozytorium szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates).
* Obejrzyj to wideo dotyczące [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Zobacz [Pomoc dotyczącą szablonu Menedżer zasobów](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->
