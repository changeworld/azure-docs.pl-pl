---
title: Zezwala lub blokuje zaproszeń do użytkowników B2B z określonym organizacjom — Azure Active Directory | Dokumentacja firmy Microsoft
description: Pokazuje, jak administrator może użyć portalu Azure lub programu PowerShell można ustawić dostęp lub odmówić listy blokowana B2B użytkowników z niektórych domen.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/19/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 7e89bf47f592e4698a6e50fced78aeab0152ebc6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Zezwala lub blokuje zaproszeń do użytkowników B2B z określonym organizacjom

Listy dozwolonych lub listę odrzuconych służy do dozwolonych lub zablokowanych zaproszeń do użytkowników B2B z określonym organizacjom. Na przykład jeśli chcesz zablokować domen adresów e-mail osobistych, można skonfigurować listę odmowy, która zawiera domen, takich jak Gmail.com i Outlook.com. Lub, jeśli firma ma powiązanie z innych firm, takich jak Contoso.com, Fabrikam.com i Litware.com, i chcesz ograniczyć zaproszenia tylko tych organizacji, możesz dodać Contoso.com, Fabrikam.com i Litware.com do Twojej listy dozwolonych.
  
## <a name="important-considerations"></a>Ważne uwagi

- Można utworzyć listy dozwolonych lub listę odrzuconych. Nie można skonfigurować obu typów list. Domyślnie, niezależnie od domeny nie są w na białej liście są na liście odmowy i na odwrót. 
- Można utworzyć tylko jedne zasady dla organizacji. Można zaktualizować zasad, aby uwzględnić więcej domen, lub możesz usunąć zasadę, aby utworzyć nowy. 
- Ta lista działa niezależnie od usługi OneDrive dla firm i SharePoint Online list dozwolonych/zablokowanych. Jeśli chcesz ograniczyć poszczególnych udostępniania plików w usłudze SharePoint Online, należy skonfigurować Zezwalaj lub Odmów listę dla usługi OneDrive dla firm i SharePoint Online. Aby uzyskać więcej informacji, zobacz [ograniczone domen w usłudze SharePoint Online i OneDrive dla firm do udostępniania](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Ta lista nie dotyczą użytkowników zewnętrznych, które już zostały zrealizowane zaproszenia. Po skonfigurowaniu listy, listy zostaną wymuszone. Jeśli zaproszenie do użytkownika jest w stanie oczekiwania i musisz ustawić zasady, która blokuje ich domeny, użytkownika Aby zrealizować zaproszenia powiedzie się.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Ustawienie Zezwalaj lub Odmów listy zasad w portalu

Domyślnie **Zezwalaj zaproszeń do wysłania do dowolnej domeny (najbardziej z wartościami granicznymi)** ustawienie jest włączone. W takim przypadku można zaprosić użytkowników B2B w każdej organizacji.

### <a name="add-a-deny-list"></a>Dodaj listę Odmów

Jest to najbardziej typowy scenariusz, których potrzebuje do pracy z niemal dowolnego organizację organizacji, ale chce zapobiec użytkownicy z określonych domen mają być zapraszani jako B2B użytkowników.

Aby dodać listę odrzuconych:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **usługi Azure Active Directory** > **użytkowników** > **ustawienia użytkownika**.
3. W obszarze **użytkowników zewnętrznych**, wybierz pozycję **Zarządzanie ustawieniami współpracy zewnętrznej**.
4. W obszarze **ograniczenia współpracy**, wybierz pozycję **odmowy zaproszenia do określonych domen**.
5. W obszarze **DOMEN**, wprowadź nazwę domeny, które chcesz zablokować. Dla wielu domen wprowadź każdej domeny w nowym wierszu. Na przykład:

   ![Pokazuje opcję Odmów domenom dodany](./media/active-directory-b2b-allow-deny-list/DenyListSettings.png)
 
6. Gdy wszystko będzie gotowe, kliknij przycisk **zapisać**.

Po ustawieniu zasad, Jeśli spróbujesz zaprosić użytkowników z zablokowanych domen, pojawi się komunikat z informacją, że domeny użytkownika jest obecnie zablokowany przez zasady zaproszenia.
 
### <a name="add-an-allow-list"></a>Dodaj do listy dozwolonych

Jest to bardziej restrykcyjne konfiguracji, których można ustawić określonych domen na liście dozwolonych i ograniczyć zaproszeń do skorzystania z innymi organizacjami lub domeny, które nie są wymienione. 

Jeśli chcesz użyć listy dozwolonych, upewnij się, że poświęcić czas na pełni ocenę potrzeb firmy są. Po wybraniu tych zasad zbyt restrykcyjne, użytkownicy mogą wybrać wysyłać dokumenty za pośrednictwem poczty e-mail lub Znajdź inne-IT oficjalnie zaakceptowanych sposoby współpraca.


Aby dodać listy dozwolonych:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **usługi Azure Active Directory** > **użytkowników** > **ustawienia użytkownika**.
3. W obszarze **użytkowników zewnętrznych**, wybierz pozycję **Zarządzanie ustawieniami współpracy zewnętrznej**.
4. W obszarze **ograniczenia współpracy**, wybierz pozycję **Zezwalaj tylko na określonych domen zaproszeń (najbardziej restrykcyjne)**.
5. W obszarze **DOMEN**, wprowadź nazwę domeny, które chcesz zezwolić. Dla wielu domen wprowadź każdej domeny w nowym wierszu. Na przykład:

   ![Pokazuje opcję Zezwalaj domenom dodany](./media/active-directory-b2b-allow-deny-list/AllowListSettings.png)
 
6. Gdy wszystko będzie gotowe, kliknij przycisk **zapisać**.

Po ustawieniu zasad, Jeśli spróbujesz zaprosić użytkowników z domeny, który nie znajduje się na liście dozwolonych, pojawi się komunikat z informacją, że domeny użytkownika jest obecnie zablokowany przez zasady zaproszenia.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Zezwalaj na przejście z odrzucanie listy i na odwrót 

Po przełączeniu z jednej zasady do innych to spowoduje odrzucenie istniejącą konfigurację zasad. Upewnij się utworzyć kopię zapasową szczegółów konfiguracji przed wykonaniem tego przełącznika. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Ustawienie Zezwalaj lub Odmów zasady listy przy użyciu programu PowerShell

### <a name="prerequisite"></a>Wymagania wstępne

Aby ustawić Zezwalaj lub Odmów listy przy użyciu programu PowerShell, należy zainstalować wersję zapoznawczą usługi Azure Active Directory modułu dla środowiska Windows PowerShell. W szczególności zainstalować AzureADPreview wersji modułu 2.0.0.98 lub nowszym.

Aby sprawdzić wersję modułu (i czy jest zainstalowany):
 
1. Otwórz program Windows PowerShell z podwyższonymi uprawnieniami użytkownika (Uruchom jako Administrator). 
2. Uruchom następujące polecenie, jeśli masz wszystkie wersje usługi Azure Active Directory modułu dla Windows PowerShell zainstalowanej na komputerze:

   ````powershell  
   Get-Module -ListAvailable AzureAD*
   ````

Jeśli moduł nie jest zainstalowany lub nie ma wymaganej wersji, wykonaj jedną z następujących czynności:

- Jeśli żadne wyniki nie są zwracane, uruchom następujące polecenie, aby zainstalować najnowszą wersję modułu AzureADPreview:
  
   ````powershell  
   Install-Module AzureADPreview
   ````
- Jeśli tylko moduł AzureAD jest wyświetlana w wynikach, uruchom następujące polecenia, aby zainstalować moduł AzureADPreview: 

   ````powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ````
- Jeśli tylko moduł AzureADPreview jest wyświetlana w wynikach, ale wersja jest mniejsza niż 2.0.0.98, uruchom następujące polecenia, aby zaktualizować go: 

   ````powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ````

- Jeśli zarówno AzureAD i AzureADPreview moduły są wyświetlane w wynikach, ale wersja modułu AzureADPreview jest mniejsza niż 2.0.0.98, uruchom następujące polecenia, aby zaktualizować go: 

   ````powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ````

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Konfigurowanie zasad przy użyciu poleceń cmdlet AzureADPolicy

Aby utworzyć Zezwalaj lub Odmów listy, użyj [AzureADPolicy nowy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) polecenia cmdlet. Poniższy przykład pokazuje, jak ustawić listę Odmów, która blokuje domeny "live.com".

````powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

Poniżej pokazano, tym samym przykładzie, ale z wbudowanym definicji zasad.

````powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
````

Aby ustawić Zezwalaj lub Odmów listy zasad, użyj [AzureADPolicy zestaw](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) polecenia cmdlet. Na przykład:

````powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
````

Aby pobrać zasady, użyj [Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) polecenia cmdlet. Na przykład:

````powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
````

Aby usunąć zasady, użyj [AzureADPolicy Usuń](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) polecenia cmdlet. Na przykład:

````powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
````

## <a name="next-steps"></a>Kolejne kroki

- Omówienie B2B usługi Azure AD, zobacz [co to jest współpraca B2B usługi Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
- Informacje dotyczące dostępu warunkowego i współpracy B2B, zobacz [dostępu warunkowego dla użytkowników współpracy B2B](active-directory-b2b-mfa-instructions.md).



