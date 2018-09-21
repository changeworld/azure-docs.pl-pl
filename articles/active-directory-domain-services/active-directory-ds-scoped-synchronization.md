---
title: 'Usługa Azure Active Directory Domain Services: Zakres synchronizacji | Dokumentacja firmy Microsoft'
description: Konfigurowanie zakresu synchronizacji z usługi Azure AD do domeny zarządzanej
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2018
ms.author: maheshu
ms.openlocfilehash: 1f7e07880c22ec6975f93d8349343986716b9445
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/20/2018
ms.locfileid: "46499253"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Konfigurowanie zakresu synchronizacji z usługi Azure AD do domeny zarządzanej
W tym artykule pokazano, jak skonfigurować tylko określone konta użytkowników mają być synchronizowane z katalogiem usługi Azure AD do domeny zarządzanej usług domenowych Azure AD.


## <a name="group-based-scoped-synchronization"></a>Oparte na grupach zakresu synchronizacji
Domyślnie wszyscy użytkownicy i grupy w katalogu usługi Azure AD są synchronizowane z domeną zarządzaną. Jeśli domena zarządzana jest używany tylko przez kilku użytkowników, można zsynchronizować tylko konta użytkowników do domeny zarządzanej. Oparte na grupach objętych zakresem synchronizacji można to zrobić. Po skonfigurowaniu tylko kont użytkowników należących do grup, które zostało podane są synchronizowane do domeny zarządzanej.


## <a name="get-started-install-the-required-powershell-modules"></a>Wprowadzenie: zainstalować wymagane moduły programu PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalowanie i konfigurowanie programu Azure AD PowerShell
Postępuj zgodnie z instrukcjami w artykule, aby [Instalowanie modułu Azure AD PowerShell i łączenie z usługą Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalowanie i konfigurowanie programu Azure PowerShell
Postępuj zgodnie z instrukcjami w artykule, aby [Instalowanie modułu Azure PowerShell i nawiązać połączenie z subskrypcją platformy Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).



## <a name="enable-group-based-scoped-synchronization"></a>Włącz synchronizację o określonym zakresie oparte na grupach
Wykonaj poniższe kroki, aby skonfigurować synchronizację o określonym zakresie oparte na grupach, do domeny zarządzanej:

1. Wybierz grupy, które chcesz synchronizować, a następnie podaj nazwę wyświetlaną grup, które chcesz zsynchronizować z domeną zarządzaną.

2. Zapisz skrypt w poniższej sekcji w pliku o nazwie ```Select-GroupsToSync.ps1```. Uruchom skrypt, takich jak poniżej:

  ```powershell
  .\Select-GroupsToSync.ps1 -groupsToAdd @(“GroupName1”, “GroupName2”)
  ```

3. Teraz Włącz oparte na grupach zakresu synchronizacji dla domeny zarządzanej.

  ```powershell
  // Login to your Azure AD tenant
  Login-AzureRmAccount

  // Retrieve the Azure AD Domain Services resource.
  $DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

  // Enable group-based scoped synchronization.
  $enableScopedSync = @{"filteredSync" = "Enabled"}

  Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
  ```

## <a name="disable-group-based-scoped-synchronization"></a>Wyłącz synchronizację o określonym zakresie oparte na grupach
Użyj następującego skryptu programu PowerShell, można wyłączyć, oparte na grupach zakresu synchronizacji dla domeny zarządzanej:

```powershell
// Login to your Azure AD tenant
Login-AzureRmAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzureRmResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzureRmResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Skrypt, aby wybrać grupy, które mają być synchronizowane domena zarządzana (Wybierz GroupsToSync.ps1)
Zapisz poniższy skrypt do pliku (```Select-GroupsToSync.ps1```). Ten skrypt umożliwia skonfigurowanie usług domenowych Azure AD, aby zsynchronizować wybrane grupy do domeny zarządzanej. Wszystkie konta użytkowników należące do określonej grupy zostaną zsynchronizowane z domeny zarządzanej.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occured assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie synchronizacji w usługach domenowych Azure AD](active-directory-ds-synchronization.md)
