---
title: 'Azure Active Directory Domain Services: Zakres synchronizacji | Dokumentacja firmy Microsoft'
description: Konfigurowanie zakresu synchronizacji z usługi Azure AD do domeny zarządzanej
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: ac11244b87c87285722b4922da69530fab98c299
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416487"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Konfigurowanie zakresu synchronizacji z usługi Azure AD do domeny zarządzanej
W tym artykule pokazano, jak skonfigurować tylko określone konta użytkowników mają być synchronizowane z katalogiem usługi Azure AD do domeny zarządzanej usług domenowych Azure AD.


## <a name="group-based-scoped-synchronization"></a>Oparte na grupach zakresu synchronizacji
Domyślnie wszyscy użytkownicy i grupy w katalogu usługi Azure AD są synchronizowane z domeną zarządzaną. Jeśli tylko kilku użytkowników używać domeny zarządzanej, może synchronizować tych kont użytkowników. Oparte na grupach objętych zakresem synchronizacji można to zrobić. Po skonfigurowaniu tylko kont użytkowników należących do grup, które zostało podane są synchronizowane do domeny zarządzanej.

Poniższa tabela pozwala określić, jak używać synchronizacji o określonym zakresie:

| **Bieżący stan** | **Żądany stan** | **Wymaganej konfiguracji** |
| --- | --- | --- |
| Istniejąca domena zarządzana jest skonfigurowany do synchronizacji wszystkich kont użytkowników i grup. | Chcesz synchronizować tylko kont użytkowników należących do określonej grupy do domeny zarządzanej. | [Usuń istniejąca domena zarządzana](active-directory-ds-disable-aadds.md). Następnie postępuj zgodnie z instrukcjami w tym artykule, aby utworzyć ją ponownie przy użyciu zakresu synchronizacji skonfigurowane. |
| Nie masz istniejącej domeny zarządzanej. | Chcesz utworzyć nową domenę zarządzanych i zsynchronizować tylko kont użytkowników należących do określonych grup. | Postępuj zgodnie z instrukcjami w tym artykule do utworzenia nowej domeny zarządzanej przy użyciu zakresu synchronizacji skonfigurowane. |
| Istniejąca domena zarządzana jest skonfigurowany do synchronizacji tylko dla kont należących do określonych grup. | Chcesz zmodyfikować listę grup użytkowników, którzy mają być synchronizowane do domeny zarządzania. | Postępuj zgodnie z instrukcjami w tym artykule w celu zmodyfikowania synchronizacji o określonym zakresie. |

> [!WARNING]
> **Zmiana zakresu synchronizacji powoduje, że Twojej domeny zarządzanej za pośrednictwem ponownej synchronizacji.**
> 
>  * Zmiana zakresu synchronizacji dla domeny zarządzanej, występuje pełnej ponownej synchronizacji.
>  * Obiekty, które nie są już wymagane w domenie zarządzanej zostaną usunięte. Nowe obiekty są tworzone w domenie zarządzanej.
>  * Ponownej synchronizacji może zająć dużo czasu, w zależności od liczby obiektów (użytkowników, grup i członkostw w grupie) w domenie zarządzanej i w katalogu usługi Azure AD. W przypadku dużych katalogów przy użyciu wielu setek tysięcy obiektów podczas ponownej synchronizacji może potrwać kilka dni.


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Tworzenie nowej domeny zarządzanej i włączanie grupy synchronizacji opartej na zakresie przy użyciu witryny Azure portal

1. Postępuj zgodnie z [Wprowadzenie — przewodnik](active-directory-ds-getting-started.md) można utworzyć domeny zarządzanej.
2. Wybierz **zakresie** podczas wyboru stylu synchronizacji w kreatorze tworzenia usług domenowych Azure AD.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>Tworzenie nowej domeny zarządzanej i włączanie grupy synchronizacji opartej na zakresie przy użyciu programu PowerShell
Aby ukończyć ten zestaw kroków za pomocą programu PowerShell. Zapoznaj się z instrukcjami, aby [włączyć usługi Azure Active Directory Domain Services przy użyciu programu PowerShell](active-directory-ds-enable-using-powershell.md). Kilka kroków opisanych w tym artykule są nieco modyfikowane Konfigurowanie synchronizacji o określonym zakresie.

