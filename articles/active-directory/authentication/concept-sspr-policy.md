---
title: Zasady samoobsługowego resetowania haseł — Azure Active Directory
description: Skonfiguruj opcje zasad samoobsługowego resetowania hasła w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd6cacae9c7af705b0de7b59e0f25f25637a5a89
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376933"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Zasady i ograniczenia haseł w Azure Active Directory

W tym artykule opisano zasady haseł i wymagania dotyczące złożoności skojarzone z kontami użytkowników w dzierżawie usługi Azure Active Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Zasady resetowania ustawień administratora

**Firma Microsoft wymusza silne domyślne zasady *dwubramowego* resetowania hasła dla dowolnej roli administratora platformy Azure** , ponieważ te zasady mogą się różnić od tych, które zostały zdefiniowane dla użytkowników i nie można ich zmienić. Należy zawsze testować funkcję resetowania hasła jako użytkownik bez przypisanych ról administratora platformy Azure.

W przypadku zasad dwubramowych **administratorzy nie mogą korzystać z pytań zabezpieczających**.

Zasady dwóch bram wymagają dwóch danych uwierzytelniania, takich jak **adres e-mail**, **aplikacja uwierzytelniania**lub **numer telefonu**. Zasady dwóch bram są stosowane w następujących okolicznościach:

* Dotyczy to wszystkich następujących ról administratora platformy Azure:
  * Administrator pomocy technicznej
  * Administrator pomocy technicznej usługi
  * Administrator rozliczeń
  * Pomoc techniczna dla partnerów (warstwa 1)
  * Pomoc techniczna dla partnerów (warstwa 2)
  * Administrator programu Exchange
  * Administrator programu Skype dla firm
  * Administrator użytkowników
  * Autorzy katalogów
  * Administrator globalny lub administrator firmy
  * Administrator programu SharePoint
  * Administrator zgodności
  * Administrator aplikacji
  * Administrator zabezpieczeń
  * Administrator ról uprzywilejowanych
  * Administrator usługi Intune
  * Administrator usługi serwera proxy aplikacji
  * Administrator systemu Dynamics 365
  * usługa Power BI administrator
  * Administrator uwierzytelniania
  * Administrator uprzywilejowanego uwierzytelniania

* W przypadku upływu 30 dni od subskrypcji próbnej oraz
* Skonfigurowano domenę niestandardową dla dzierżawy usługi Azure AD, np. *contoso.com*; oraz
* Azure AD Connect synchronizuje tożsamości z katalogu lokalnego

### <a name="exceptions"></a>Wyjątki

Zasady pojedynczej bramy wymagają jednej części danych uwierzytelniania, takich jak adres e-mail *lub* numer telefonu. Zasady pojedynczej bramy są stosowane w następujących okolicznościach:

* Jest to w ciągu pierwszych 30 dni subskrypcji wersji próbnej; oraz
* Domena niestandardowa nie została skonfigurowana dla dzierżawy usługi Azure AD, więc jest używana wartość domyślna * *. onmicrosoft.com*. Należy pamiętać, że domyślna domena * *. onmicrosoft.com* nie jest zalecana do użycia w środowisku produkcyjnym; lub
* Azure AD Connect nie synchronizuje tożsamości

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Zasady UserPrincipalName mające zastosowanie do wszystkich kont użytkowników

Każde konto użytkownika, które musi zalogować się do usługi Azure AD, musi mieć unikatową wartość atrybutu nazwy głównej użytkownika (UPN) skojarzoną z danym kontem. Poniższa tabela zawiera opis zasad dotyczących zarówno Active Directory lokalnych kont użytkowników, które są synchronizowane z chmurą, jak i konta użytkowników tylko w chmurze:

| Właściwość | Wymagania UserPrincipalName |
| --- | --- |
| Dozwolone znaki |<ul> <li>A – Z</li> <li>a – z</li><li>0 – 9</li> <li> ' \. - \_! \# ^ \~</li></ul> |
| Znaki nie są dozwolone |<ul> <li>"\@\" znak, który nie oddziela nazwy użytkownika z domeny.</li> <li>Nie może zawierać znaku kropki "." bezpośrednio przed symbolem "\@\"</li></ul> |
| Ograniczenia długości |<ul> <li>Łączna długość nie może przekraczać 113 znaków</li><li>Przed symbolem "\@\"" może znajdować się maksymalnie 64 znaków</li><li>Po znaku "\@\"" może znajdować się maksymalnie 48 znaków</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Zasady dotyczące haseł mające zastosowanie tylko do kont użytkowników w chmurze

W poniższej tabeli opisano ustawienia zasad haseł stosowane do kont użytkowników, które są tworzone i zarządzane w usłudze Azure AD:

| Właściwość | Wymagania |
| --- | --- |
| Dozwolone znaki |<ul><li>A – Z</li><li>a – z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / \` ~ "();</li> <li>puste miejsce</li></ul> |
| Znaki nie są dozwolone | Znaki Unicode. |
| Ograniczenia dotyczące haseł |<ul><li>Co najmniej 8 znaków i maksymalnie 256 znaków.</li><li>Wymaga trzech z czterech następujących elementów:<ul><li>Małe litery.</li><li>Wielkie litery.</li><li>Liczby (0-9).</li><li>Symbole (zobacz poprzednie ograniczenia dotyczące haseł).</li></ul></li></ul> |
| Czas wygaśnięcia hasła (maksymalny wiek hasła) |<ul><li>Wartość domyślna: **90** dni.</li><li>Wartość można skonfigurować za pomocą polecenia cmdlet `Set-MsolPasswordPolicy` w module Azure Active Directory dla środowiska Windows PowerShell.</li></ul> |
| Powiadomienie o wygaśnięciu hasła (gdy użytkownicy są powiadamiani o wygaśnięciu hasła) |<ul><li>Wartość domyślna: **14** dni (przed wygaśnięciem hasła).</li><li>Wartość można skonfigurować za pomocą polecenia cmdlet `Set-MsolPasswordPolicy`.</li></ul> |
| Wygaśnięcie hasła (niech hasło nigdy nie wygasa) |<ul><li>Wartość domyślna: **false** (wskazuje, że hasło ma datę wygaśnięcia).</li><li>Wartość można skonfigurować dla poszczególnych kont użytkowników przy użyciu polecenia cmdlet `Set-MsolUser`.</li></ul> |
| Historia zmian haseł | *Nie* można ponownie użyć ostatniego hasła, gdy użytkownik zmieni hasło. |
| Historia resetowania hasła | Ostatniego hasła *można* użyć ponownie, gdy użytkownik resetuje zapomniane hasło. |
| Blokada konta | Po 10 nieudanych próbach logowania z nieprawidłowym hasłem użytkownik jest blokowany przez jedną minutę. Więcej nieprawidłowych prób logowania Zablokuj użytkownika w celu zwiększenia czasu trwania. [Inteligentna blokada](howto-password-smart-lockout.md) śledzi ostatnie trzy niewłaściwe skróty haseł, aby uniknąć zwiększenia licznika blokady dla tego samego hasła. Jeśli ktoś wprowadzi to samo złe hasło wielokrotnie, to zachowanie nie spowoduje zablokowania konta. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Ustawianie zasad wygasania haseł w usłudze Azure AD

Administrator globalny lub administrator użytkownika usługi firmy Microsoft w chmurze może użyć Moduł Microsoft Azure AD dla Windows PowerShell, aby ustawić hasła użytkowników, które nie wygasną. Można również użyć poleceń cmdlet programu Windows PowerShell, aby usunąć konfigurację nigdy nie wygasa lub zobaczyć, które hasła użytkowników są ustawione na nigdy nie wygasają. 

Te wskazówki odnoszą się do innych dostawców, takich jak usługa Intune i pakiet Office 365, które również polegają na usłudze Azure AD na potrzeby tożsamości i usług katalogowych. Wygaśnięcie hasła jest jedyną częścią zasad, które można zmienić.

> [!NOTE]
> Tylko hasła kont użytkowników, które nie są synchronizowane za poorednictwem synchronizacji katalogów, można skonfigurować tak, aby nie wygaśnie. Aby uzyskać więcej informacji na temat synchronizacji katalogów, zobacz [łączenie usługi AD z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Ustawianie lub sprawdzanie zasad haseł przy użyciu programu PowerShell

Aby rozpocząć, musisz [pobrać i zainstalować moduł Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Po zainstalowaniu programu można skonfigurować każde pole przy użyciu poniższych kroków.

### <a name="check-the-expiration-policy-for-a-password"></a>Sprawdź zasady wygasania hasła

1. Nawiązywanie połączenia z programem Windows PowerShell przy użyciu poświadczeń administratora użytkownika lub administratora firmy.
1. Wykonaj jedno z następujących poleceń:

   * Aby sprawdzić, czy hasło jednego użytkownika jest ustawione na nigdy nie wygasa, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN (na przykład *kwiecień\@contoso.onmicrosoft.com*) lub identyfikatora użytkownika, który chcesz sprawdzić:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Aby wyświetlić ustawienie **hasło nigdy nie wygasa** dla wszystkich użytkowników, uruchom następujące polecenie cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Ustaw hasło na wygasające

1. Nawiązywanie połączenia z programem Windows PowerShell przy użyciu poświadczeń administratora użytkownika lub administratora firmy.
1. Wykonaj jedno z następujących poleceń:

   * Aby ustawić hasło jednego użytkownika w taki sposób, aby hasło wygasło, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN lub identyfikatora użytkownika użytkownika:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Aby ustawić hasła wszystkich użytkowników w organizacji w taki sposób, aby wygaśnie, użyj następującego polecenia cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Ustaw hasło nigdy nie wygasa

1. Nawiązywanie połączenia z programem Windows PowerShell przy użyciu poświadczeń administratora użytkownika lub administratora firmy.
1. Wykonaj jedno z następujących poleceń:

   * Aby ustawić hasło jednego użytkownika tak, aby nigdy nie wygasło, uruchom następujące polecenie cmdlet przy użyciu nazwy UPN lub identyfikatora użytkownika użytkownika:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Aby ustawić hasła wszystkich użytkowników w organizacji, aby nigdy nie wygasnąć, uruchom następujące polecenie cmdlet:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Hasła ustawione na `-PasswordPolicies DisablePasswordExpiration` nadal wiek na podstawie atrybutu `pwdLastSet`. Jeśli ustawisz hasła użytkowników, aby nigdy nie wygasnąć, a następnie po 90 dniach zostanie wystawione hasło. W oparciu o `pwdLastSet` atrybutu, w przypadku zmiany czasu wygaśnięcia na `-PasswordPolicies None`, wszystkie hasła, które mają `pwdLastSet` starsze niż 90 dni, wymagają od użytkownika zmiany ich przy następnym logowaniu. Ta zmiana może mieć wpływ na dużą liczbę użytkowników.

## <a name="next-steps"></a>Następne kroki

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
* [Myślę, że coś jest zerwane. Jak mogę Rozwiązywanie problemów z SSPR?](active-directory-passwords-troubleshoot.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)
