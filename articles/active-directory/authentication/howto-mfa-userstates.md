---
title: Multi-Factor Authentication poszczególnych użytkowników — Azure Active Directory
description: Włącz usługę MFA, zmieniając Stany użytkowników w usłudze Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55bba2ff51460a10feabd881458b8d4a15cde924
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74914625"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Jak wymagać weryfikacji dwuetapowej dla użytkownika

W celu przeprowadzenia weryfikacji dwuetapowej można wykonać jedną z dwóch metod, które wymagają użycia konta administratora globalnego. Pierwszą opcją jest włączenie każdego użytkownika dla usługi Azure Multi-Factor Authentication (MFA). Gdy użytkownicy są włączani indywidualnie, przeprowadzają weryfikację dwuetapową przy każdym logowaniu (z pewnymi wyjątkami, takimi jak logowanie przy użyciu zaufanych adresów IP lub włączenie funkcji _zapamiętanych urządzeń_ ). Druga opcja polega na skonfigurowaniu zasad dostępu warunkowego, które wymagają weryfikacji dwuetapowej w określonych warunkach.

> [!TIP]
> Zalecanym podejściem jest włączenie Multi-Factor Authentication platformy Azure korzystającej z zasad dostępu warunkowego. Zmiana stanu użytkowników nie jest już zalecana, chyba że licencje nie uwzględniają dostępu warunkowego, ponieważ będą wymagały od użytkowników wykonania uwierzytelniania MFA przy każdym logowaniu.

## <a name="choose-how-to-enable"></a>Wybieranie sposobu włączania

**Włączone przez zmianę stanu użytkownika** — jest to tradycyjna metoda wymagająca weryfikacji dwuetapowej i została omówiona w tym artykule. Działa ona zarówno z usługą Azure MFA w chmurze, jak i na serwerze usługi Azure MFA. Użycie tej metody wymaga, aby użytkownicy przeprowadzali weryfikację dwuetapową przy **każdym** logowaniu i zastępują zasady dostępu warunkowego.

Włączone przez zasady dostępu warunkowego — jest to najbardziej elastyczny sposób na umożliwienie weryfikacji dwuetapowej dla użytkowników. Włączenie zasad dostępu warunkowego działa tylko dla usługi Azure MFA w chmurze i jest funkcją Premium usługi Azure AD. Więcej informacji na temat tej metody można znaleźć w temacie [wdrażanie usługi Azure Multi-Factor Authentication opartej na chmurze](howto-mfa-getstarted.md).

