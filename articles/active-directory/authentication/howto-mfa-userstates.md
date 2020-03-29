---
title: Uwierzytelnianie wieloskładnikowe dla użytkowników — usługa Azure Active Directory
description: Włącz usługę MFA, zmieniając stany użytkowników w usłudze Azure Multi-Factor Authentication.
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
ms.openlocfilehash: 61d7227c57422cfe2228002750ec29bffa385d44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756774"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Jak wymagać weryfikacji dwuetapowej dla użytkownika

Można podjąć jedną z dwóch metod wymagających weryfikacji dwuetapowej, z których oba wymagają przy użyciu konta administratora globalnego. Pierwszą opcją jest włączenie każdego użytkownika dla usługi Azure Multi-Factor Authentication (MFA). Gdy użytkownicy są włączeni indywidualnie, przeprowadzają weryfikację dwuetapową za każdym razem, gdy się logują (z pewnymi wyjątkami, na przykład podczas logowania się z zaufanych adresów IP lub gdy funkcja _zapamiętanych urządzeń_ jest włączona). Drugą opcją jest skonfigurowanie zasad dostępu warunkowego, która wymaga weryfikacji dwuetapowej pod pewnymi warunkami.

> [!TIP]
> Włączenie uwierzytelniania wieloskładnikowego platformy Azure przy użyciu zasad dostępu warunkowego jest zalecane podejście. Zmiana stanu użytkownika nie jest już zalecana, chyba że licencje nie zawierają dostępu warunkowego, ponieważ będzie wymagać od użytkowników wykonywania usługi MFA za każdym razem, gdy się zalogują.

## <a name="choose-how-to-enable"></a>Wybieranie sposobu włączania

**Włączone przez zmianę stanu użytkownika** — jest to tradycyjna metoda wymaga weryfikacji dwuetapowej i została omówiona w tym artykule. Działa zarówno z usługi Azure MFA w chmurze i usługi Azure MFA Server. Za pomocą tej metody wymaga od użytkowników do wykonywania weryfikacji dwuetapowej **za każdym razem, gdy** się logują i zastępuje zasady dostępu warunkowego.

**Włączone przez zasady dostępu warunkowego** — jest to najbardziej elastyczny sposób, aby umożliwić weryfikację dwuetapową dla użytkowników. Włączenie przy użyciu zasad dostępu warunkowego działa tylko dla usługi Azure MFA w chmurze i jest funkcją premium usługi Azure AD. Więcej informacji na temat tej metody można znaleźć w [obszarze Wdrażanie opartego na chmurze uwierzytelniania wieloskładnikowego platformy Azure.](howto-mfa-getstarted.md)

