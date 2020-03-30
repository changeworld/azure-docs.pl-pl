---
title: Udzielanie uprawnień użytkowników do określonych zasad laboratoryjnych | Dokumenty firmy Microsoft
description: Dowiedz się, jak przyznać uprawnienia użytkowników do określonych zasad laboratoryjnych w DevTest Labs na podstawie potrzeb każdego użytkownika
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
ms.date: 10/07/2019
ms.author: spelluru
ms.openlocfilehash: 9b31f3e68fbabc32f301fdcd8066a3bfbf1c2dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284215"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>Udzielanie uprawnień użytkowników do określonych zasad laboratoryjnych
## <a name="overview"></a>Omówienie
W tym artykule pokazano, jak używać programu PowerShell do udzielania użytkownikom uprawnień do określonych zasad laboratoryjnych. W ten sposób uprawnienia mogą być stosowane na podstawie potrzeb każdego użytkownika. Na przykład można przyznać określonemu użytkownikowi możliwość zmiany ustawień zasad maszyny Wirtualnej, ale nie zasad kosztów.

## <a name="policies-as-resources"></a>Zasady jako zasoby
Jak omówiono w artykule [Kontrola dostępu oparta na roli platformy Azure,](../role-based-access-control/role-assignments-portal.md) rbac umożliwia szczegółowe zarządzanie dostępem zasobów dla platformy Azure. Za pomocą RBAC, można segregować obowiązki w zespole DevOps i udzielić tylko ilość dostępu do użytkowników, których potrzebują do wykonywania swoich zadań.

W laboratoriach devtest zasad jest typem zasobu, który umożliwia akcję RBAC **Microsoft.DevTestLab/labs/policySets/policies/**. Każda zasada laboratorium jest zasobem typu zasobów zasad i może być przypisana jako zakres do roli RBAC.

Na przykład w celu przyznania użytkownikom uprawnień do odczytu/zapisu do zasad **Dozwolonych rozmiarów maszyn wirtualnych,** należy utworzyć rolę niestandardową, która współpracuje z **microsoft.DevTestLab/labs/policySets/policies/action,** a następnie przypisać odpowiednich użytkowników do tej roli niestandardowej w zakresie **Microsoft.DevTestLab/labs/policySets/policies/AllowedVmSizesInLab**.

Aby dowiedzieć się więcej o rolach niestandardowych w rbac, zobacz [kontroli dostępu ról niestandardowych](../role-based-access-control/custom-roles.md).

## <a name="creating-a-lab-custom-role-using-powershell"></a>Tworzenie roli niestandardowej laboratorium przy użyciu programu PowerShell
Aby rozpocząć, musisz zainstalować program [Azure PowerShell](/powershell/azure/install-az-ps). 

Po skonfigurowaniu poleceń cmdlet programu Azure PowerShell można wykonać następujące zadania:

* Wyświetlanie listy wszystkich operacji/akcji dostawcy zasobów
* Lista akcji w określonej roli:
* Tworzenie roli niestandardowej

Poniższy skrypt programu PowerShell ilustruje przykłady wykonywania tych zadań:

    # List all the operations/actions for a resource provider.
    Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

    # List actions in a particular role.
    (Get-AzRoleDefinition "DevTest Labs User").Actions

    # Create custom role.
    $policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "Policy Contributor"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
    $policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>Przypisywanie uprawnień do użytkownika dla określonych zasad przy użyciu ról niestandardowych
Po zdefiniowaniu ról niestandardowych można przypisać je do użytkowników. Aby przypisać rolę niestandardową do użytkownika, należy najpierw uzyskać **ObjectId** reprezentujący tego użytkownika. Aby to zrobić, należy użyć polecenia cmdlet **Get-AzADUser.**

W poniższym przykładzie **ObjectId** użytkownika *SomeUser* jest 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3.

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

Po uzyskaniu **identyfikatora objectid** dla użytkownika i niestandardowej nazwy roli można przypisać tę rolę do użytkownika za pomocą polecenia cmdlet **New-AzRoleAssignment:**

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

W poprzednim przykładzie używana jest zasada **AllowedVmSizesInLab.** Możesz skorzystać z dowolnej z następujących schołek:

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown (LabVmsShutdown)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
Po przyznaniu uprawnień użytkownika do określonych zasad laboratoryjnych, oto kilka następnych kroków, które należy wziąć pod uwagę:

* [Zabezpieczanie dostępu do laboratoriów](devtest-lab-add-devtest-user.md)
* [Ustawianie zasad laboratoryjnych](devtest-lab-set-lab-policy.md)
* [Tworzenie szablonu laboratorium](devtest-lab-create-template.md)
* [Tworzenie niestandardowych artefaktów dla maszyn wirtualnych](devtest-lab-artifact-author.md)
* [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)

