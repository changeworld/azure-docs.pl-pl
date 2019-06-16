---
title: Udzielanie uprawnień użytkownika do określonych laboratorium zasad | Dokumentacja firmy Microsoft
description: Dowiedz się, jak udzielić użytkownikowi uprawnień do zasad określonych laboratorium w usłudze DevTest Labs, odpowiednio do potrzeb każdego użytkownika
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 70469a9e8737a9df18628951a061c97081c74080
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127382"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Udzielanie uprawnień użytkownika do zasad określonych laboratorium
## <a name="overview"></a>Omówienie
W tym artykule pokazano, jak udzielić uprawnień użytkowników do zasad wymagających używania określonego laboratorium przy użyciu programu PowerShell. Dzięki temu uprawnienia można zastosować zależnie od potrzeb każdego użytkownika. Na przykład można przyznać określonego użytkownika, możliwość zmiany ustawień zasad maszyny Wirtualnej, ale nie zasady kosztów.

## <a name="policies-as-resources"></a>Zasady jako zasoby
Zgodnie z opisem w [kontroli dostępu opartej na roli Azure](../role-based-access-control/role-assignments-portal.md) artykułu, RBAC umożliwia precyzyjne zarządzanie dostępem zasobów na platformie Azure. Przy użyciu funkcji RBAC, można segregować obowiązki w obrębie zespołu DevOps i udzielać uprawnień dostępu do użytkowników, które są im niezbędne do wykonywania swoich zadań.

W usłudze DevTest Labs zasady jest typ zasobu, który umożliwia działanie RBAC **Microsoft.DevTestLab/labs/policySets/policies/** . Wszystkie zasady laboratorium jest zasobem w typie zasób zasad i mogą być przypisane jako zakres do roli RBAC.

Na przykład, aby przyznać użytkownikom uprawnienia odczytu/zapisu do **dozwolone rozmiary maszyn wirtualnych** zasady, należy utworzyć rolę niestandardową, która współdziała z **Microsoft.DevTestLab/labs/policySets/policies/** akcji , a następnie przypisz odpowiednich użytkowników do tej roli niestandardowej w zakresie **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Aby dowiedzieć się więcej na temat ról niestandardowych w ROLACH, zobacz [kontroli dostępu niestandardowych ról](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Tworzenie roli niestandardowej laboratorium przy użyciu programu PowerShell
Aby rozpocząć pracę, musisz [zainstalować program Azure PowerShell](/powershell/azure/install-az-ps). 

Po skonfigurowaniu poleceń cmdlet programu Azure PowerShell, należy wykonać następujące zadania:

* Lista wszystkich operacji/akcji dla dostawcy zasobów
* Akcje listy z określoną rolą:
* Tworzenie roli niestandardowej

Poniższy skrypt programu PowerShell przedstawia przykładowe sposoby wykonywania następujących zadań:

    ‘List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    ‘List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    ‘Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Przypisywanie uprawnień użytkownika dla określonych zasad za pomocą ról niestandardowych
Po zdefiniowaniu roli niestandardowej można przypisać je do użytkowników. Aby można było przypisać niestandardową rolę do użytkownika, należy najpierw uzyskać **ObjectId** reprezentujący tego użytkownika. Aby to zrobić, należy użyć **Get AzADUser** polecenia cmdlet.

W poniższym przykładzie **ObjectId** z *SomeUser* 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 jest użytkownik.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Po utworzeniu **ObjectId** dla użytkownika oraz nazwa roli niestandardowej można przypisać tę rolę do użytkownika o **New AzRoleAssignment** polecenia cmdlet:

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

W poprzednim przykładzie **AllowedVmSizesInLab** zasady są używane. Możesz użyć dowolnej z następujących zasad:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Kolejne kroki
Po użytkownik zostały przyznane użytkownikowi uprawnień do laboratorium określonych zasad, poniżej przedstawiono kilka następnych kroków, aby wziąć pod uwagę:

* [Zabezpieczanie dostępu do laboratoriów](devtest-lab-add-devtest-user.md)
* [Ustawianie zasad laboratorium](devtest-lab-set-lab-policy.md)
* [Tworzenie szablonu laboratorium](devtest-lab-create-template.md)
* [Tworzenie niestandardowych artefaktów dla maszyn wirtualnych](devtest-lab-artifact-author.md)
* [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)