**Włączona przez usługę Azure AD Identity Protection** — ta metoda używa zasad ryzyka usługi Azure AD Identity Protection, aby wymagać weryfikacji dwuetapowej na podstawie ryzyka logowania dla wszystkich aplikacji w chmurze. Ta metoda wymaga licencjonowania usługi Azure Active Directory P2. Więcej informacji na temat tej metody można znaleźć w [usłudze Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Więcej informacji na temat licencji i cen można znaleźć na stronach cenowych [usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) i [uwierzytelniania wieloskładnikowego.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

## <a name="enable-azure-mfa-by-changing-user-state"></a>Włączanie usługi Azure MFA przez zmianę stanu użytkownika

Konta użytkowników w usłudze Azure Multi-Factor Authentication mają następujące trzy różne stany:

> [!IMPORTANT]
> Włączenie usługi Azure MFA za pośrednictwem zasad dostępu warunkowego nie zmieni stanu użytkownika. Nie zaalarmuj użytkowników, którzy są wyłączeni. Dostęp warunkowy nie zmienia stanu. **Organizacje nie powinny włączać ani wymuszać użytkowników, jeśli korzystają z zasad dostępu warunkowego.**

| Stan | Opis | Dotyczy to aplikacji innych niż przeglądarki | Dotyczy to aplikacji przeglądarki | Nowoczesne uwierzytelnianie, którego dotyczy problem |
|:---:| --- |:---:|:--:|:--:|
| Disabled (Wyłączony) | Domyślny stan dla nowego użytkownika nie zarejestrowanego w usłudze Azure MFA. | Nie | Nie | Nie |
| Enabled (Włączony) | Użytkownik został zarejestrowany w usłudze Azure MFA, ale nie został zarejestrowany. Otrzymują monit o zarejestrowanie się przy następnym loguchi. | Nie.  Kontynuują pracę do czasu zakończenia procesu rejestracji. | Tak. Po wygaśnięciu sesji wymagana jest rejestracja usługi Azure MFA.| Tak. Po wygaśnięciu tokenu dostępu wymagana jest rejestracja usługi Azure MFA. |
| Enforced (Wymuszony) | Użytkownik został zarejestrowany i zakończył proces rejestracji usługi Azure MFA. | Tak. Aplikacje wymagają haseł aplikacji. | Tak. Usługa Azure MFA jest wymagana przy logowaniu. | Tak. Usługa Azure MFA jest wymagana przy logowaniu. |

Stan użytkownika odzwierciedla, czy administrator zarejestrował je w usłudze Azure MFA i czy zakończył proces rejestracji.

Wszyscy użytkownicy zaczynają *wyłączone*. Podczas rejestrowania użytkowników w usłudze Azure MFA ich stan zmienia się na *Włączone*. Gdy włączono użytkowników zalogować się i zakończyć proces rejestracji, ich stan zmienia się *na Wymuszone*.

> [!NOTE]
> Jeśli usługa MFA jest ponownie włączona w obiekcie użytkownika, który ma już szczegóły rejestracji, takie jak telefon lub poczta e-mail, administratorzy muszą ponownie zarejestrować usługę MFA tego użytkownika za pośrednictwem witryny Azure portal lub programu PowerShell. Jeśli użytkownik nie zarejestruje się ponownie, ich stan usługi MFA nie przechodzi z *Włączone* do *wymuszone* w interfejsie użytkownika zarządzania zarządzania usługi MFA.

### <a name="view-the-status-for-a-user"></a>Wyświetlanie stanu użytkownika

Aby uzyskać dostęp do strony, na której można wyświetlać stany użytkowników i zarządzać nimi, należy wykonać następujące czynności:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator.
2. Wyszukaj i wybierz pozycję *Azure Active Directory*. Wybierz **pozycję Użytkownicy** > **Wszyscy użytkownicy**.
3. Wybierz pozycję **Uwierzytelnianie wieloskładnikowe**. Może być konieczne przewinięcie w prawo, aby wyświetlić tę opcję menu. Wybierz przykładowy zrzut ekranu poniżej, aby wyświetlić pełne okno witryny Azure portal i lokalizację menu:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Wybieranie uwierzytelniania wieloskładnikowego w oknie Użytkownicy w usłudze Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
4. Zostanie otwarta nowa strona z wyświetlaniem stanów użytkownika.
   ![stan użytkownika uwierzytelniania wieloskładnikowego — zrzut ekranu](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Zmienianie stanu użytkownika

1. Użyj powyższych kroków, aby przejść do strony **użytkowników** uwierzytelniania wieloskładnikowego platformy Azure.
2. Znajdź użytkownika, którego chcesz włączyć dla usługi Azure MFA. Może być konieczna zmiana widoku u góry.
   ![Wybierz użytkownika, dla niego, aby zmienić stan na karcie Użytkownicy](./media/howto-mfa-userstates/enable1.png)
3. Zaznacz pole obok ich nazwy.
4. Po prawej stronie w obszarze **Szybkie kroki**wybierz pozycję **Włącz** lub **Wyłącz**.
   ![Włącz wybranego użytkownika, klikając włącz w menu szybkie kroki](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Włączono* użytkowników są automatycznie przełączane do *wymuszone* podczas rejestracji usługi Azure MFA. Nie należy ręcznie zmieniać stanu użytkownika na *Wymuszony*.

5. Potwierdź swój wybór w otwieranych oknach podręcznych.

Po włączeniu użytkowników powiadom ich za pośrednictwem poczty e-mail. Poinformuj ich, że zostaną poproszeni o zarejestrowanie się przy następnym loguchi. Ponadto jeśli organizacja używa aplikacji innych niż przeglądarki, które nie obsługują nowoczesnego uwierzytelniania, muszą utworzyć hasła aplikacji. Można również dołączyć łącze do [podręcznika użytkownika końcowego usługi Azure MFA,](../user-help/multi-factor-authentication-end-user.md) aby ułatwić im rozpoczęcie pracy.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell

Aby zmienić stan użytkownika przy użyciu programu `$st.State`Azure AD [PowerShell,](/powershell/azure/overview)zmień plik . Istnieją trzy możliwe stany:

* Enabled (Włączony)
* Enforced (Wymuszony)
* Disabled (Wyłączony)  

Nie przenosij użytkowników bezpośrednio do stanu *Wymuszone.* Jeśli to zrobisz, aplikacje nie oparte na przeglądarce przestaną działać, ponieważ użytkownik nie przeszedł rejestracji usługi Azure MFA i uzyskał [hasło aplikacji](howto-mfa-mfasettings.md#app-passwords).

Najpierw zainstaluj moduł, używając:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Nie zapomnij połączyć się najpierw za pomocą **Connect-MsolService**

   ```PowerShell
   Connect-MsolService
   ```

W tym przykładzie skrypt programu PowerShell umożliwia uwierzytelnianie wieloskładnikowe dla poszczególnych użytkowników:

   ```PowerShell
   Import-Module MSOnline
   Connect-MsolService
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

Korzystanie z programu PowerShell jest dobrym rozwiązaniem, gdy trzeba zbiorczo włączyć użytkowników. Na przykład następujący skrypt pętli za pośrednictwem listy użytkowników i umożliwia mfa na swoich kontach:

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
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
   ```

które można również skrócić do:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konwertowanie użytkowników z usługi MFA na użytkownika na uwierzytelnianie wieloskładnikowe oparte na dostępie warunkowym

Poniższy program PowerShell może pomóc w konwersji do usługi Azure multi-factor authentication oparty na usłudze Azure.

Uruchom ten program PowerShell w oknie ISE lub zapisz jako . ps1, aby uruchomić lokalnie.

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
> Niedawno zmieniliśmy zachowanie i skrypt programu PowerShell powyżej odpowiednio. Wcześniej skrypt zapisane poza metody uwierzytelniania Wieloskładnikowego, wyłączone mfa i przywrócone metody. Nie jest to już konieczne teraz, gdy domyślne zachowanie wyłączenia nie wyczyści ć metod.
>
> Jeśli usługa MFA jest ponownie włączona w obiekcie użytkownika, który ma już szczegóły rejestracji, takie jak telefon lub poczta e-mail, administratorzy muszą ponownie zarejestrować usługę MFA tego użytkownika za pośrednictwem witryny Azure portal lub programu PowerShell. Jeśli użytkownik nie zarejestruje się ponownie, ich stan usługi MFA nie przechodzi z *Włączone* do *wymuszone* w interfejsie użytkownika zarządzania zarządzania usługi MFA.

## <a name="next-steps"></a>Następne kroki

* Dlaczego użytkownik został poproszony lub nie został poproszony o wykonanie usługi MFA? Zobacz sekcję [Raport logowania usługi Azure AD w raporcie Raporty w dokumencie uwierzytelniania wieloskładnikowego platformy Azure](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Aby skonfigurować dodatkowe ustawienia, takie jak zaufane pliki IP, niestandardowe wiadomości głosowe i alerty o oszustwach, zobacz artykuł [Konfigurowanie ustawień uwierzytelniania wieloskładnikowego platformy Azure](howto-mfa-mfasettings.md)
* Informacje dotyczące zarządzania ustawieniami użytkownika dla uwierzytelniania wieloskładnikowego platformy Azure można znaleźć w artykule [Zarządzanie ustawieniami użytkownika za pomocą uwierzytelniania wieloskładnikowego platformy Azure w chmurze](howto-mfa-userdevicesettings.md)
