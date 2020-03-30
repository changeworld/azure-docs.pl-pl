---
title: Przykłady programu PowerShell V2 do zarządzania grupami — Usługa Azure AD | Dokumenty firmy Microsoft
description: Ta strona zawiera przykłady programu PowerShell ułatwiające zarządzanie grupami w usłudze Azure Active Directory
keywords: Usługa Azure AD, usługa Azure Active Directory, program PowerShell, grupy, zarządzanie grupami
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a218e956c72f8005e533db7b8800e98ee72ce223
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74233113"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Polecenia cmdlet usługi Azure Active Directory w wersji 2 do zarządzania grupami

> [!div class="op_single_selector"]
> - [Portal Azure](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> - [Powershell](groups-settings-v2-cmdlets.md)
>
>

Ten artykuł zawiera przykłady sposobu używania programu PowerShell do zarządzania grupami w usłudze Azure Active Directory (Azure AD).  Informuje również, jak skonfigurować moduł programu Azure AD PowerShell. Najpierw należy [pobrać moduł programu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Instalowanie modułu programu Azure AD PowerShell

Aby zainstalować moduł programu Azure AD PowerShell, należy użyć następujących poleceń:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Aby sprawdzić, czy moduł jest gotowy do użycia, użyj następującego polecenia:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Teraz możesz zacząć używać poleceń cmdlet w module. Pełny opis poleceń cmdlet w module usługi Azure AD można znaleźć w dokumentacji referencyjnej online dla [programu Azure Active Directory PowerShell w wersji 2.](/powershell/azure/install-adv2?view=azureadps-2.0)

## <a name="connect-to-the-directory"></a>Łączenie się z katalogiem

Przed rozpoczęciem zarządzania grupami przy użyciu poleceń cmdlet programu Azure AD PowerShell należy połączyć sesję programu PowerShell z katalogiem, który chcesz zarządzać. Użyj następującego polecenia:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

Polecenie cmdlet monituje o poświadczenia, których chcesz użyć, aby uzyskać dostęp do katalogu. W tym przykładzie karen@drumkit.onmicrosoft.com używamy dostępu do katalogu demonstracyjnego. Polecenie cmdlet zwraca potwierdzenie, aby pokazać, że sesja została pomyślnie połączona z katalogiem:

```powershell
    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

Teraz możesz rozpocząć korzystanie z poleceń cmdlet AzureAD do zarządzania grupami w katalogu.

## <a name="retrieve-groups"></a>Pobieranie grup

Aby pobrać istniejące grupy z katalogu, należy użyć polecenia cmdlet Get-AzureADGroups. 

Aby pobrać wszystkie grupy w katalogu, użyj polecenia cmdlet bez parametrów:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

Polecenie cmdlet zwraca wszystkie grupy w połączonym katalogu.

Za pomocą parametru -objectID można pobrać określoną grupę, dla której określono identyfikator obiektu grupy:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

Polecenie cmdlet zwraca teraz grupę, której identyfikator obiektu odpowiada wartości wprowadzonego parametru:

```powershell
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
```

Można wyszukać określoną grupę za pomocą parametru -filter. Ten parametr przyjmuje klauzulę filtru ODATA i zwraca wszystkie grupy, które pasują do filtru, jak w poniższym przykładzie:

```powershell
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
```

> [!NOTE]
> Polecenia cmdlet programu Azure AD PowerShell implementują standard kwerendy OData. Aby uzyskać więcej informacji, zobacz **$filter** [w opcjach kwerend systemowych OData przy użyciu punktu końcowego OData](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Tworzenie grup

Aby utworzyć nową grupę w katalogu, użyj polecenia cmdlet New-AzureADGroup. To polecenie cmdlet tworzy nową grupę zabezpieczeń o nazwie "Marketing":

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Aktualizowanie grup

Aby zaktualizować istniejącą grupę, należy użyć polecenia cmdlet Set-AzureADGroup. W tym przykładzie zmieniamy właściwość DisplayName grupy "Administratorzy usługi Intune". Najpierw znajdujemy grupę przy użyciu polecenia cmdlet i filtrowania Get-AzureADGroup przy użyciu atrybutu DisplayName:

```powershell
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
```

Następnie zmieniamy właściwość Description na nową wartość "Administratorzy urządzeń usługi Intune":

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Teraz, jeśli ponownie znajdziemy grupę, widzimy, że właściwość Description jest aktualizowana w celu odzwierciedlenia nowej wartości:

```powershell
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
```

## <a name="delete-groups"></a>Usuwanie grup

Aby usunąć grupy z katalogu, użyj polecenia cmdlet Remove-AzureADGroup w następujący sposób:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Zarządzanie członkostwem w grupie

### <a name="add-members"></a>Dodawanie członków

Aby dodać nowych członków do grupy, należy użyć polecenia cmdlet Add-AzureADGroupMember. To polecenie dodaje członka do grupy Administratorzy usługi Intune, którą użyliśmy w poprzednim przykładzie:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Parametr -ObjectId jest objectID grupy, do której chcemy dodać element członkowski, a -RefObjectId jest ObjectID użytkownika, który chcemy dodać jako członek grupy.

### <a name="get-members"></a>Uzyskaj członków

Aby uzyskać istniejących członków grupy, należy użyć polecenia cmdlet Get-AzureADGroupMember, jak w tym przykładzie:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Usuwanie członków

Aby usunąć członka, którego wcześniej dodaliśmy do grupy, użyj polecenia cmdlet Remove-AzureADGroupMember, jak pokazano poniżej:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Weryfikowanie członków

Aby zweryfikować członkostwo w grupach użytkownika, użyj polecenia cmdlet Select-AzureADGroupIdsUserIsMemberOf. To polecenie cmdlet przyjmuje za swoje parametry ObjectId użytkownika, dla którego można sprawdzić członkostwa w grupach i listę grup, dla których należy sprawdzić członkostwa. Lista grup musi być podana w postaci złożonej zmiennej typu "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck", więc najpierw musimy utworzyć zmienną o tym typie:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

Następnie udostępniamy wartości dla groupIds, aby zaewidencjonować atrybut "GroupIds" tej złożonej zmiennej:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Teraz, jeśli chcemy sprawdzić członkostwa w grupie użytkownika z ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea przeciwko grupom w $g, powinniśmy użyć:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

Zwrócona wartość to lista grup, których członkiem jest ten użytkownik. Tę metodę można również zastosować w celu sprawdzenia członkostwa kontaktów, grup lub podmiotów zabezpieczeń usługi dla danej listy grup, używając select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf lub Select-AzureADGroupIdsGroupIsMemberOf lub Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Wyłączanie tworzenia grup przez użytkowników

Można uniemożliwić użytkownikom niebędącym administratorami tworzenie grup zabezpieczeń. Domyślnym zachowaniem w usługach msods (Microsoft Online Directory Services) jest umożliwienie użytkownikom niebędącym administratorami tworzenia grup, niezależnie od tego, czy jest również włączone samoobsługowe zarządzanie grupami (SSGM). Ustawienie SSGM steruje zachowaniem tylko w panelu dostęp do moich aplikacji.

Aby wyłączyć tworzenie grup dla użytkowników niebędących administratorami:

1. Sprawdź, czy użytkownicy niebędący administratorami mogą tworzyć grupy:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Jeśli powróci, `UsersPermissionToCreateGroupsEnabled : True`użytkownicy niebędący administratorami mogą tworzyć grupy. Aby wyłączyć tę funkcję:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Zarządzanie właścicielami grup

Aby dodać właścicieli do grupy, użyj polecenia cmdlet Add-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

Parametr -ObjectId jest objectID grupy, do której chcemy dodać właściciela, a -RefObjectId jest ObjectID użytkownika lub jednostki usługi, którą chcemy dodać jako właściciel grupy.

Aby pobrać właścicieli grupy, należy użyć polecenia cmdlet Get-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

Polecenie cmdlet zwraca listę właścicieli (użytkowników i podmiotów serwisu) dla określonej grupy:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Jeśli chcesz usunąć właściciela z grupy, użyj polecenia cmdlet Remove-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Aliasy zarezerwowane

Podczas tworzenia grupy niektóre punkty końcowe umożliwiają użytkownikowi końcowemu określenie nazwy mailNickname lub aliasu, który ma być używany jako część adresu e-mail grupy.Grupy z następującymi wysoce uprzywilejowanymi aliasami wiadomości e-mail mogą być tworzone tylko przez administratora globalnego usługi Azure AD. 
  
* Nadużyć
* administrator
* administrator
* hostmaster
* majordomo ( majordomo )
* Postmaster
* root
* Bezpieczne
* security
* ssl-admin
* Webmaster

## <a name="group-writeback-to-on-premises-preview"></a>Grupowanie stornowanie do lokalnego (wersja zapoznawcza)

Obecnie wiele grup jest nadal zarządzanych w lokalnej usłudze Active Directory. Aby odpowiedzieć na żądania synchronizacji grup w chmurze z powrotem do lokalnego, funkcja stornowania grup usługi Office 365 dla usługi Azure AD jest teraz dostępna w wersji zapoznawczej.

Grupy usługi Office 365 są tworzone i zarządzane w chmurze. Funkcja stornia zwrotnego umożliwia zapisywanie grup usługi Office 365 jako grup dystrybucyjnych w lesie usługi Active Directory z zainstalowanym programem Exchange. Użytkownicy z lokalnymi skrzynkami pocztowymi programu Exchange mogą następnie wysyłać i odbierać wiadomości e-mail z tych grup. Funkcja stornowanie grupy nie obsługuje grup zabezpieczeń usługi Azure AD ani grup dystrybucyjnych.

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją [usługi synchronizacji usługi Azure AD Connect.](../hybrid/how-to-connect-syncservice-features.md)

Stornia zwrotne grupy usługi Office 365 to publiczna funkcja w wersji zapoznawczej usługi Azure Active Directory (Azure AD) i jest dostępna z dowolnym płatnym planem licencji usługi Azure AD. Aby uzyskać informacje prawne dotyczące wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="next-steps"></a>Następne kroki

Więcej dokumentacji programu Azure Active Directory PowerShell można znaleźć w [poleceniach cmdlet usługi Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Zarządzanie dostępem do zasobów za pomocą grup usługi Azure Active Directory](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
