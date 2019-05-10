---
title: Automatyzacja dodawania użytkownika laboratorium w usłudze Azure DevTest Labs | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zautomatyzować Dodawanie użytkownika laboratorium do laboratorium Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 0eed874d405fcf99241a702292f8ceadae6c5a07
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65502025"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatyzacja dodawania użytkownika laboratorium do laboratorium Azure DevTest Labs
Usługa Azure DevTest Labs umożliwia szybkie tworzenie środowisk dewelopersko testowe samoobsługowego przy użyciu witryny Azure portal. Jednak jeśli masz kilka zespołów i kilka wystąpień usługi DevTest Labs, Automatyzowanie procesu tworzenia zaoszczędzić czas. [Szablony usługi Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) umożliwiają tworzenie laboratoriów, laboratorium, maszyny wirtualne, niestandardowe obrazy, formuły i dodawanie użytkowników w zautomatyzowany sposób. Ten artykuł skupia się na temat dodawania użytkowników do wystąpienia usługi DevTest Labs.

Aby dodać użytkownika do laboratorium, należy dodać użytkownika do **użytkownik usługi DevTest Labs** roli dla laboratorium. W tym artykule pokazano, jak zautomatyzować Dodawanie użytkownika do laboratorium przy użyciu jednej z następujących sposobów:

- Szablony usługi Azure Resource Manager
- Polecenia cmdlet programu Azure PowerShell 
- Interfejs wiersza polecenia platformy Azure.

## <a name="use-azure-resource-manager-templates"></a>Korzystanie z szablonów usługi Azure Resource Manager
Poniższy przykładowy szablon usługi Resource Manager Określa użytkownika mają zostać dodane do **użytkownik usługi DevTest Labs** Rola laboratorium. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Jeśli masz przypisanie roli w tym samym szablonie, który jest utworzenie laboratorium, pamiętaj, aby dodać zależność od zasobu przypisania roli i laboratorium. Aby uzyskać więcej informacji, zobacz [Definiowanie zależności w szablonach usługi Resource Manager platformy Azure](/azure-resource-manager/resource-group-define-dependencies.md) artykułu.

### <a name="role-assignment-resource-information"></a>Informacje o zasobach przypisania roli
Zasób przypisania roli należy określić typ i nazwę.

Pierwszą rzeczą, należy pamiętać, jest, że nie jest typem zasobu `Microsoft.Authorization/roleAssignments` tak jak grupy zasobów.  Zamiast tego typu zasobu jest zgodny ze wzorcem `{provider-namespace}/{resource-type}/providers/roleAssignments`. W tym przypadku będzie typ zasobu `Microsoft.DevTestLab/labs/providers/roleAssignments`.

Sama nazwa przypisania roli musi być globalnie unikatowa.  Nazwa przypisania używa wzorca `{labName}/Microsoft.Authorization/{newGuid}`. `newGuid` Jest wartość parametru szablonu. Zapewnia, że nazwa przypisania roli jest unikatowa. Ponieważ nie ma żadnych funkcji szablonu do tworzenia identyfikatorów GUID, należy wygenerować identyfikatora GUID, samodzielnie przy użyciu dowolnego narzędzia generator GUID.  

W szablonie, nazwa przypisania roli jest zdefiniowana `fullDevTestLabUserRoleName` zmiennej. Dokładny wiersz z szablonu jest:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Właściwości zasobów przypisania roli
Przypisanie roli, sama definiuje trzy właściwości. Potrzebuje `roleDefinitionId`, `principalId`, i `scope`.

### <a name="role-definition"></a>Definicja roli
Identyfikator definicji roli jest identyfikator ciągu dla istniejącej definicji roli. Rola identyfikator znajduje się w formularzu `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`. 

Identyfikatora subskrypcji przy użyciu `subscription().subscriptionId` funkcji szablonu.  

Musisz pobrać definicji roli `DevTest Labs User` wbudowana rola. Aby uzyskać identyfikator GUID dla [użytkownik usługi DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) roli, można użyć [interfejsu API REST przypisania roli](/rest/api/authorization/roleassignments) lub [Get AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) polecenia cmdlet.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

Identyfikator roli jest zdefiniowane w sekcji zmiennych i nosi nazwę `devTestLabUserRoleId`. W szablonie identyfikator roli jest ustawiony na: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Identyfikator podmiotu zabezpieczeń
Identyfikator podmiotu zabezpieczeń jest identyfikator obiektu użytkownika usługi Active Directory, grupy lub jednostki usługi, który chcesz dodać jako użytkownika laboratorium do laboratorium. Szablon używa `ObjectId` jako parametr.

