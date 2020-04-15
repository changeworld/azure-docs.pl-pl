---
title: Włączanie uwierzytelniania wieloskładnikowego dla użytkownika — usługa Azure Active Directory
description: Dowiedz się, jak włączyć uwierzytelnianie wieloskładnikowe platformy Azure dla użytkowników, zmieniając stan użytkownika
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8ceaf13324864c7ec3df731c3e710815b0eba9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309778"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Włącz uwierzytelnianie wieloskładnikowe platformy Azure dla użytkowników w celu zabezpieczenia zdarzeń logowania

Istnieją dwa sposoby zabezpieczania zdarzeń logowania użytkownika, wymagając uwierzytelniania wieloskładnikowego w usłudze Azure AD. Pierwszą i preferowaną opcją jest skonfigurowanie zasad dostępu warunkowego, która wymaga uwierzytelniania wieloskładnikowego pod pewnymi warunkami. Drugą opcją jest włączenie każdego użytkownika dla uwierzytelniania wieloskładnikowego platformy Azure. Gdy użytkownicy są włączeni indywidualnie, wykonują uwierzytelnianie wieloskładnikowe za każdym razem, gdy się logują (z pewnymi wyjątkami, na przykład podczas logowania się z zaufanych adresów IP lub gdy funkcja _zapamiętanych urządzeń_ jest włączona).

