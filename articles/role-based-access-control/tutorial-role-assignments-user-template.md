---
title: Samouczek — udzielanie użytkownikowi dostęp do zasobów platformy Azure przy użyciu szablonu RBAC i usługi Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udzielić użytkownikowi dostępu do zasobów platformy Azure przy użyciu kontroli dostępu opartej na rolach (RBAC) przy użyciu szablonu usługi Azure Resource Manager.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: edb20221862e6439b3bc574995f4037cbc95f8f9
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668886"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Samouczek: Daj użytkownikowi dostęp do zasobów platformy Azure przy użyciu szablonu RBAC i usługi Resource Manager

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](overview.md) to sposób zarządzania dostępem do zasobów platformy Azure. W ramach tego samouczka Utwórz grupę zasobów i udzielić użytkownikowi dostępu do tworzenia i zarządzania maszynami wirtualnymi w tej grupie zasobów. Ten samouczek koncentruje się na proces wdrażania szablonu usługi Resource Manager, aby udzielić dostępu. Aby uzyskać więcej informacji na temat tworzenia szablonów usługi Resource Manager, zobacz [dokumentacji usługi Resource Manager](/azure/azure-resource-manager/) i [odwołanie do szablonu](/azure/templates/microsoft.authorization/allversions
).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Udzielanie dostępu użytkownikowi w zakresie grupy zasobów
> * Weryfikowanie wdrożenia
> * Czyszczenie

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać i usunąć przypisania roli, musisz mieć:

* `Microsoft.Authorization/roleAssignments/write` i `Microsoft.Authorization/roleAssignments/delete` uprawnienia, takie jak [Administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) lub [właściciela](built-in-roles.md#owner)

## <a name="grant-access"></a>Udzielanie dostępu

Szablon używany w tym przewodniku Szybki Start jest z [szablony szybkiego startu platformy](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Azure autoryzacji dotyczące szablonów można znaleźć [tutaj](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Aby wdrożyć szablon, zaznacz **wypróbuj** aby otworzyć usługa Azure Cloud shell, a następnie wklej poniższy skrypt programu PowerShell do okna powłoki. Aby wkleić kod, kliknij prawym przyciskiem myszy w oknie shell, a następnie wybierz pozycję **Wklej**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Otwórz grupę zasobów, utworzony w poprzedniej procedurze. Domyślna nazwa jest nazwą projektu, przy użyciu **rg** dołączane.
1. Z menu po lewej stronie wybierz opcję **Kontrola dostępu (IAM)** .
1. Wybierz **przypisań ról**. 
1. W **nazwa**, wprowadź adres e-mail został wpisany w poprzedniej procedurze. Powinien zostać wyświetlony użytkownik z adresem e-mail ma **Współautor maszyny wirtualnej** roli.

## <a name="clean-up"></a>Czyszczenie

Aby usunąć grupy zasobów utworzonej w poprzedniej procedurze, zaznacz **wypróbuj** aby otworzyć usługa Azure Cloud shell, a następnie wklej poniższy skrypt programu PowerShell do okna powłoki.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Samouczek: Udzielanie użytkownikowi dostępu do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i programu Azure PowerShell](tutorial-role-assignments-user-powershell.md)