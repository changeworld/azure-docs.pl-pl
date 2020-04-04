---
title: Synchronizacja o określonym zakresie dla usług domenowych usługi Azure AD | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować synchronizację o określonym zakresie z usługi Azure AD na domenę zarządzaną usług domenowych usługi active directory platformy Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 9ef7e14cc2a290cc5583e3e599e278f98882152c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654735"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Konfigurowanie synchronizacji o określonym zakresie z usługi Azure AD na usługi domenowe Usługi domenowe Active Directory platformy Azure

Aby świadczyć usługi uwierzytelniania, usługi domenowe Active Directory azure (Azure AD DS) synchronizują użytkowników i grupy z usługi Azure AD. W środowisku hybrydowym użytkownicy i grupy z lokalnego środowiska usług domenowych Active Directory (AD DS) mogą być najpierw synchronizowane z usługą Azure AD przy użyciu usługi Azure AD Connect, a następnie synchronizowane z usługą Azure AD DS.

Domyślnie wszyscy użytkownicy i grupy z katalogu usługi Azure AD są synchronizowane z domeną zarządzaną usługą Azure AD DS. Jeśli masz określone potrzeby, możesz zamiast tego zsynchronizować tylko zdefiniowany zestaw użytkowników.

W tym artykule pokazano, jak utworzyć domenę zarządzaną usług Azure AD DS, która używa synchronizacji o określonym zakresie, a następnie zmienić lub wyłączyć zestaw użytkowników o określonym zakresie.

## <a name="scoped-synchronization-overview"></a>Omówienie synchronizacji o określonym zakresie

Domyślnie wszyscy użytkownicy i grupy z katalogu usługi Azure AD są synchronizowane z domeną zarządzaną usługą Azure AD DS. Jeśli tylko niewielu użytkowników musi uzyskać dostęp do domeny zarządzanej, można synchronizować tylko te konta użytkowników. Ta synchronizacja o zakresie jest oparta na grupach. Podczas konfigurowania synchronizacji o zakresie opartym na grupach tylko konta użytkowników, które należą do określonych grup, są synchronizowane z domeną zarządzaną usługą Azure AD DS.

W poniższej tabeli przedstawiono sposób korzystania z synchronizacji o określonym zakresie:

| Bieżący stan | Żądany stan | Wymagana konfiguracja |
| --- | --- | --- |
| Istniejąca domena zarządzana jest skonfigurowana do synchronizowania wszystkich kont użytkowników i grup. | Chcesz zsynchronizować tylko konta użytkowników, które należą do określonych grup. | Nie można zmienić z synchronizacji wszystkich użytkowników do przy użyciu synchronizacji o określonym zakresie. [Usuń istniejącą domenę zarządzaną](delete-aadds.md), a następnie wykonaj kroki opisane w tym artykule, aby ponownie utworzyć domenę zarządzaną usługą Azure AD DS ze skonfigurowaną synchronizacją o określonym zakresie. |
| Brak istniejącej domeny zarządzanej. | Chcesz utworzyć nową domenę zarządzaną i zsynchronizować tylko konta użytkowników należących do określonych grup. | Wykonaj kroki opisane w tym artykule, aby utworzyć domenę zarządzaną usługą Azure AD DS ze skonfigurowaną synchronizacją o określonym zakresie. |
| Istniejąca domena zarządzana jest skonfigurowana do synchronizowania tylko kont należących do określonych grup. | Chcesz zmodyfikować listę grup, których użytkownicy powinni być synchronizowani z domeną zarządza zarządzana usługą Azure AD DS. | Wykonaj kroki opisane w tym artykule, aby zmodyfikować synchronizację o określonym zakresie. |

Za pomocą portalu Azure lub programu PowerShell można skonfigurować ustawienia synchronizacji o określonym zakresie:

