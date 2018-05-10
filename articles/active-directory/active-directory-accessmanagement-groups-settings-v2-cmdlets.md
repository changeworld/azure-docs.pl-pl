---
title: Przykłady programu PowerShell do zarządzania grupami w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Ta strona zawiera przykłady z programu PowerShell w celu zarządzania grupami w usłudze Azure Active Directory
keywords: Azure AD, Azure Active Directory, programu PowerShell, grup, grupy zarządzania
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: rodejo
ms.openlocfilehash: 312efd6233546ae32e498907e04fbf8aea73f7b7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Usługi Active Directory w wersji 2 poleceń cmdlet systemu Azure dla grupy zarządzania
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Ten artykuł zawiera przykłady Zarządzanie grupami w usłudze Azure Active Directory (Azure AD) przy użyciu programu PowerShell.  On również wyjaśniono, jak pobrać skonfigurować moduł Azure AD PowerShell. Najpierw należy [Pobierz moduł Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Zainstaluj moduł programu PowerShell usługi Azure AD
Aby zainstalować moduł Azure AD PowerShell, użyj następujących poleceń:

    PS C:\Windows\system32> install-module azuread

Aby sprawdzić, czy moduł został zainstalowany, użyj następującego polecenia:

    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}

Teraz można uruchomić przy użyciu poleceń cmdlet w module. Pełny opis poleceń cmdlet w module usługi Azure AD, można znaleźć w dokumentacji online dla [Azure Active Directory programu PowerShell w wersji 2](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connect-to-the-directory"></a>Łączenie się z katalogiem
Aby umożliwić zarządzanie grupami za pomocą poleceń cmdlet programu PowerShell usługi Azure AD, należy połączyć sesji programu PowerShell do katalogu, który ma być zarządzany. Użyj następującego polecenia:

    PS C:\Windows\system32> Connect-AzureAD

Polecenie cmdlet monituje o poświadczenia, który ma być używany na dostęp do Twojego katalogu. W tym przykładzie używamy karen@drumkit.onmicrosoft.com dostępu do katalogu pokazu. Polecenie cmdlet zwraca potwierdzenie, że sesja została pomyślnie podłączona do katalogu:

    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…

Teraz można uruchomić przy użyciu poleceń cmdlet AzureAD do zarządzania grupami w katalogu.

## <a name="retrieve-groups"></a>Pobieranie grupy
Aby pobrać istniejących grup z katalogu, należy użyć polecenia cmdlet Get-AzureADGroups. 

Aby uzyskać dostęp do wszystkich grup w katalogu, należy użyć polecenia cmdlet bez parametrów:

    PS C:\Windows\system32> get-azureadgroup

Polecenie cmdlet zwraca wszystkie grupy połączonego katalogu.

Można użyć parametru - objectID można pobrać określonej grupy, dla którego należy określić identyfikator obiektu grupy:

    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b

Polecenie cmdlet zwraca teraz grupy, którego identyfikator obiektu jest zgodna z wartością parametru wprowadzony:

    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Możesz wyszukać określoną grupę za pomocą parametru - filtru. Ten parametr przyjmuje klauzuli filtru ODATA i zwraca wszystkich grup, które są zgodne z filtrem, jak w poniższym przykładzie:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

> [!NOTE] 
> Polecenia cmdlet programu Azure AD PowerShell wdrożenia standardowego zapytania OData. Aby uzyskać więcej informacji, zobacz **$filter** w [opcje zapytania systemu OData przy użyciu punktu końcowego OData](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Tworzenie grup
Aby utworzyć nową grupę w katalogu, należy użyć polecenia cmdlet New-AzureADGroup. To polecenie cmdlet tworzy nową grupę zabezpieczeń o nazwie "Marketing":

    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"

## <a name="update-groups"></a>Grupy aktualizacji
Aby uaktualnić istniejącą grupę, należy użyć polecenia cmdlet Set-AzureADGroup. W tym przykładzie Zmieniamy Właściwość DisplayName grupy "Administratorzy usługi Intune". Po pierwsze są znajdowane grupy za pomocą polecenia cmdlet Get-AzureADGroup są i Filtruj, używając atrybutu Nazwa wyświetlana:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

Następnie Zmieniamy właściwości Description na nową wartość "Administratorzy usługi Intune urządzenie":

    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"

Teraz możemy znaleźć grupy możemy wyświetlić właściwości Description zostanie zaktualizowany w celu uwzględnienia nowej wartości:

    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True

## <a name="delete-groups"></a>Usuń grupy
Usuń grupy z katalogiem, użyj polecenia cmdlet Remove-AzureADGroup w następujący sposób:

    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b

## <a name="manage-group-membership"></a>Zarządzaj członkostwem w grupach 
### <a name="add-members"></a>Dodaj członków
Aby dodać nowe elementy członkowskie do grupy, użyj polecenia cmdlet Add-AzureADGroupMember. To polecenie dodaje członka do grupy Administratorzy usługi Intune, używane w poprzednim przykładzie:

    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Identyfikator obiektu grupy, do którego chcemy dodać element członkowski jest parametr - ObjectId, a RefObjectId — jest identyfikator obiektu użytkownika, którego chcemy, aby dodać jako członka do grupy.

### <a name="get-members"></a>Pobierz elementy członkowskie
Aby uzyskać istniejących członków grupy, należy użyć polecenia cmdlet Get-AzureADGroupMember, jak w poniższym przykładzie:

    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User

### <a name="remove-members"></a>Usuń członków
Aby usunąć element członkowski, który wcześniej dodane do grupy, użyj polecenia cmdlet Remove-AzureADGroupMember, jak to pokazano poniżej:

    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

### <a name="verify-members"></a>Sprawdź elementy członkowskie
Aby sprawdzić członkostwa w grupach użytkownika, użyj polecenia cmdlet AzureADGroupIdsUserIsMemberOf wybierz. To polecenie cmdlet przyjmuje jako parametry ObjectId użytkownika, do których chcesz sprawdzić członkostwa w grupach i listę grup, do których chcesz sprawdzić członkostwa. Lista grup muszą być w formie złożone zmiennej typu "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", dlatego firma Microsoft najpierw utworzyć zmienną z danym typem:

    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck

Następnie możemy podać wartości identyfikatory GroupID sprawdzić w atrybucie "Identyfikatory GroupID" tej zmiennej złożone:

    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"

Teraz Jeśli chcemy sprawdzić członkostwa w grupach użytkownika z 72cd4bbd-2594-40a2-935c-016f3cfeeeea ObjectID dla grup w $g, możemy użyć:

    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}


Wartość zwracana jest lista grup, których członkiem jest ten użytkownik. Można także zastosować dla tej metody do sprawdzenia członkostwa kontakty, grupy lub podmiotów zabezpieczeń usługi dla danej listy grup, za pomocą AzureADGroupIdsContactIsMemberOf Select, wybierz AzureADGroupIdsGroupIsMemberOf lub Wybierz AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Wyłącz tworzenie grupy użytkowników
Aby uniemożliwić użytkownikom niebędącym administratorami tworzenie grup zabezpieczeń. Domyślne zachowanie w Microsoft Online Directory Services (MSODS) jest umożliwienie użytkownikom niebędącym administratorami tworzenie grup, czy grupy Samoobsługowe zarządzanie GRUPAMI jest włączona. Ustawienie GRUPAMI steruje zachowaniem tylko w panelu dostępu Moje aplikacje. 

Aby wyłączyć tworzenie grupy dla użytkowników bez uprawnień administratora:

1. Sprawdź, czy użytkownicy inni niż administratorzy mogą tworzyć grupy:
   
  ````
  PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
  ````
  
2. Jeśli zmienna zwraca `UsersPermissionToCreateGroupsEnabled : True`, a następnie użytkownicy inni niż administratorzy mogą tworzyć grupy. Aby wyłączyć tę funkcję:
  
  ```` 
  Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
  ````
  
## <a name="manage-owners-of-groups"></a>Zarządzanie właścicielami grupy
Aby dodać właścicieli do grupy, użyj polecenia cmdlet Add-AzureADGroupOwner:

    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea

Parametr - ObjectId jest identyfikator obiektu grupy, do którego chcemy dodać właściciela i RefObjectId — jest identyfikator obiektu użytkownika, którego chcemy, aby dodać jako właściciela grupy.

Aby pobrać właścicieli grupy, użyj polecenia cmdlet Get-AzureADGroupOwner:

    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

Polecenie cmdlet zwraca listę właścicieli dla określonej grupy:

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User

Jeśli chcesz usunąć właściciela z grupy, należy użyć polecenia cmdlet Remove-AzureADGroupOwner:

    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67

## <a name="reserved-aliases"></a>Aliasy zastrzeżone 
Po utworzeniu grupy pewność, że punkty końcowe umożliwiają użytkownikom końcowym Określ mailNickname lub alias ma być używany jako część adresu e-mail grupy. Grupy z następujących aliasów e-mail wysoko uprzywilejowane można tworzyć tylko przez administratora globalnego usługi Azure AD. 
  
* nadużyć 
* administrator 
* administrator 
* hostmaster 
* majordomo 
* Postmaster 
* główny 
* Zabezpieczenia 
* security 
* admin protokołu SSL 
* webmaster 

## <a name="next-steps"></a>Kolejne kroki
Więcej dokumentacji programu PowerShell usługi Azure Active Directory można znaleźć [polecenia cmdlet usługi Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](active-directory-manage-groups.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
