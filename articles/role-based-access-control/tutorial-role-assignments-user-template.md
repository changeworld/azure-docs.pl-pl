---
title: 'Samouczek: Udzielanie użytkownikom dostępu do zasobów platformy Azure za pomocą szablonu RBAC i Resource Manager'
description: Dowiedz się, jak udzielić użytkownikowi dostępu do zasobów platformy Azure przy użyciu kontroli dostępu opartej na rolach (RBAC) przy użyciu szablonu usługi Azure Resource Manager w tym samouczku.
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
ms.openlocfilehash: 96ca4f65d2def5f5004388c533410f09cc2a71fa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77138217"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Samouczek: Udzielanie użytkownikowi dostępu do zasobów platformy Azure przy użyciu szablonu RBAC i Resource Manager

[Kontrola dostępu oparta na rolach (RBAC, Role Based Access Control)](overview.md) to sposób zarządzania dostępem do zasobów platformy Azure. W tym samouczku utworzysz grupę zasobów i udzielisz użytkownikowi dostępu do tworzenia maszyn wirtualnych w grupie zasobów i zarządzania nimi. Ten samouczek koncentruje się na procesie wdrażania szablonu Menedżera zasobów w celu udzielenia dostępu. Aby uzyskać więcej informacji na temat tworzenia szablonów Menedżera zasobów, zobacz [Dokumentacja Menedżera zasobów](/azure/azure-resource-manager/) i odwołanie do [szablonu](/azure/templates/microsoft.authorization/allversions
).

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Udzielanie dostępu użytkownikowi w zakresie grupy zasobów
> * Weryfikowanie wdrożenia
> * Czyszczenie

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać i usunąć przypisania ról, musisz mieć:

* `Microsoft.Authorization/roleAssignments/write`i `Microsoft.Authorization/roleAssignments/delete` uprawnień, takich jak [Administrator dostępu do użytkownika](built-in-roles.md#user-access-administrator) lub [Właściciel](built-in-roles.md#owner)

## <a name="grant-access"></a>Udzielanie dostępu

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Więcej szablonów związanych z autoryzacją platformy Azure można znaleźć [tutaj](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Aby wdrożyć szablon, wybierz pozycję **Spróbuj,** aby otworzyć powłokę usługi Azure Cloud, a następnie wklej następujący skrypt programu PowerShell do okna powłoki. Aby wkleić kod, kliknij prawym przyciskiem myszy okno powłoki, a następnie wybierz polecenie **Wklej**.

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

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Otwórz grupę zasobów utworzoną w ostatniej procedurze. Domyślna nazwa to nazwa projektu z **dodatkiem rg.**
1. Z menu po lewej stronie wybierz opcję **Kontrola dostępu (IAM)**.
1. Wybierz pozycję **Przypisania ról**. 
1. W **obszarze Nazwa**wprowadź adres e-mail wpisany w ostatniej procedurze. Użytkownik z adresem e-mail ma rolę **współautora maszyny wirtualnej.**

## <a name="clean-up"></a>Czyszczenie

Aby usunąć grupę zasobów utworzoną w ostatniej procedurze, wybierz pozycję **Wypróbuj,** aby otworzyć powłokę usługi Azure Cloud, a następnie wklej następujący skrypt programu PowerShell do okna powłoki.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Udzielanie użytkownikowi dostępu do zasobów platformy Azure przy użyciu funkcji RBAC i programu Azure PowerShell](tutorial-role-assignments-user-powershell.md)