Wykonaj poniższe kroki, aby skonfigurować synchronizację o określonym zakresie oparte na grupach, do domeny zarządzanej:

1. Wykonaj następujące czynności:
   * [Zadanie 1: Zainstaluj wymagane moduły programu PowerShell](active-directory-ds-enable-using-powershell.md#task-1-install-the-required-powershell-modules).
   * [Zadanie 2. Tworzenie jednostki usługi wymagane w katalogu usługi Azure AD](active-directory-ds-enable-using-powershell.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory).
   * [Zadanie 3. Tworzenie i Konfigurowanie grupy "Administratorzy usługi AAD DC"](active-directory-ds-enable-using-powershell.md#task-3-create-and-configure-the-aad-dc-administrators-group).
   * [Zadanie 4. Zarejestruj dostawcę zasobów usługi Azure AD Domain Services](active-directory-ds-enable-using-powershell.md#task-4-register-the-azure-ad-domain-services-resource-provider).
   * [Zadanie 5. Utwórz grupę zasobów](active-directory-ds-enable-using-powershell.md#task-5-create-a-resource-group).
   * [Zadanie 6. Tworzenie i konfigurowanie sieci wirtualnej](active-directory-ds-enable-using-powershell.md#task-6-create-and-configure-the-virtual-network).

2. Wybierz grupy, które chcesz synchronizować, a następnie podaj nazwę wyświetlaną grup, które chcesz zsynchronizować z domeną zarządzaną.

3. Zapisz [skryptu w poniższej sekcji](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) w pliku o nazwie ```Select-GroupsToSync.ps1```. Uruchom skrypt, takich jak poniżej:

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **Pamiętaj uwzględnić grupy "Administratorzy usługi AAD DC".**
   >
   > Na liście skonfigurowane do synchronizacji zakresu grupy, musi zawierać grupy "Administratorzy usługi AAD DC". Jeśli ta grupa nie zostanie uwzględniony, domeny zarządzanej, będzie można jej używać.
   >

4. Teraz Tworzenie domeny zarządzanej i włączanie na podstawie grupy synchronizacji o określonym zakresie dla domeny zarządzanej. Zawiera właściwości ```"filteredSync" = "Enabled"``` w ```Properties``` parametru. Zobacz na przykład poniższy fragment skryptu skopiowane z [zadanie 7: Aprowizowanie domeny zarządzanej usług domenowych Azure AD](active-directory-ds-enable-using-powershell.md#task-7-provision-the-azure-ad-domain-services-managed-domain).

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso100.com"
   $ResourceGroupName = "ContosoAaddsRg"
   $VnetName = "DomainServicesVNet_WUS"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -ApiVersion 2017-06-01 -Force -Verbose
   ```

   > [!TIP]
   > Nie zapomnij dołączyć ```"filteredSync" = "Enabled"``` w ```-Properties``` parametru, więc o określonym zakresie synchronizacja jest włączona dla domeny zarządzanej.


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
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```


## <a name="modify-group-based-scoped-synchronization"></a>Modyfikowanie oparte na grupach zakresu synchronizacji
Aby zmodyfikować listę grup użytkowników, którzy mają być synchronizowane z domeną zarządzaną, uruchom ponownie [skrypt programu PowerShell](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) i określ nową listę grup. Pamiętaj, aby zawsze określać grupy "Administratorzy usługi AAD DC" na tej liście.

> [!WARNING]
> **Pamiętaj uwzględnić grupy "Administratorzy usługi AAD DC".**
>
> Na liście skonfigurowane do synchronizacji zakresu grupy, musi zawierać grupy "Administratorzy usługi AAD DC". Jeśli ta grupa nie zostanie uwzględniony, domeny zarządzanej, będzie można jej używać.
>


## <a name="disable-group-based-scoped-synchronization"></a>Wyłącz synchronizację o określonym zakresie oparte na grupach
Użyj następującego skryptu programu PowerShell, można wyłączyć, oparte na grupach zakresu synchronizacji dla domeny zarządzanej:

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>Kolejne kroki
* [Omówienie synchronizacji w usługach domenowych Azure AD](active-directory-ds-synchronization.md)
* [Włączanie usługi Azure Active Directory Domain Services przy użyciu programu PowerShell](active-directory-ds-enable-using-powershell.md)