> [!NOTE]
> Włączenie uwierzytelniania wieloskładnikowego platformy Azure przy użyciu zasad dostępu warunkowego jest zalecane podejście. Zmiana stanu użytkownika nie jest już zalecana, chyba że licencje nie zawierają dostępu warunkowego, ponieważ wymaga to od użytkowników wykonywania usługi MFA za każdym razem, gdy się logują.
>
> Aby rozpocząć korzystanie z dostępu warunkowego, zobacz [Samouczek: Bezpieczne zdarzenia logowania użytkownika za pomocą uwierzytelniania wieloskładnikowego platformy Azure](tutorial-enable-azure-mfa.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Stany użytkownika uwierzytelniania wieloskładnikowego platformy Azure

Konta użytkowników w usłudze Azure Multi-Factor Authentication mają następujące trzy różne stany:

> [!IMPORTANT]
> Włączenie uwierzytelniania wieloskładnikowego platformy Azure za pomocą zasad dostępu warunkowego nie zmienia stanu użytkownika. Nie przejmuj się, jeśli użytkownicy są wyłączeni. Dostęp warunkowy nie zmienia stanu.
>
> **Nie należy włączać ani wymuszać użytkowników, jeśli używasz zasad dostępu warunkowego.**

| Stan | Opis | Dotyczy to aplikacji innych niż przeglądarki | Dotyczy to aplikacji przeglądarki | Nowoczesne uwierzytelnianie, którego dotyczy problem |
|:---:| --- |:---:|:--:|:--:|
| Disabled (Wyłączony) | Domyślny stan dla nowego użytkownika nie zarejestrowanego w usłudze Azure Multi-Factor Authentication. | Nie | Nie | Nie |
| Enabled (Włączony) | Użytkownik został zarejestrowany w usłudze Azure Multi-Factor Authentication, ale nie został zarejestrowany. Otrzymują monit o zarejestrowanie się przy następnym loguchi. | Nie.  Kontynuują pracę do czasu zakończenia procesu rejestracji. | Tak. Po wygaśnięciu sesji wymagana jest rejestracja uwierzytelniania wieloskładnikowego platformy Azure.| Tak. Po wygaśnięciu tokenu dostępu wymagana jest rejestracja uwierzytelniania wieloskładnikowego platformy Azure. |
| Enforced (Wymuszony) | Użytkownik został zarejestrowany i zakończył proces rejestracji dla usługi Azure Uwierzytelnianie wieloskładnikowe. | Tak. Aplikacje wymagają haseł aplikacji. | Tak. Uwierzytelnianie wieloskładnikowe platformy Azure jest wymagane przy logowaniu. | Tak. Uwierzytelnianie wieloskładnikowe platformy Azure jest wymagane przy logowaniu. |

Stan użytkownika odzwierciedla, czy administrator zarejestrował je w usłudze Azure Multi-Factor Authentication i czy zakończył proces rejestracji.

Wszyscy użytkownicy zaczynają *wyłączone*. Podczas rejestrowania użytkowników w usłudze Azure Multi-Factor Authentication ich stan zmienia się na *Włączone*. Gdy włączono użytkowników zalogować się i zakończyć proces rejestracji, ich stan zmienia się *na Wymuszone*.

> [!NOTE]
> Jeśli usługa MFA jest ponownie włączona w obiekcie użytkownika, który ma już szczegóły rejestracji, takie jak telefon lub poczta e-mail, administratorzy muszą ponownie zarejestrować usługę MFA tego użytkownika za pośrednictwem witryny Azure portal lub programu PowerShell. Jeśli użytkownik nie zarejestruje się ponownie, ich stan usługi MFA nie przechodzi z *Włączone* do *wymuszone* w interfejsie użytkownika zarządzania zarządzania usługi MFA.

## <a name="view-the-status-for-a-user"></a>Wyświetlanie stanu użytkownika

Skorzystaj z następujących kroków, aby uzyskać dostęp do strony portalu Azure, na której można wyświetlać stany użytkowników i zarządzać nimi:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako administrator.
1. Wyszukaj i wybierz *pozycję Azure Active Directory*, a następnie wybierz **pozycję Użytkownicy** > **Wszyscy użytkownicy**.
1. Wybierz pozycję **Uwierzytelnianie wieloskładnikowe**. Może być konieczne przewinięcie w prawo, aby wyświetlić tę opcję menu. Wybierz przykładowy zrzut ekranu poniżej, aby wyświetlić pełne okno witryny Azure portal i lokalizację menu:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Wybieranie uwierzytelniania wieloskładnikowego w oknie Użytkownicy w usłudze Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Zostanie otwarta nowa strona, która wyświetla stan użytkownika, jak pokazano w poniższym przykładzie.
   ![Zrzut ekranu przedstawiający przykładowe informacje o stanie użytkownika dla uwierzytelniania wieloskładnikowego platformy Azure](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Zmienianie stanu użytkownika

Aby zmienić stan uwierzytelniania wieloskładnikowego platformy Azure dla użytkownika, wykonaj następujące kroki:

1. Użyj powyższych kroków, aby przejść do strony **użytkowników** uwierzytelniania wieloskładnikowego platformy Azure.
1. Znajdź użytkownika, którego chcesz włączyć dla uwierzytelniania wieloskładnikowego platformy Azure. Może być konieczna zmiana widoku u góry na **użytkowników.**
   ![Wybierz użytkownika, dla niego, aby zmienić stan na karcie Użytkownicy](./media/howto-mfa-userstates/enable1.png)
1. Zaznacz pole wyboru obok nazw użytkowników, dla którzy zmienią stan.
1. Po prawej stronie w obszarze **szybkie kroki**wybierz pozycję **Włącz** lub **Wyłącz**. W poniższym przykładzie użytkownik *John Smith* ma czek obok swojej nazwy ![i jest włączony do użycia: Włącz wybranego użytkownika, klikając włącz menu szybkie kroki](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Włączeni* użytkownicy są automatycznie przełączane do *wymuszone* podczas rejestracji do azure uwierzytelnianie wieloskładnikowe. Nie zmieniaj ręcznie stanu użytkownika na *Wymuszony*.

1. Potwierdź swój wybór w otwieranych oknach podręcznych.

Po włączeniu użytkowników powiadom ich za pośrednictwem poczty e-mail. Poinformuj użytkowników, że wyświetlany jest monit z prośbą o zarejestrowanie się przy następnym loguchi. Ponadto jeśli organizacja używa aplikacji innych niż przeglądarki, które nie obsługują nowoczesnego uwierzytelniania, muszą utworzyć hasła aplikacji. Aby uzyskać więcej informacji, zobacz [podręcznik użytkownika końcowego uwierzytelniania wieloskładnikowego platformy Azure,](../user-help/multi-factor-authentication-end-user.md) aby ułatwić im rozpoczęcie pracy.

## <a name="change-state-using-powershell"></a>Zmienianie stanu przy użyciu programu PowerShell

Aby zmienić stan użytkownika przy użyciu programu Azure `$st.State` AD [PowerShell,](/powershell/azure/overview)należy zmienić parametr dla konta użytkownika. Istnieją trzy możliwe stany dla konta użytkownika:

* *Enabled (Włączony)*
* *Enforced (Wymuszony)*
* *Wyłączone*  

Nie przenosij użytkowników bezpośrednio do stanu *Wymuszone.* Jeśli to zrobisz, aplikacje nie oparte na przeglądarce przestaną działać, ponieważ użytkownik nie przeszedł rejestracji uwierzytelniania wieloskładnikowego platformy Azure i uzyskał [hasło aplikacji.](howto-mfa-mfasettings.md#app-passwords)

Aby rozpocząć, należy zainstalować moduł *MSOnline* przy użyciu [install-module](/powershell/module/powershellget/install-module) w następujący sposób:

```PowerShell
Install-Module MSOnline
```

Następnie połącz się za pomocą [Connect-MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

Poniższy przykładowy skrypt programu PowerShell umożliwia *bsimon@contoso.com*uwierzytelnianie wieloskładnikowe dla indywidualnego użytkownika o nazwie:

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Korzystanie z programu PowerShell jest dobrym rozwiązaniem, gdy trzeba zbiorczo włączyć użytkowników. Poniższy skrypt pętli za pośrednictwem listy użytkowników i umożliwia mfa na swoich kontach. Zdefiniuj konta użytkowników ustaw `$users` go w pierwszym wierszu w następujący sposób:

   ```PowerShell
   # Define your list of users to update state in bulk
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

Aby wyłączyć usługę MFA, poniższy przykład pobiera użytkownika z [Get-MsolUser](/powershell/module/msonline/get-msoluser), a następnie usuwa wszelkie *Wymagania StrongAuthentication* zestaw dla zdefiniowanego użytkownika za pomocą [Set-MsolUser:](/powershell/module/msonline/set-msoluser)

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Można również bezpośrednio wyłączyć mfa dla użytkownika za pomocą [Set-MsolUser](/powershell/module/msonline/set-msoluser) w następujący sposób:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konwertowanie użytkowników z usługi MFA na użytkownika na uwierzytelnianie wieloskładnikowe oparte na dostępie warunkowym

Poniższy program PowerShell może pomóc w konwersji do usługi Azure multi-factor authentication oparty na usłudze Azure.

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
> Niedawno zmieniliśmy zachowanie i ten skrypt programu PowerShell. Wcześniej skrypt zapisane poza metody uwierzytelniania Wieloskładnikowego, wyłączone mfa i przywrócone metody. Nie jest to już konieczne teraz, gdy domyślne zachowanie wyłączenia nie wyczyści ć metod.
>
> Jeśli usługa MFA jest ponownie włączona w obiekcie użytkownika, który ma już szczegóły rejestracji, takie jak telefon lub poczta e-mail, administratorzy muszą ponownie zarejestrować usługę MFA tego użytkownika za pośrednictwem witryny Azure portal lub programu PowerShell. Jeśli użytkownik nie zarejestruje się ponownie, ich stan usługi MFA nie przechodzi z *Włączone* do *wymuszone* w interfejsie użytkownika zarządzania zarządzania usługi MFA.

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować ustawienia uwierzytelniania wieloskładnikowego platformy Azure, takie jak zaufane pliki IP, niestandardowe wiadomości głosowe i alerty o oszustwach, zobacz [Konfigurowanie ustawień uwierzytelniania wieloskładnikowego platformy Azure](howto-mfa-mfasettings.md). Aby zarządzać ustawieniami użytkownika uwierzytelniania wieloskładnikowego platformy Azure, zobacz [Zarządzanie ustawieniami użytkownika za pomocą uwierzytelniania wieloskładnikowego platformy Azure](howto-mfa-userdevicesettings.md).

Aby zrozumieć, dlaczego użytkownik został poproszony lub nie został poproszony o wykonanie usługi MFA, zobacz [Raporty uwierzytelniania wieloskładnikowego platformy Azure](howto-mfa-reporting.md#azure-ad-sign-ins-report).