| Akcja | | |
|--|--|--|
| Tworzenie domeny zarządzanej usług Azure AD DS i konfigurowanie synchronizacji o określonym zakresie | [Azure Portal](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Modyfikowanie synchronizacji o określonym zakresie | [Azure Portal](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Wyłączanie synchronizacji o określonym zakresie | [Azure Portal](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Zmiana zakresu synchronizacji powoduje, że domena zarządzana usługą Azure AD DS ponownie zsynchronizuje wszystkie dane. Obowiązują następujące zastrzeżenia:
> 
>  * Po zmianie zakresu synchronizacji dla domeny zarządzanej usług Azure AD DS występuje pełna ponowna synchronizacja.
>  * Obiekty, które nie są już wymagane w domenie zarządzanej usług Azure AD DS są usuwane. Nowe obiekty są tworzone w domenie zarządzanej.
>  * Ponowna synchronizacja może zająć dużo czasu. Czas synchronizacji zależy od liczby obiektów, takich jak użytkownicy, grupy i członkostwa w domenie zarządzanej usług Azure AD DS i katalogu usługi Azure AD. W przypadku dużych katalogów z setkami tysięcy obiektów ponowna synchronizacja może potrwać kilka dni.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Włączanie synchronizacji o określonym zakresie przy użyciu portalu Azure

Aby włączyć synchronizację o określonym zakresie w witrynie Azure portal, wykonaj następujące kroki:

1. Postępuj zgodnie z [samouczkiem, aby utworzyć i skonfigurować wystąpienie usług Azure AD DS](tutorial-create-instance-advanced.md). Wykonaj wszystkie wymagania wstępne i kroki wdrażania inne niż dla zakresu synchronizacji.
1. Wybierz pozycję **Zakres** w kroku synchronizacji, a następnie wybierz grupy usługi Azure AD do synchronizacji z wystąpieniem usług Azure AD DS.

Wdrożenie może potrwać do godziny. W witrynie Azure portal na stronie **Przegląd** domeny zarządzanej usług Azure AD DS jest wyświetlany bieżący stan na tym etapie wdrażania.

Gdy w witrynie Azure Portal zostanie wykazanie, że domena zarządzana usługą Azure AD DS zakończyła inicjowanie obsługi administracyjnej, należy wykonać następujące zadania:

* Zaktualizuj ustawienia DNS dla sieci wirtualnej, aby maszyny wirtualne mogły znaleźć domenę zarządzana dla dołączania do domeny lub uwierzytelniania.
    * Aby skonfigurować usługę DNS, wybierz domenę zarządzana usługą Azure AD DS w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne skonfigurowanie tych ustawień DNS.
* [Włącz synchronizację haseł z usługami domenowymi usługi Azure AD, aby](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) użytkownicy końcowi mogli logować się do domeny zarządzanej przy użyciu poświadczeń firmowych.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Modyfikowanie synchronizacji o określonym zakresie przy użyciu portalu Azure

Aby zmodyfikować listę grup, których użytkownicy powinni być synchronizowani z domeną zarządzana usługą Azure AD DS, wykonaj następujące kroki:

1. W witrynie Azure portal wyszukaj i wybierz pozycję **Usługi domenowe usługi Azure AD**. Wybierz wystąpienie, takie jak *aaddscontoso.com*.
1. Wybierz **opcję Synchronizacja** z menu po lewej stronie.
1. Aby dodać grupę, wybierz **pozycję + Wybierz grupy** u góry, a następnie wybierz grupy do dodania.
1. Aby usunąć grupę z zakresu synchronizacji, zaznacz ją z listy aktualnie zsynchronizowanych grup i wybierz pozycję **Usuń grupy**.
1. Po wprowadzeniu wszystkich zmian wybierz pozycję **Zapisz zakres synchronizacji**.

Zmiana zakresu synchronizacji powoduje, że domena zarządzana usługą Azure AD DS ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej usług Azure AD DS są usuwane, a ponowna synchronizacja może zająć dużo czasu.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Wyłączanie synchronizacji o określonym zakresie przy użyciu witryny Azure portal

Aby wyłączyć synchronizację o określonym zakresie w przypadku domeny zarządzanej usług Azure AD DS w oparciu o grupy, wykonaj następujące kroki:

1. W witrynie Azure portal wyszukaj i wybierz pozycję **Usługi domenowe usługi Azure AD**. Wybierz wystąpienie, takie jak *aaddscontoso.com*.
1. Wybierz **opcję Synchronizacja** z menu po lewej stronie.
1. Ustaw zakres synchronizacji z **zakresu** na **Wszystkie,** a następnie wybierz pozycję **Zapisz zakres synchronizacji**.

Zmiana zakresu synchronizacji powoduje, że domena zarządzana usługą Azure AD DS ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej usług Azure AD DS są usuwane, a ponowna synchronizacja może zająć dużo czasu.

## <a name="powershell-script-for-scoped-synchronization"></a>Skrypt programu PowerShell do synchronizacji o określonym zakresie

Aby skonfigurować synchronizację o określonym zakresie przy użyciu programu PowerShell, należy najpierw zapisać następujący skrypt w pliku o nazwie `Select-GroupsToSync.ps1`. Ten skrypt konfiguruje usługi Azure AD DS do synchronizowania wybranych grup z usługi Azure AD. Wszystkie konta użytkowników, które są częścią określonych grup są synchronizowane z domeną zarządza zarządzana usługą Azure AD DS.

Ten skrypt jest używany w dodatkowych krokach w tym artykule.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Włączanie synchronizacji o określonym zakresie przy użyciu programu PowerShell

Użyj programu PowerShell, aby wykonać następujący zestaw kroków. Zapoznaj się z instrukcjami, aby [włączyć usługi domenowe Active Directory azure przy użyciu programu PowerShell](powershell-create-instance.md). Kilka kroków w tym artykule są nieco modyfikowane w celu skonfigurowania synchronizacji o określonym zakresie.

1. Wykonaj następujące zadania z tego artykułu, aby włączyć usługi Azure AD DS przy użyciu programu PowerShell. Zatrzymaj się na kroku, aby faktycznie utworzyć domenę zarządzaną. Konfigurowanie synchronizacji o określonym zakresie, które tworzysz domenę zarządzana usługą Azure AD DS.

   * [Zainstaluj wymagane moduły programu PowerShell](powershell-create-instance.md#prerequisites).
   * [Utwórz wymaganą jednostkę usługi i grupę usługi Azure AD dla dostępu administracyjnego](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Tworzenie zasobów platformy Azure, takich jak sieć wirtualna i podsieci](powershell-create-instance.md#create-supporting-azure-resources).

1. Określ grupy i użytkowników, które zawierają, które chcesz zsynchronizować z usługi Azure AD. Zrób listę wyświetlanych nazw grup do synchronizacji z usługą Azure AD DS.

1. Uruchom [skrypt z poprzedniej sekcji](#powershell-script-for-scoped-synchronization) i użyj parametru *-groupsToAdd,* aby przekazać listę grup do synchronizacji.

   > [!WARNING]
   > Na liście grup synchronizacji z określonym zakresem należy dołączyć grupę *Administratorzy kontrolera domeny usługi AAD.* Jeśli nie uwzględnisz tej grupy, domena zarządzana usługą Azure AD DS nie nadaje się do użytecznego.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Teraz utwórz domenę zarządzaną usługą Azure AD DS i włącz synchronizację o określonym zakresie w oparciu o grupy. Dołącz *"filteredSync" = "Włączone"* w parametrze *-Properties.*

    Ustaw identyfikator subskrypcji platformy Azure, a następnie podaj nazwę domeny zarządzanej, takiej jak *aaddscontoso.com*. Identyfikator subskrypcji można uzyskać za pomocą polecenia cmdlet [Get-AzSubscription.][Get-AzSubscription] Ustaw nazwę grupy zasobów, nazwę sieci wirtualnej i region na wartości używane w poprzednich krokach do tworzenia obsługiwanych zasobów platformy Azure:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

Utworzenie zasobu i zwrócenie kontroli do monitu programu PowerShell zajmuje kilka minut. Domena zarządzana usługą Azure AD DS nadal jest aprowizowana w tle i może potrwać do godziny, aby zakończyć wdrożenie. W witrynie Azure portal na stronie **Przegląd** domeny zarządzanej usług Azure AD DS jest wyświetlany bieżący stan na tym etapie wdrażania.

Gdy w witrynie Azure Portal zostanie wykazanie, że domena zarządzana usługą Azure AD DS zakończyła inicjowanie obsługi administracyjnej, należy wykonać następujące zadania:

* Zaktualizuj ustawienia DNS dla sieci wirtualnej, aby maszyny wirtualne mogły znaleźć domenę zarządzana dla dołączania do domeny lub uwierzytelniania.
    * Aby skonfigurować usługę DNS, wybierz domenę zarządzana usługą Azure AD DS w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne skonfigurowanie tych ustawień DNS.
* Jeśli utworzono domenę zarządzaną usługą Azure AD DS w regionie obsługującym strefy dostępności, utwórz sieciową grupę zabezpieczeń, aby ograniczyć ruch w sieci wirtualnej dla domeny zarządzanej usług Azure AD DS. Tworzony jest standardowy moduł równoważenia obciążenia platformy Azure, który wymaga umieszczenia tych reguł. Ta sieciowa grupa zabezpieczeń zabezpiecza usługi Azure AD DS i jest wymagana do poprawnego działania domeny zarządzanej.
    * Aby utworzyć sieciową grupę zabezpieczeń i wymagane reguły, wybierz domenę zarządzaną usługą Azure AD DS w portalu. W oknie **Przegląd** zostanie wyświetlony monit o automatyczne utworzenie i skonfigurowanie sieciowej grupy zabezpieczeń.
* [Włącz synchronizację haseł z usługami domenowymi usługi Azure AD, aby](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) użytkownicy końcowi mogli logować się do domeny zarządzanej przy użyciu poświadczeń firmowych.

## <a name="modify-scoped-synchronization-using-powershell"></a>Modyfikowanie synchronizacji o określonym zakresie przy użyciu programu PowerShell

Aby zmodyfikować listę grup, których użytkownicy powinni być synchronizowani z domeną zarządzaną usługą Azure AD DS, uruchom ponownie [skrypt programu PowerShell](#powershell-script-for-scoped-synchronization) i określ nową listę grup. W poniższym przykładzie grupy do synchronizacji nie zawierają już *nazwy grupy GroupName2,* a teraz zawierają *nazwa groupname3*.

> [!WARNING]
> Na liście grup synchronizacji z określonym zakresem należy dołączyć grupę *Administratorzy kontrolera domeny usługi AAD.* Jeśli nie uwzględnisz tej grupy, domena zarządzana usługą Azure AD DS nie nadaje się do użytecznego.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Zmiana zakresu synchronizacji powoduje, że domena zarządzana usługą Azure AD DS ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej usług Azure AD DS są usuwane, a ponowna synchronizacja może zająć dużo czasu.

## <a name="disable-scoped-synchronization-using-powershell"></a>Wyłączanie synchronizacji o określonym zakresie przy użyciu programu PowerShell

Aby wyłączyć synchronizację o zakresie opartą na grupach dla domeny zarządzanej usług Azure AD DS, ustaw *"filteredSync" = "Wyłączone"* w zasobie usług Azure AD DS, a następnie zaktualizuj domenę zarządzaną. Po zakończeniu wszyscy użytkownicy i grupy są ustawione do synchronizacji z usługi Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Zmiana zakresu synchronizacji powoduje, że domena zarządzana usługą Azure AD DS ponownie zsynchronizuje wszystkie dane. Obiekty, które nie są już wymagane w domenie zarządzanej usług Azure AD DS są usuwane, a ponowna synchronizacja może zająć dużo czasu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o procesie synchronizacji, zobacz [Opis synchronizacji w usługach domenowych usługi Azure AD](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