Identyfikator obiektu można uzyskać za pomocą [Get AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup lub [Get AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) poleceń cmdlet programu PowerShell. Te polecenia cmdlet zwraca jednego środowiska lub listę obiektów usługi Active Directory, które mają właściwość ID, który jest identyfikator obiektu, które są potrzebne. Poniższy przykład pokazuje, jak uzyskać identyfikator obiektu jeden użytkownik w firmie.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Możesz również użyć poleceń cmdlet programu PowerShell usługi Azure Active Directory, które obejmują [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0), i [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Scope
Zakres Określa zasób lub grupa zasobów, dla którego stosuje się przypisanie roli. W przypadku zasobów zakres ma postać: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`. Szablon używa `subscription().subscriptionId` funkcję, aby wypełnić `subscription-id` części i `resourceGroup().name` funkcji szablonu, aby wypełnić `resource-group-name` części. Korzystanie z tych funkcji oznacza, że laboratorium, do którego jest przypisanie roli musi istnieć w bieżącej subskrypcji i tej samej grupie zasobów, do której wykonano wdrożenie szablonu. Ostatnia część `resource-name`, nazywa się laboratorium. Ta wartość jest odbierany przez parametr szablonu, w tym przykładzie. 

Zakres roli w szablonie: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Wdrażanie szablonu
Najpierw należy utworzyć plik parametrów (na przykład: azuredeploy.parameters.json) który przekazuje wartości parametrów w szablonie usługi Resource Manager. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Następnie należy użyć [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) polecenia cmdlet programu PowerShell do wdrożenia szablonu usługi Resource Manager. Następujące przykładowe polecenie przypisuje osoby, grupy lub jednostki usługi rola użytkownik usługi DevTest Labs w środowisku laboratoryjnym.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Należy pamiętać, grupy wdrożenia nazwy i przypisanie roli identyfikatora GUID musi być unikatowa. Jeśli spróbujesz wdrożyć przydziału zasobu z identyfikatorem GUID nie jest unikatowa, a następnie otrzymasz `RoleAssignmentUpdateNotPermitted` błędu.

Jeśli planujesz użyć szablonu kilka razy, można dodać kilka obiektów usługi Active Directory do roli Użytkownik usługi DevTest Labs dla swojego laboratorium, należy wziąć pod uwagę przy użyciu obiektów dynamicznych polecenia programu PowerShell. W poniższym przykładzie użyto [nowy Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) polecenia cmdlet, aby określić dynamicznie zasobu wdrożenia nazwa i rola przypisania grupy identyfikator GUID.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell
Jak wspomniano we wprowadzeniu, utworzyć nowe przypisanie roli platformy Azure można dodać użytkownika do **użytkownik usługi DevTest Labs** roli dla laboratorium. W programie PowerShell, możesz zrobić za pomocą [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) polecenia cmdlet. To polecenie cmdlet ma wiele parametrów opcjonalnych, aby umożliwić elastyczne. `ObjectId`, `SigninName`, Lub `ServicePrincipalName` może być określony jako obiekt udzielenia uprawnień.  

Oto przykład polecenia programu Azure PowerShell dodaje użytkownika do roli Użytkownik usługi DevTest Labs w określonym środowisku laboratoryjnym.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Aby określić, zasobów, do której są uprawnienia przyznane można określić przy użyciu kombinacji `ResourceName`, `ResourceType`, `ResourceGroup` lub `scope` parametru. Niezależnie od kombinacji parametrów jest używany, zawierać informacje wystarczające do polecenia cmdlet do unikatowego identyfikowania obiektu usługi Active Directory (użytkownika, grupy lub jednostki usługi), zakres (grupy zasobów lub zasobu) i definicji roli.

## <a name="use-azure-command-line-interface-cli"></a>Użyj interfejsu wiersza polecenia platformy Azure (CLI)
W interfejsie wiersza polecenia platformy Azure, Dodawanie użytkownika labs do laboratorium jest wykonywane przy użyciu `az role assignment create` polecenia. Aby uzyskać więcej informacji na temat poleceń cmdlet narzędzia wiersza polecenia platformy Azure, zobacz [zarządzanie dostępem do zasobów platformy Azure przy użyciu RBAC i wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md).

Obiekt, który jest udzielany jest dostęp, może zostać określony przez `objectId`, `signInName`, `spn` parametrów. Laboratorium, do której obiekt jest zostanie im przyznany dostęp można zidentyfikować przez `scope` adres url lub jako kombinację `resource-name`, `resource-type`, i `resource-group` parametrów.

Poniższy przykład wiersza polecenia platformy Azure pokazuje, jak dodać osoby do roli Użytkownik usługi DevTest Labs w określonym środowisku laboratoryjnym.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły:

- [Tworzenie i zarządzanie maszynami wirtualnymi za pomocą usługi DevTest Labs przy użyciu wiersza polecenia platformy Azure](devtest-lab-vmcli.md)
- [Utwórz maszynę wirtualną z usługą DevTest Labs przy użyciu programu Azure PowerShell](devtest-lab-vm-powershell.md)
- [Użyj narzędzia wiersza polecenia do uruchamiania i zatrzymywania maszyn wirtualnych w usłudze Azure DevTest Labs](use-command-line-start-stop-virtual-machines.md)

