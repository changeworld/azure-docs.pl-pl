---
title: Zezwalaj lub blokuj zaproszenia do określonych organizacji — Usługa Azure AD
description: Pokazuje, jak administrator może używać witryny Azure portal lub programu PowerShell do ustawiania listy dostępu lub odmowy w celu zezwalania lub blokowania użytkowników B2B z niektórych domen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/15/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5100c4406cfd4a8395dfa177dc3cd5e911decb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273423"
---
# <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Zezwalanie na zaproszenia lub ich blokowanie dla użytkowników B2B z określonych organizacji

Można użyć listy dozwolonych lub listy odmów, aby zezwolić lub zablokować zaproszenia dla użytkowników B2B z określonych organizacji. Jeśli na przykład chcesz zablokować domeny osobistych adresów e-mail, możesz skonfigurować listę odmów zawierającą domeny takie jak Gmail.com i Outlook.com. Jeśli twoja firma współpracuje z innymi firmami, takimi jak Contoso.com, Fabrikam.com i Litware.com, a chcesz ograniczyć zaproszenia tylko do tych organizacji, możesz dodać Contoso.com, Fabrikam.com i Litware.com do listy dozwolonych.
  
## <a name="important-considerations"></a>Istotne zagadnienia

- Można utworzyć listę dozwolonych lub listę odmowy. Nie można skonfigurować obu typów list. Domyślnie niezależnie od domen nie znajdują się na liście dozwolonych znajdują się na liście odmowy i na odwrót. 
- Można utworzyć tylko jedną zasadę na organizację. Można zaktualizować zasady, aby uwzględnić więcej domen lub można usunąć zasady, aby utworzyć nową. 
- Liczba domen, które można dodać do listy dozwolonych lub listy odmów, jest ograniczona tylko rozmiarem zasad. Maksymalny rozmiar całej zasady to 25 KB (25 000 znaków), która zawiera listę dozwolonych lub listę odmów oraz inne parametry skonfigurowane dla innych funkcji.
- Ta lista działa niezależnie od list dozwolonych/blokowych usługi OneDrive dla Firm i usługi SharePoint Online. Aby ograniczyć udostępnianie poszczególnych plików w usłudze SharePoint Online, należy skonfigurować listę dozwolonych lub odrzucania dla usługi OneDrive dla Firm i usługi SharePoint Online. Aby uzyskać więcej informacji, zobacz [Ograniczone domeny udostępniania w usłudze SharePoint Online i usłudze OneDrive dla Firm](https://support.office.com/article/restricted-domains-sharing-in-sharepoint-online-and-onedrive-for-business-5d7589cd-0997-4a00-a2ba-2320ec49c4e9).
- Lista nie ma zastosowania do użytkowników zewnętrznych, którzy już zrealizowali zaproszenie. Lista zostanie wyegzekwowana po skonfigurowaniu listy. Jeśli zaproszenie użytkownika jest w stanie oczekiwania i ustawisz zasadę, która blokuje ich domenę, próba zrealizowania zaproszenia przez użytkownika zakończy się niepowodzeniem.

## <a name="set-the-allow-or-deny-list-policy-in-the-portal"></a>Ustawianie zasad listy dozwolonych lub odrzucania w portalu

Domyślnie włączone jest ustawienie **Zezwalaj na zaproszenia do dowolnej domeny (najbardziej włącznie).** W takim przypadku można zaprosić użytkowników B2B z dowolnej organizacji.

### <a name="add-a-deny-list"></a>Dodawanie listy odmów

Jest to najbardziej typowy scenariusz, w którym organizacja chce współpracować z niemal każdą organizacją, ale chce uniemożliwić użytkownikom z określonych domen zaproszenie jako użytkowników B2B.

Aby dodać listę odmów:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz**ustawienia użytkownika****użytkowników** >  **usługi Azure Active Directory** > .
3. W obszarze **Użytkownicy zewnętrzni**wybierz pozycję **Zarządzaj ustawieniami współpracy zewnętrznej**.
4. W obszarze **Ograniczenia współpracy**wybierz **pozycję Odmów zaproszenia do określonych domen**.
5. W obszarze **DOMENY DOCELOWE**wprowadź nazwę jednej z domen, które chcesz zablokować. W przypadku wielu domen wprowadź każdą domenę w nowym wierszu. Przykład:

   ![Pokazuje opcję odmowy z dodanymi domenami](./media/allow-deny-list/DenyListSettings.png)
 
6. Po zakończeniu kliknij przycisk **Zapisz**.

Po ustawieniu zasad, jeśli spróbujesz zaprosić użytkownika z zablokowanej domeny, zostanie wyświetlony komunikat informujący, że domena użytkownika jest obecnie zablokowana przez zasady zaproszenia.
 
### <a name="add-an-allow-list"></a>Dodawanie listy dozwolonych

Jest to bardziej restrykcyjna konfiguracja, w której można ustawić określone domeny na liście dozwolonych i ograniczyć zaproszenia do innych organizacji lub domen, które nie są wymienione. 

Jeśli chcesz użyć listy dozwolonych, upewnij się, że spędzasz czas, aby w pełni ocenić, jakie są Twoje potrzeby biznesowe. Jeśli ta zasada będzie zbyt restrykcyjna, użytkownicy mogą wysyłać dokumenty pocztą e-mail lub znaleźć inne sposoby współpracy, które nie są objęte sankcjami IT.


Aby dodać listę dozwolonych:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Wybierz**ustawienia użytkownika****użytkowników** >  **usługi Azure Active Directory** > .
3. W obszarze **Użytkownicy zewnętrzni**wybierz pozycję **Zarządzaj ustawieniami współpracy zewnętrznej**.
4. W obszarze **Ograniczenia współpracy**wybierz pozycję **Zezwalaj na zaproszenia tylko do określonych domen (najbardziej restrykcyjnych).**
5. W obszarze **DOMENY DOCELOWE**wprowadź nazwę jednej z domen, na które chcesz zezwolić. W przypadku wielu domen wprowadź każdą domenę w nowym wierszu. Przykład:

   ![Pokazuje opcję zezwalania z dodanymi domenami](./media/allow-deny-list/AllowListSettings.png)
 
6. Po zakończeniu kliknij przycisk **Zapisz**.

Po ustawieniu zasad, jeśli spróbujesz zaprosić użytkownika z domeny, która nie znajduje się na liście dozwolonych, zostanie wyświetlony komunikat informujący, że domena użytkownika jest obecnie zablokowana przez zasady zaproszenia.

### <a name="switch-from-allow-to-deny-list-and-vice-versa"></a>Przełączanie z listy zezwalania na odmowę i odwrotnie 

Jeśli przełączysz się z jednej zasady do drugiej, spowoduje to odrzucenie istniejącej konfiguracji zasad. Przed wykonaniem przełącznika należy wykonać zapasową szczegółów konfiguracji. 

## <a name="set-the-allow-or-deny-list-policy-using-powershell"></a>Ustawianie zasad listy dozwolonych lub odrzucania przy użyciu programu PowerShell

### <a name="prerequisite"></a>Wymagania wstępne

> [!Note]
> Moduł AzureADPreview nie jest w pełni obsługiwanym modułem, tak jak w wersji zapoznawczej. 

Aby ustawić listę dozwolonych lub odrzucanych przy użyciu programu PowerShell, należy zainstalować wersję zapoznawczą modułu usługi Azure Active Directory dla programu Windows PowerShell. W szczególności należy zainstalować moduł AzureADPreview w wersji 2.0.0.98 lub nowszej.

Aby sprawdzić wersję modułu (i sprawdzić, czy jest zainstalowany):
 
1. Otwórz program Windows PowerShell jako użytkownika z podwyższonym poziomem uprawnień (Uruchom jako administrator). 
2. Uruchom następujące polecenie, aby sprawdzić, czy na komputerze są zainstalowane wersje modułu usługi Azure Active Directory dla programu Windows PowerShell:

   ```powershell  
   Get-Module -ListAvailable AzureAD*
   ```

Jeśli moduł nie jest zainstalowany lub nie masz wymaganej wersji, wykonaj jedną z następujących czynności:

- Jeśli nie zostaną zwrócone żadne wyniki, uruchom następujące polecenie, aby zainstalować najnowszą wersję modułu AzureADPreview:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Jeśli w wynikach jest wyświetlany tylko moduł AzureAD, uruchom następujące polecenia, aby zainstalować moduł AzureADPreview: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Jeśli w wynikach jest wyświetlany tylko moduł AzureADPreview, ale wersja jest mniejsza niż 2.0.0.98, uruchom następujące polecenia, aby go zaktualizować: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
   ```

- Jeśli w wynikach są wyświetlane moduły AzureAD i AzureADPreview, ale wersja modułu AzureADPreview jest mniejsza niż 2.0.0.98, uruchom następujące polecenia, aby go zaktualizować: 

   ```powershell 
   Uninstall-Module AzureAD 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
    ```

### <a name="use-the-azureadpolicy-cmdlets-to-configure-the-policy"></a>Konfigurowanie zasad za pomocą poleceń cmdlet AzureADPolicy

Aby utworzyć listę dozwolonych lub odrzucania, należy użyć polecenia cmdlet [New-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview) W poniższym przykładzie pokazano, jak ustawić listę odmowy, która blokuje domenę "live.com".

```powershell 
$policyValue = @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}")

