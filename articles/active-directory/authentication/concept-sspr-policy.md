---
title: Zasady — usługi Azure Active Directory resetowania haseł usługi Azure AD samoobsługi
description: Skonfiguruj opcje zasad resetowania haseł usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d99169fc38f3976b35a0ebbdd6605450fbd3e2e9
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412877"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Zasady dotyczące haseł i ograniczenia dotyczące usługi Azure Active Directory

W tym artykule opisano zasady haseł i wymagań dotyczących złożoności skojarzone z kontami użytkowników w dzierżawie usługi Azure Active Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Administrator zresetować różnice zasad

**Firma Microsoft wymusza silne domyślne *dwóch bram* zasady dla dowolnej roli administratora platformy Azure resetowania hasła** te zasady mogą być inne niż ten, który zostały zdefiniowane dla użytkowników i nie można zmienić. Należy zawsze przetestować funkcji resetowania hasła jako użytkownik bez żadnych przypisanych ról administratora platformy Azure.

Za pomocą zasad dwóch bram **administratorzy nie mają możliwość Użyj pytań zabezpieczających**.

Zasada dwóch bram wymaga dwóch rodzajów danych uwierzytelniania, takich jak **adres e-mail**, **aplikacji authenticator**, lub **numer telefonu**. Zasada dwóch bram ma zastosowanie w następujących okolicznościach:

* Uwzględnione są następujące role administratora platformy Azure:
  * Administrator pomocy technicznej
  * Administrator pomocy technicznej usługi
  * Administrator rozliczeń
  * Pomoc techniczna dla partnerów (warstwa 1)
  * Pomoc techniczna dla partnerów (warstwa 2)
  * Administrator programu Exchange
  * Administrator programu Skype dla firm
  * Administrator użytkownika
  * Zapisywanie katalogów
  * Administrator globalny lub administrator firmy
  * Administrator programu SharePoint
  * Administrator zgodności
  * Administrator aplikacji
  * Administrator zabezpieczeń
  * Administrator ról uprzywilejowanych
  * Administrator usługi Intune
  * Administrator usługi serwera proxy aplikacji
  * Administrator usługi Dynamics 365
  * Administrator usługi Power BI
  * Administrator uwierzytelniania
  * Administrator uprzywilejowanych uwierzytelniania

* Po upływie 30 dni w ramach wersji próbnej subskrypcji; lub
* Domena jest obecny, np. contoso.com; lub
* Usługa Azure AD Connect synchronizuje tożsamości z katalogu lokalnego

### <a name="exceptions"></a>Wyjątki

Zasada jednej bramy wymaga jednego elementu danych uwierzytelniania, takich jak adres e-mail *lub* numer telefonu. Zasada jednej bramy ma zastosowanie w następujących okolicznościach:

* Znajduje się w 30-dniowego okresu próbnego subskrypcji; lub
* Domena nie jest obecny (*. onmicrosoft.com); i
* Program Azure AD Connect nie jest synchronizowanie tożsamości

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName zasady, które są stosowane do wszystkich kont użytkowników

Każdego konta użytkownika, który musi się zalogować do usługi Azure AD musi mieć wartość atrybutu unikatowego użytkownika głównej nazwy (UPN) skojarzonych z ich kontem. W poniższej tabeli przedstawiono zasady, które dotyczą zarówno lokalnych kont użytkowników Active Directory, które są synchronizowane z chmurą oraz do kont użytkowników tylko w chmurze:

| Właściwość | Wymagania dotyczące UserPrincipalName |
| --- | --- |
| Dozwolona liczba znaków |<ul> <li>A – Z</li> <li>z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Znaki nie są dozwolone |<ul> <li>Wszelkie "\@ \" znak, który nie jest oddzielenie nazwy użytkownika z domeny.</li> <li>Nie może zawierać znaku kropki "." bezpośrednio przed "\@ \" symboli</li></ul> |
| Ograniczenia długości |<ul> <li>Łączna długość nie może zawierać znaków 113</li><li>Może to być maksymalnie 64 znaków przed "\@ \" symboli</li><li>Może istnieć maksymalnie 48 znaków po "\@ \" symboli</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Zasady haseł, które mają zastosowanie tylko do kont użytkowników w chmurze

W poniższej tabeli opisano ustawienia zasad haseł, które są stosowane do kont użytkowników, które są tworzone i zarządzane w usłudze Azure AD:

