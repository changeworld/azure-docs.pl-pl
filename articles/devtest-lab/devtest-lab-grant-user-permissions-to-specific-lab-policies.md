---
title: Przyznawanie uprawnień użytkownikom laboratorium określonych zasad | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można udzielić użytkownikowi uprawnień do zasad określonych laboratorium w usłudze DevTest Labs na podstawie potrzeb każdego użytkownika
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: v-craic
ms.openlocfilehash: 2e42d371e1f5244b61dc30823db43fefe549b00d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Przyznawanie uprawnień użytkownikom zasad określonych laboratorium
## <a name="overview"></a>Przegląd
W tym artykule przedstawiono sposób udzielić uprawnień użytkowników do zasad laboratorium określonego za pomocą programu PowerShell. W ten sposób uprawnienia mogą być stosowane zgodnie z potrzebami każdego użytkownika. Na przykład można przyznać określonemu użytkownikowi możliwość zmiany ustawień zasad maszyny Wirtualnej, ale nie zasady kosztów.

## <a name="policies-as-resources"></a>Zasady jako zasoby
Zgodnie z opisem w [kontroli dostępu opartej na roli Azure](../role-based-access-control/role-assignments-portal.md) artykułu, RBAC umożliwia precyzyjne zarządzanie dostępem zasobów platformy Azure. Przy użyciu funkcji RBAC, można rozdzielenie obowiązków w obrębie organizacji DevOps i udzielić tylko takiego dostępu dla użytkowników, które są niezbędne do wykonywania swoich zadań.

W usłudze DevTest Labs zasady jest typ zasobu, który umożliwia działanie RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Każda zasada laboratorium jest zasobem w typie zasobów zasad i można przypisać zasięgu roli RBAC.

Na przykład, aby przyznać użytkownikom uprawnienia odczytu/zapisu do **dozwolone rozmiary maszyn wirtualnych** zasad, należy utworzyć niestandardową rolę, która współdziała z **Microsoft.DevTestLab/labs/policySets/policies/*** akcji i Przypisz tę rolę niestandardową, w zakresie odpowiednich użytkowników **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Aby dowiedzieć się więcej na temat ról niestandardowych w RBAC, zobacz [niestandardowych ról dla kontroli dostępu](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Tworzenie laboratorium niestandardowej roli zabezpieczeń przy użyciu programu PowerShell
Aby rozpocząć pracę, należy przeczytać artykuł następujące, które wyjaśniają, jak zainstalować i skonfigurować poleceń cmdlet programu PowerShell usługi Azure: [ https://azure.microsoft.com/blog/azps-1-0-pre ](https://azure.microsoft.com/blog/azps-1-0-pre).

Po skonfigurowaniu poleceń cmdlet programu Azure PowerShell, można wykonywać następujące zadania:

* Lista wszystkich operacji/akcje dla dostawcy zasobów
* Akcje listy z określoną rolą:
* Tworzenie niestandardowej roli zabezpieczeń

Poniższy skrypt programu PowerShell przedstawiono przykłady sposobu wykonywania następujących zadań:

    ‘List all the operations/actions for a resource provider.
    Get-AzureRmProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzureRmRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Przypisywanie uprawnień do użytkownika dla określonych zasad przy użyciu ról niestandardowych
Po zdefiniowaniu poszczególnych ról niestandardowych, można przypisać je do użytkowników. Aby można było przypisać niestandardową rolę dla użytkownika, należy najpierw uzyskać **ObjectId** reprezentujący użytkownika. Aby to zrobić, użyj **Get-AzureRmADUser** polecenia cmdlet.

W poniższym przykładzie **ObjectId** z *SomeUser* 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 jest użytkownik.

    PS C:\>Get-AzureRmADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Po utworzeniu **ObjectId** dla użytkownika i nazwy niestandardowej roli zabezpieczeń, można przypisać tej roli użytkownika z **AzureRmRoleAssignment nowy** polecenia cmdlet:

    PS C:\>New-AzureRmRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

W poprzednim przykładzie **AllowedVmSizesInLab** zasady są używane. Można użyć dowolnego z następujących zasad:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
Raz użytkownikowi nie zostały przyznane użytkownikowi uprawnień do laboratorium określone zasady, poniżej przedstawiono niektóre warto rozważyć poniższe kroki:

* [Zabezpieczanie dostępu do laboratoriów](devtest-lab-add-devtest-user.md)
* [Ustawianie zasad laboratorium](devtest-lab-set-lab-policy.md)
* [Tworzenie szablonu laboratorium](devtest-lab-create-template.md)
* [Tworzenie niestandardowych artefaktów dla maszyn wirtualnych](devtest-lab-artifact-author.md)
* [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)