New-AzureADPolicy -Definition $policyValue -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Poniżej przedstawiono ten sam przykład, ale z definicją zasad wbudowaną.

```powershell  
New-AzureADPolicy -Definition @("{`"B2BManagementPolicy`":{`"InvitationsAllowedAndBlockedDomainsPolicy`":{`"AllowedDomains`": [],`"BlockedDomains`": [`"live.com`"]}}}") -DisplayName B2BManagementPolicy -Type B2BManagementPolicy -IsOrganizationDefault $true 
```

Aby ustawić zasady listy zezwalaj lub odrzucaj, należy użyć polecenia cmdlet [Set-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview) Przykład:

```powershell   
Set-AzureADPolicy -Definition $policyValue -Id $currentpolicy.Id 
```

Aby uzyskać zasady, należy użyć polecenia cmdlet [Get-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) Przykład:

```powershell
$currentpolicy = Get-AzureADPolicy | ?{$_.Type -eq 'B2BManagementPolicy'} | select -First 1 
```

Aby usunąć zasady, należy użyć polecenia cmdlet [Remove-AzureADPolicy.](https://docs.microsoft.com/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview) Przykład:

```powershell
Remove-AzureADPolicy -Id $currentpolicy.Id 
```

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem usługi Azure AD B2B, zobacz [Co to jest współpraca usługi Azure AD B2B?](what-is-b2b.md)
- Aby uzyskać informacje na temat dostępu warunkowego i współpracy B2B, zobacz [Dostęp warunkowy dla użytkowników współpracy B2B](conditional-access.md).