Włączone przez Azure AD Identity Protection — ta metoda korzysta z zasad ryzyka Azure AD Identity Protection, aby wymagać weryfikacji dwuetapowej na podstawie ryzyka związanego z logowaniem dla wszystkich aplikacji w chmurze. Ta metoda wymaga licencjonowania Azure Active Directory P2. Więcej informacji na temat tej metody można znaleźć w [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Więcej informacji o licencjach i cenach można znaleźć na stronach z cennikiem [usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) i [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) .

## <a name="enable-azure-mfa-by-changing-user-state"></a>Włączanie usługi Azure MFA przez zmianę stanu użytkownika

Konta użytkowników na platformie Azure Multi-Factor Authentication mają trzy różne stany:

> [!IMPORTANT]
> Włączenie usługi Azure MFA za pomocą zasad dostępu warunkowego nie spowoduje zmiany stanu użytkownika. Nie są wyświetlane alarmy, które są wyłączone. Dostęp warunkowy nie zmienia stanu. **Organizacje nie powinny włączać ani wymuszać użytkowników, jeśli korzystają z zasad dostępu warunkowego.**

| Stan | Opis | Uwzględnione aplikacje nie korzystające z przeglądarki | Uwzględnione aplikacje przeglądarki | Zmodyfikowane nowoczesne uwierzytelnianie |
|:---:| --- |:---:|:--:|:--:|
| Disabled (Wyłączony) | Stan domyślny dla nowego użytkownika, który nie jest zarejestrowany w usłudze Azure MFA. | Nie | Nie | Nie |
| Enabled (Włączony) | Użytkownik został zarejestrowany w usłudze Azure MFA, ale nie została zarejestrowana. Otrzymują monit o zarejestrowanie się przy następnym logowaniu. | Nie.  Nadal działają do momentu zakończenia procesu rejestracji. | Tak. Po wygaśnięciu sesji wymagana jest rejestracja w usłudze Azure MFA.| Tak. Po wygaśnięciu tokenu dostępu wymagana jest rejestracja w usłudze Azure MFA. |
| Enforced (Wymuszony) | Użytkownik został zarejestrowany i ukończył proces rejestracji w usłudze Azure MFA. | Tak. Aplikacje wymagają haseł aplikacji. | Tak. Usługa Azure MFA jest wymagana podczas logowania. | Tak. Usługa Azure MFA jest wymagana podczas logowania. |

Stan użytkownika wskazuje, czy administrator zarejestrował je w usłudze Azure MFA, oraz czy ukończył proces rejestracji.

Wszyscy użytkownicy zaczynają *wyłączać*. Po zarejestrowaniu użytkowników w usłudze Azure MFA ich stan zmieni się na *włączone*. Gdy użytkownicy będą mogli się zalogować i zakończyć proces rejestracji, ich stan zmieni się na *wymuszone*.

> [!NOTE]
> Jeśli usługa MFA jest ponownie włączona w obiekcie użytkownika, który ma już szczegóły rejestracji, na przykład telefon lub poczta e-mail, Administratorzy muszą ponownie zarejestrować usługę MFA za pośrednictwem Azure Portal lub PowerShell. Jeśli użytkownik nie zostanie ponownie zarejestrowany, jego stan MFA nie przechodzi z *włączonego* do *wymuszanego* w interfejsie użytkownika zarządzania uwierzytelnianiem usługi MFA.

### <a name="view-the-status-for-a-user"></a>Wyświetlanie stanu użytkownika

Wykonaj następujące kroki, aby uzyskać dostęp do strony, na której można wyświetlać Stany użytkowników i zarządzać nimi:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Wyszukaj i wybierz *Azure Active Directory*. Wybierz pozycję **Użytkownicy** > **Wszyscy użytkownicy**.
3. Wybierz pozycję **Multi-Factor Authentication**.
   ![wybierz Multi-Factor Authentication](./media/howto-mfa-userstates/selectmfa.png)
4. Zostanie wyświetlona nowa strona wyświetlająca Stany użytkownika.
   ![stanu użytkownika usługi uwierzytelniania wieloskładnikowego — zrzut ekranu](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Zmiana stanu użytkownika

1. Wykonaj powyższe kroki, aby przejść do strony **Użytkownicy** usługi Azure Multi-Factor Authentication.
2. Znajdź użytkownika, który ma zostać włączony dla usługi Azure MFA. Może być konieczna zmiana widoku w górnej części strony.
   ![wybierz użytkownika, aby zmienić stan dla na karcie Użytkownicy](./media/howto-mfa-userstates/enable1.png)
3. Zaznacz pole obok nazwy.
4. Po prawej stronie w obszarze **szybkie kroki**wybierz pozycję **Włącz** lub **Wyłącz**.
   ![włączyć wybranego użytkownika, klikając pozycję Włącz w menu szybkie kroki](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Włączone* użytkownicy są automatycznie przełączane w celu *wymuszenia* rejestracji w usłudze Azure MFA. Nie zmieniaj ręcznie stanu użytkownika na *wymuszone*.

5. Potwierdź wybór w otwartym oknie podręcznym.

Po włączeniu użytkowników Powiadom ich pocztą e-mail. Poinformuj ich o konieczności zarejestrowania się podczas następnego logowania. Ponadto, jeśli w organizacji są używane aplikacje nie korzystające z przeglądarki, które nie obsługują nowoczesnego uwierzytelniania, muszą one tworzyć hasła aplikacji. Możesz również dołączyć link do [podręcznika użytkownika końcowego usługi Azure MFA](../user-help/multi-factor-authentication-end-user.md) , aby ułatwić Ci rozpoczęcie pracy.

### <a name="use-powershell"></a>Używanie programu PowerShell

Aby zmienić stan użytkownika przy użyciu [programu Azure AD PowerShell](/powershell/azure/overview), Zmień `$st.State`. Istnieją trzy możliwe stany:

* Enabled (Włączony)
* Enforced (Wymuszony)
* Disabled (Wyłączony)  

Nie przenoś użytkowników bezpośrednio do stanu *wymuszonego* . W takim przypadku aplikacje niekorzystające z przeglądarki przestaną działać, ponieważ użytkownik nie przeszedł rejestrację usługi Azure MFA i uzyskał [hasło aplikacji](howto-mfa-mfasettings.md#app-passwords).

Najpierw zainstaluj moduł, używając:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Nie zapomnij najpierw nawiązać połączenia za pomocą polecenia **Connect-MsolService**

Ten przykładowy skrypt programu PowerShell umożliwia uwierzytelnianie wieloskładnikowe dla pojedynczego użytkownika:

   ```PowerShell
   Import-Module MSOnline
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

Korzystanie z programu PowerShell jest dobrym rozwiązaniem w przypadku konieczności zbiorczego włączania użytkowników. Przykładowo następujący skrypt pętli przez listę użytkowników i włącza uwierzytelnianie wieloskładnikowe na swoich kontach:

   ```PowerShell
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Aby wyłączyć usługę MFA, użyj tego skryptu:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationMethods @()
   ```

który może być również skrócony do:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konwertowanie użytkowników z usługi MFA na użytkownika na podstawie dostępu warunkowego

Poniższe środowisko programu PowerShell może pomóc w konwersji do usługi Azure Multi-Factor Authentication opartej na dostęp warunkowy.

Uruchom to środowisko PowerShell w oknie ISE lub Zapisz jako. Plik PS1 do uruchomienia lokalnego.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Ostatnio zmieniono zachowanie i skrypt programu PowerShell powyżej. Wcześniej skrypt zapisany z metod MFA, wyłączył uwierzytelnianie MFA i przywrócił metody. Nie jest to już konieczne, ponieważ domyślne zachowanie funkcji Disable nie czyści metod.
>
> Jeśli usługa MFA jest ponownie włączona w obiekcie użytkownika, który ma już szczegóły rejestracji, na przykład telefon lub poczta e-mail, Administratorzy muszą ponownie zarejestrować usługę MFA za pośrednictwem Azure Portal lub PowerShell. Jeśli użytkownik nie zostanie ponownie zarejestrowany, jego stan MFA nie przechodzi z *włączonego* do *wymuszanego* w interfejsie użytkownika zarządzania uwierzytelnianiem usługi MFA.

## <a name="next-steps"></a>Następne kroki

* Dlaczego użytkownik był monitowany lub nie ma monitu o przeprowadzenie uwierzytelniania MFA? Zapoznaj się z sekcją [raport dotyczący logowania usługi Azure AD w artykule raporty w usłudze azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Aby skonfigurować dodatkowe ustawienia, takie jak Zaufane adresy IP, niestandardowe wiadomości głosowe i alerty oszustwa, zobacz artykuł [Konfigurowanie ustawień usługi Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
* Informacje o zarządzaniu ustawieniami użytkownika w usłudze Azure Multi-Factor Authentication można znaleźć w artykule [Zarządzanie ustawieniami użytkownika w usłudze azure Multi-Factor Authentication w chmurze](howto-mfa-userdevicesettings.md)
