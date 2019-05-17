---
title: Zezwalaj lub Blokuj zaproszenia do określonym organizacjom — usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Pokazuje, jak administrator może użyć witryny Azure portal lub programu PowerShell do ustawiania dostęp lub odmowa listy do zezwalania lub blokowania użytkowników B2B z niektórych domen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b302ec0265473e09b3960660b10661faa1960442
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812963"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Zezwalaj na zaproszenia lub blokowanie ich dla użytkowników B2B z określonym organizacjom

Umożliwia listy dozwolonych lub listę odrzuconych dozwolonych lub zablokowanych zaproszeń do użytkowników B2B z określonym organizacjom. Na przykład jeśli chcesz zablokować domeny adresów e-mail osobiste, możesz skonfigurować liście odrzuconych, który zawiera domeny, takich jak Gmail.com i Outlook.com. Lub, jeśli firma ma współpracuje z innych firm, takich jak Contoso.com i Fabrikam.com, Litware.com i chcesz ograniczyć zaproszenia do tylko tych organizacji, możesz dodać Contoso.com i Fabrikam.com, Litware.com do Twojej listy dozwolonych.
  
## <a name="important-considerations"></a>Istotne zagadnienia

- Można utworzyć listy dozwolonych lub listę odrzuconych. Nie można skonfigurować oba typy list. Domyślnie, niezależnie od domen nie znajdują się w dozwolonych są na liście odmowy i na odwrót. 
- Można utworzyć tylko jeden zestaw zasad dla danej organizacji. Można zaktualizować zasad, aby uwzględnić więcej domen, lub możesz usunąć zasady Aby utworzyć nową. 
- Ta lista działa niezależnie od usługi OneDrive dla firm i SharePoint Online list dozwolonych/zablokowanych. Jeśli chcesz ograniczyć poszczególnych udostępniania plików w usłudze SharePoint Online, należy skonfigurować dozwolonych lub Lista niedozwolonych aplikacji na potrzeby usługi OneDrive dla firm i SharePoint Online. Aby uzyskać więcej informacji, zobacz [domeny udostępnianie w usłudze SharePoint Online i OneDrive dla firm z ograniczeniami](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Ta lista nie ma zastosowania dla użytkowników zewnętrznych, którzy już wykorzystana zaproszenia. Po skonfigurowaniu listy, listy będzie wymuszane. Zaproszenia do użytkownika jest w stanie oczekiwania i musisz ustawić zasady, która blokuje ich domeny, próba zrealizować zaproszenia użytkownika zakończy się niepowodzeniem.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Ustaw Zezwalaj lub Odmów zasady listy w portalu

Domyślnie **Zezwalaj na zaproszenia do wysłania do wszystkich domen (najbardziej liberalne)** ustawienie jest włączone. W takim przypadku można zaprosić użytkowników B2B z każdej organizacji.

### <a name="add-a-deny-list"></a>Dodaj listę odrzuconych

Jest to najbardziej typowy scenariusz, których Twoja organizacja chce, aby pracować z prawie każdej organizacji, ale chce uniemożliwić użytkownikom z określonych domen może zaprosić jako użytkowników B2B.

Aby dodać listę odrzuconych:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **usługi Azure Active Directory** > **użytkowników** > **ustawienia użytkownika**.
3. W obszarze **użytkowników zewnętrznych**, wybierz opcję **Zarządzaj ustawieniami współpracy zewnętrznej**.
4. W obszarze **ograniczenia współpracy**, wybierz opcję **Odmawiaj zaproszeń do określonych domen**.
5. W obszarze **domeny docelowe**, wprowadź nazwę domeny, które chcesz zablokować. W przypadku wielu domen należy wprowadzić każdej domeny w nowym wierszu. Na przykład:

   ![Pokazuje opcję Odmów domenom dodano](./media/allow-deny-list/DenyListSettings.png)
 
6. Gdy wszystko będzie gotowe, kliknij przycisk **Zapisz**.

Po ustawieniu zasad, jeśli zostanie podjęta próba zaprosić użytkownika z zablokowanych domen, pojawi się komunikat z informacją, że domena użytkownika jest obecnie blokowana przez zasady zapraszania.
 
### <a name="add-an-allow-list"></a>Dodaj listę dozwolonych

Jest to bardziej restrykcyjne konfiguracji, których można ustawić określone domeny na liście dozwolonych i ograniczyć zaproszenia do innych organizacji i domeny, które nie są wymienione. 

Jeśli chcesz użyć listy dozwolonych, upewnij się, że poświęcać czasu na w pełni ocenę potrzeb firmy są. Jeśli ta zasada będzie zbyt restrykcyjne, użytkownicy mają możliwość wysyłać dokumenty za pośrednictwem poczty e-mail, lub znaleźć inne infrastruktury informatycznej oficjalnie sposoby współpracować.


Aby dodać listę dozwolonych:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **usługi Azure Active Directory** > **użytkowników** > **ustawienia użytkownika**.
3. W obszarze **użytkowników zewnętrznych**, wybierz opcję **Zarządzaj ustawieniami współpracy zewnętrznej**.
4. W obszarze **ograniczenia współpracy**, wybierz opcję **Zezwalaj na zaproszenia tylko do określonych domen (najbardziej restrykcyjne)**.
5. W obszarze **domeny docelowe**, wprowadź nazwę domeny, które chcesz zezwolić. W przypadku wielu domen należy wprowadzić każdej domeny w nowym wierszu. Na przykład:

   ![Pokazuje opcję Zezwalaj domenom dodano](./media/allow-deny-list/AllowListSettings.png)
 
6. Gdy wszystko będzie gotowe, kliknij przycisk **Zapisz**.

Po ustawieniu zasad, jeśli zostanie podjęta próba zaprosić użytkownika z domeny, który nie znajduje się na liście dozwolonych, pojawi się komunikat z informacją, że domena użytkownika jest obecnie blokowana przez zasady zapraszania.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Przejdź z umożliwiają Odmów listy i na odwrót 

Po przełączeniu z jednej zasady do drugiego to odrzuca istniejącą konfigurację zasad. Upewnij się utworzyć kopię zapasową szczegółowe informacje o konfiguracji, przed wykonaniem switch. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Ustaw Zezwalaj lub Odmów zasady listy przy użyciu programu PowerShell

### <a name="prerequisite"></a>Wymaganie wstępne

Aby ustawić dozwolonych lub Lista niedozwolonych aplikacji przy użyciu programu PowerShell, należy zainstalować wersję zapoznawczą usługi Azure Active Directory modułu dla Windows PowerShell. W szczególności zainstalować AzureADPreview wersja modułu 2.0.0.98 lub nowszej.

Aby sprawdzić wersję modułu (i czy jest zainstalowany):
 
1. Otwórz program Windows PowerShell z podwyższonymi uprawnieniami użytkownika (Uruchom jako Administrator). 
2. Uruchom następujące polecenie, aby zobaczyć, jeśli wszystkie wersje usługi Azure Active Directory modułu dla Windows PowerShell na komputerze zainstalowany:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Jeśli nie zainstalowano modułu, lub nie ma wymaganej wersji, wykonaj jedną z następujących czynności:

- Jeśli żadne wyniki nie zostaną zwrócone, uruchom następujące polecenie, aby zainstalować najnowszą wersję modułu AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Jeśli tylko moduł usługi Azure AD jest wyświetlany w wynikach, uruchom następujące polecenia, aby zainstalować moduł AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Jeśli istnieją tylko w module AzureADPreview jest wyświetlana w wynikach, ale wersja jest mniejsza niż 2.0.0.98, uruchom następujące polecenia, aby zaktualizować go: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Jeśli moduły usługi Azure AD i AzureADPreview są wyświetlane w wynikach, ale wersja AzureADPreview module jest mniejsza niż 2.0.0.98, uruchom następujące polecenia, aby zaktualizować go: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Skonfiguruj zasady za pomocą poleceń cmdlet AzureADPolicy

Aby utworzyć dozwolonych lub Lista niedozwolonych aplikacji, użyj [New AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) polecenia cmdlet. Poniższy przykład pokazuje, jak ustawić listę odrzuconych które blokuje domeny "live.com".

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Poniżej pokazano w tym samym przykładzie, ale za pomocą wbudowanych definicji zasad.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Aby ustawić Zezwalaj lub Odmów listy zasad, należy użyć [AzureADPolicy zestaw](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) polecenia cmdlet. Na przykład:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Aby pobrać zasady wymagane, należy użyć [Get AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) polecenia cmdlet. Na przykład:

```powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Aby usunąć zasady, użyj [AzureADPolicy Usuń](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) polecenia cmdlet. Na przykład:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Kolejne kroki

- Omówienie usługi Azure AD B2B, zobacz [czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- Aby uzyskać informacji na temat dostępu warunkowego i współpraca B2B, zobacz [dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md).