| Właściwość | Wymagania |
| --- | --- |
| Dozwolona liczba znaków |<ul><li>A – Z</li><li>z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / \` ~ " ( ) ;</li></ul> |
| Znaki nie są dozwolone |<ul><li>Znaki Unicode.</li><li>Miejsca do magazynowania.</li><li> Nie może zawierać znaku kropki "." bezpośrednio przed "\@ \" symbol".</li></ul> |
| Ograniczenia haseł |<ul><li>Co najmniej 8 znaków i nie więcej niż 16 znaków.</li><li>Wymaga trzech spośród czterech z następujących czynności:<ul><li>Małe litery.</li><li>Wielkie litery.</li><li>Cyfry (0 – 9).</li><li>Symbole (patrz poprzednie ograniczenia haseł).</li></ul></li></ul> |
| Okres wygasania haseł |<ul><li>Wartość domyślna: **90** dni.</li><li>Wartość jest konfigurowane za pomocą `Set-MsolPasswordPolicy` polecenia cmdlet usługi Azure Active Directory modułu dla Windows PowerShell.</li></ul> |
| Powiadomienie o wygaśnięciu hasła |<ul><li>Wartość domyślna: **14** dni (po których wygasa hasło).</li><li>Wartość jest konfigurowane za pomocą `Set-MsolPasswordPolicy` polecenia cmdlet.</li></ul> |
| Wygaśnięcia hasła |<ul><li>Wartość domyślna: **false** dni (wskazuje, że wygaśnięcie hasła jest włączona).</li><li>Wartość można skonfigurować dla poszczególnych kont użytkowników przy użyciu `Set-MsolUser` polecenia cmdlet.</li></ul> |
| Historię zmian haseł |Ostatnie hasło *nie* można użyć ponownie, gdy użytkownik zmieni hasło. |
| Historia resetowania hasła | Ostatnie hasło *można* można użyć ponownie, gdy użytkownik resetuje zapomniane hasło. |
| Blokada konta |Po 10 nieudanych prób logowania przy użyciu nieprawidłowego hasła użytkownik jest zablokowany przez jedną minutę. Dodatkowo nieprawidłowe próby logowania blokady użytkownika dla zwiększenia czasów trwania czasu. [Blokada Smart](howto-password-smart-lockout.md) śledzi ostatnie trzy skróty nieprawidłowego hasła, aby uniknąć zwiększenie licznika blokady dla tego samego hasła. Jeśli ktoś wprowadzi nieprawidłowe hasło wiele razy, to zachowanie nie spowoduje konta do blokady. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Konfigurowanie zasad wygasania haseł w usłudze Azure AD

Administrator globalny lub administrator użytkowników dla usługi w chmurze firmy Microsoft można użyć modułu Microsoft Azure AD dla programu Windows PowerShell można ustawić hasła użytkowników, aby nie wygaśnie. Można również użyć poleceń cmdlet programu Windows PowerShell do usunięcia nigdy nie — wygasa konfiguracji lub aby zobaczyć, który użytkownik hasła są ustawione na nigdy nie wygasa. 

Niniejsze wytyczne mają zastosowanie do innych dostawców, takich jak usługi Intune i Office 365, które polegają również na usłudze Azure AD dla tożsamości i usługi katalogowe. Wygaśnięcie hasła jest tylko część zasady, które mogą być zmieniane.

> [!NOTE]
> Tylko hasła dla kont użytkowników, które nie są synchronizowane za pomocą synchronizacji katalogów można skonfigurować nie wygaśnie. Aby uzyskać więcej informacji na temat synchronizacji katalogów, zobacz [AD z usługą Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Ustaw lub sprawdź zasady haseł za pomocą programu PowerShell

Aby rozpocząć pracę, musisz [Pobierz i zainstaluj moduł programu Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Po instalacji, można użyć następujące kroki konfigurowania poszczególnych pól.

### <a name="check-the-expiration-policy-for-a-password"></a>Sprawdzanie zasad wygasania haseł

1. Łączenie z programu Windows PowerShell, za pomocą użytkownika administratora sieci lub poświadczenia administratora przedsiębiorstwa.
1. Wykonaj jedną z następujących poleceń:

   * Aby zobaczyć, jeśli ustawiono pojedynczego użytkownika, hasło nigdy nie wygasa, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN (na przykład *aprilr\@contoso.onmicrosoft.com*) lub identyfikator użytkownika ma być sprawdzana:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Aby wyświetlić **hasło nigdy nie wygasa** ustawienia dla wszystkich użytkowników, uruchom następujące polecenie cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Ustaw hasło wygaśnie

1. Łączenie z programu Windows PowerShell, za pomocą użytkownika administratora sieci lub poświadczenia administratora przedsiębiorstwa.
1. Wykonaj jedną z następujących poleceń:

   * Konfigurowanie hasła jednego użytkownika i hasło wygaśnie, należy uruchomić następujące polecenie cmdlet przy użyciu nazwy UPN lub identyfikator użytkownika, użytkownik:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Aby ustawić hasła wszystkich użytkowników w organizacji tak, aby wygasną, należy użyć następującego polecenia cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Ustaw hasło nigdy nie wygasa

1. Łączenie z programu Windows PowerShell, za pomocą użytkownika administratora sieci lub poświadczenia administratora przedsiębiorstwa.
1. Wykonaj jedną z następujących poleceń:

   * Aby ustawić hasło jeden użytkownik nigdy nie wygasa, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN lub identyfikator użytkownika, użytkownik:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Aby ustawić hasła wszystkich użytkowników w organizacji nigdy nie wygasa, uruchom następujące polecenie cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Wartość hasła `-PasswordPolicies DisablePasswordExpiration` nadal wieku na podstawie `pwdLastSet` atrybutu. Jeśli zostały ustawione haseł użytkowników, nigdy nie wygasa, a następnie Przejdź ponad 90 dni, wygaśnięcia hasła. Na podstawie `pwdLastSet` atrybutu, jeśli zmienisz wygaśnięcia do `-PasswordPolicies None`, wszystkie hasła, które mają `pwdLastSet` starsze niż 90 dni, aby użytkownik musiał je zmienić przy następnym zalogowaniu. Ta zmiana może wpływać na dużą liczbę użytkowników. 

## <a name="next-steps"></a>Kolejne kroki

Poniższe artykuły zawierają dodatkowe informacje na temat resetowania haseł za pomocą usługi Azure AD:

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md).
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md).
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś działa niewłaściwie. Jak rozwiązywać problemy z funkcją samoobsługowego resetowania haseł?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)
