---
title: 'Azure Active Directory Domain Services: Synchronizacja w zakresie | Microsoft Docs'
description: Konfigurowanie synchronizacji z zakresem z usługi Azure AD z domenami zarządzanymi
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 7d3bd8c6c62c0b8a1be6203e426337fcee7d2126
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617115"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Konfigurowanie synchronizacji z zakresem z usługi Azure AD do domeny zarządzanej
W tym artykule opisano sposób konfigurowania tylko określonych kont użytkowników, które mają być synchronizowane z katalogu usługi Azure AD do domeny zarządzanej Azure AD Domain Services.


## <a name="group-based-scoped-synchronization"></a>Synchronizacja z zakresem opartym na grupach
Domyślnie wszyscy użytkownicy i grupy w katalogu usługi Azure AD są synchronizowane z domeną zarządzaną. Jeśli tylko kilku użytkowników korzysta z domeny zarządzanej, można synchronizować tylko te konta użytkowników. Synchronizacja z zakresem opartym na grupach umożliwia wykonanie tej czynności. Po skonfigurowaniu konta użytkowników należące do określonych grup są synchronizowane z domeną zarządzaną.

Poniższa tabela ułatwia określenie sposobu korzystania z synchronizacji w zakresie:

| **Bieżący stan** | **Żądany stan** | **Wymagana konfiguracja** |
| --- | --- | --- |
| Istniejąca domena zarządzana jest skonfigurowana do synchronizacji wszystkich kont użytkowników i grup. | Chcesz synchronizować tylko konta użytkowników należące do określonych grup w domenie zarządzanej. | [Usuń istniejącą domenę zarządzaną](delete-aadds.md). Następnie postępuj zgodnie z instrukcjami w tym artykule, aby utworzyć je ponownie z konfiguracją synchronizacji o określonym zakresie. |
| Nie masz istniejącej domeny zarządzanej. | Chcesz utworzyć nową domenę zarządzaną i zsynchronizować tylko konta użytkowników należące do określonych grup. | Postępuj zgodnie z instrukcjami w tym artykule, aby utworzyć nową domenę zarządzaną z skonfigurowaną synchronizacją o określonym zakresie. |
| Istniejąca domena zarządzana jest skonfigurowana tak, aby synchronizować tylko konta należące do określonych grup. | Chcesz zmodyfikować listę grup, których użytkownicy mają być zsynchronizowani z domeną zarządzania. | Postępuj zgodnie z instrukcjami w tym artykule, aby zmodyfikować synchronizację z zakresem. |

> [!WARNING]
> **Zmiana zakresu synchronizacji powoduje, że Twoja domena zarządza przejdzie przez ponowną synchronizację.**
> 
>  * W przypadku zmiany zakresu synchronizacji dla domeny zarządzanej następuje pełna ponowna synchronizacja.
>  * Obiekty, które nie są już wymagane w domenie zarządzanej, są usuwane. Nowe obiekty są tworzone w domenie zarządzanej.
>  * Ponowna synchronizacja może zająć dużo czasu, w zależności od liczby obiektów (użytkowników, grup i członkostwa w grupach) w domenie zarządzanej i w katalogu usługi Azure AD. W przypadku dużych katalogów z wieloma tysiącami obiektów ponowna synchronizacja może potrwać kilka dni.


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Utwórz nową domenę zarządzaną i Włącz synchronizację z zakresem opartym na grupach przy użyciu Azure Portal

1. Postępuj zgodnie z [przewodnikiem wprowadzenie](tutorial-create-instance.md) , aby utworzyć domenę zarządzaną.
2. Wybierz **zakres** podczas wybierania stylu synchronizacji w kreatorze tworzenia Azure AD Domain Services.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>Tworzenie nowej domeny zarządzanej i Włączanie synchronizacji z zakresem opartym na grupach przy użyciu programu PowerShell
Użyj programu PowerShell, aby ukończyć ten zestaw kroków. Zapoznaj się z instrukcjami, aby [włączyć Azure Active Directory Domain Services przy użyciu programu PowerShell](powershell-create-instance.md). Kilka kroków tego artykułu jest nieco nieznacznie modyfikowanych w celu skonfigurowania synchronizacji z zakresem.

Wykonaj następujące kroki, aby skonfigurować synchronizację z zakresem opartym na grupach w domenie zarządzanej:

1. Wykonaj następujące zadania:
   * [Zadanie 1: Zainstaluj wymagane moduły](powershell-create-instance.md#task-1-install-the-required-powershell-modules)programu PowerShell.
   * [Zadanie 2: Utwórz wymaganą jednostkę usługi w katalogu](powershell-create-instance.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory)usługi Azure AD.
   * [Zadanie 3: Utwórz i skonfiguruj grupę "Administratorzy usługi AAD DC"] PowerShell-Create-instance. MD # Task-3-Create-and-configure-the-AAD-DC-Administrators-Group.
   * [Zadanie 4: Zarejestruj dostawcę](powershell-create-instance.md#task-4-register-the-azure-ad-domain-services-resource-provider)zasobów Azure AD Domain Services.
   * [Zadanie 5: Utwórz grupę](powershell-create-instance.md#task-5-create-a-resource-group)zasobów.
   * [Zadanie 6: Utwórz i skonfiguruj sieć](powershell-create-instance.md#task-6-create-and-configure-the-virtual-network)wirtualną.

2. Wybierz grupy, które chcesz synchronizować, i podaj nazwę wyświetlaną grup, które mają być synchronizowane z domeną zarządzaną.

3. Zapisz [skrypt w poniższej sekcji](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) w pliku o nazwie ```Select-GroupsToSync.ps1```. Wykonaj skrypt podobny do poniższego:

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **Nie zapomnij dodać grupy "Administratorzy kontrolera domeny usługi AAD".**
   >
   > Należy uwzględnić grupę "Administratorzy kontrolera domeny usługi AAD" na liście grup skonfigurowanych na potrzeby synchronizacji z zakresem. Jeśli ta grupa nie zostanie uwzględniona, domena zarządzana będzie bezużyteczny.
   >

4. Teraz Utwórz domenę zarządzaną i Włącz synchronizację z zakresem opartym na grupach dla domeny zarządzanej. Dołącz Właściwość ```"filteredSync" = "Enabled"``` ```Properties``` do parametru. Na przykład zapoznaj się z poniższym fragmentem skryptu skopiowanym z [zadania 7: Zainicjuj obsługę administracyjną](powershell-create-instance.md#task-7-provision-the-azure-ad-domain-services-managed-domain)Azure AD Domain Services domenie zarządzanej.

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
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
   > Nie zapomnij dołączyć ```"filteredSync" = "Enabled"``` ```-Properties``` do parametru, więc synchronizacja w zakresie jest włączona dla domeny zarządzanej.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Skrypt służący do wybierania grup do synchronizacji z domeną zarządzaną (Select-GroupsToSync. ps1)
Zapisz następujący skrypt do pliku (```Select-GroupsToSync.ps1```). Ten skrypt służy do konfigurowania Azure AD Domain Services synchronizacji wybranych grup z domeną zarządzaną. Wszystkie konta użytkowników należące do określonych grup zostaną zsynchronizowane z domeną zarządzaną.

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


## <a name="modify-group-based-scoped-synchronization"></a>Modyfikuj synchronizację z zakresem opartym na grupach
Aby zmodyfikować listę grup, których użytkownicy powinni synchronizować z domeną zarządzaną, uruchom ponownie [skrypt programu PowerShell](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) i określ nową listę grup. Pamiętaj, aby zawsze określać grupę "Administratorzy kontrolera domeny usługi AAD" na tej liście.

> [!WARNING]
> **Nie zapomnij dodać grupy "Administratorzy kontrolera domeny usługi AAD".**
>
> Należy uwzględnić grupę "Administratorzy kontrolera domeny usługi AAD" na liście grup skonfigurowanych na potrzeby synchronizacji z zakresem. Jeśli ta grupa nie zostanie uwzględniona, domena zarządzana będzie bezużyteczny.
>


## <a name="disable-group-based-scoped-synchronization"></a>Wyłącz synchronizację z zakresem opartym na grupach
Użyj poniższego skryptu programu PowerShell, aby wyłączyć synchronizację z zakresem opartym na grupach dla domeny zarządzanej:

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>Następne kroki
* [Omówienie synchronizacji w Azure AD Domain Services](synchronization.md)
* [Włączanie Azure Active Directory Domain Services przy użyciu programu PowerShell](powershell-create-instance.md)
