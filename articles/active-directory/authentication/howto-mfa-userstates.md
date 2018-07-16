---
title: Stany użytkowników uwierzytelniania wieloskładnikowego Azure firmy Microsoft
description: Więcej informacji na temat stanów użytkowników w usłudze Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 6945966d4a701ea6e2684b7da766c8b6c9f9a283
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049052"
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Jak, które wymuszają weryfikację dwuetapową dla użytkownika lub grupy

Można wybrać jedno z dwóch podejść do wymagania weryfikacji dwuetapowej. Pierwszym z nich jest umożliwienie każdy użytkownik usługi Azure Multi-Factor Authentication (MFA). Gdy użytkownicy są włączone indywidualnie, wykonują weryfikacji dwuetapowej każdym logowaniu (z pewnymi wyjątkami, takie jak podczas logowania z zaufanego adresu IP adresów lub _zapamiętanych urządzeniach_ jest włączona funkcja). Drugą opcją jest, aby skonfigurować zasady dostępu warunkowego, które wymagają weryfikacji dwuetapowej pod pewnymi warunkami.

>[!TIP] 
>Wybierz jedną z następujących metod, które wymuszają weryfikację dwuetapową, nie obydwa. Włączanie usługi Azure Multi-Factor Authentication użytkownik zastępuje wszystkie zasady dostępu warunkowego.

## <a name="which-option-is-right-for-you"></a>Która opcja jest dla Ciebie odpowiednia?

**Włączanie usługi Azure Multi-Factor Authentication, zmieniając stanów użytkowników** to tradycyjne podejście do wymagania weryfikacji dwuetapowej. Działa zarówno usługi Azure MFA w chmurze i serwera Azure MFA. Wszystkich użytkowników, którzy włączenia wykonaj weryfikację dwuetapową, za każdym razem, gdy zalogują się w. Włączenie użytkownik zastępuje wszelkie zasady dostępu warunkowego, które mogłyby wpłynąć na tego użytkownika. 

**Włączanie usługi Azure Multi-Factor Authentication za pomocą zasad dostępu warunkowego** jest to bardziej elastyczne podejście do wymagania weryfikacji dwuetapowej. Działa tylko dla usługi Azure MFA w chmurze, jednak i _dostępu warunkowego_ jest [płatnych funkcji usługi Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features). Można utworzyć zasady dostępu warunkowego, które są stosowane do grupy, a także poszczególnych użytkowników. Grupy o wysokim ryzyku można podać więcej ograniczeń niż grup o niskim ryzyku lub weryfikacji dwuetapowej, które mogą być wymagane tylko dla aplikacji w chmurze o wysokim ryzyku i pomijana dla nich niskiego ryzyka. 

Obie opcje monitować użytkowników o rejestrowania dla usługi Azure Multi-Factor Authentication podczas pierwszego logowania po Włącz wymagania. Obie opcje również pracować można konfigurować [ustawienia usługi Azure Multi-Factor Authentication](howto-mfa-mfasettings.md).

## <a name="enable-azure-mfa-by-changing-user-status"></a>Włącz usługę Azure MFA, zmieniając stan użytkownika

Konta użytkowników w usłudze Azure Multi-Factor Authentication mają następujące trzy różne stany:

| Stan | Opis | Aplikacje korzystające z przeglądarki, których to dotyczy | Aplikacje przeglądarki, których to dotyczy | Nowoczesnego uwierzytelniania, których to dotyczy |
|:---:|:---:|:---:|:--:|:--:|
| Disabled (Wyłączony) |Stan domyślny dla nowego użytkownika, nie są zarejestrowane w usłudze Azure MFA. |Nie |Nie |Nie |
| Enabled (Włączony) |Użytkownik został zarejestrowany w usłudze Azure MFA, ale nie została zarejestrowana. Otrzyma monit o zarejestrować przy następnym logowaniu. |Nie.  One nadal działać do momentu zakończenia procesu rejestracji. | Tak. Po wygaśnięciu sesji, wymagana jest rejestracja usługi Azure MFA.| Tak. Po wygaśnięciu ważności tokenu dostępu, wymagana jest rejestracja usługi Azure MFA. |
| Enforced (Wymuszony) |Użytkownik został zarejestrowany i zakończeniu procesu rejestracji dla usługi Azure MFA. |Tak.  Aplikacje wymagające wprowadzenia hasła aplikacji. |Tak. Usługa Azure MFA jest wymagana podczas logowania. | Tak. Usługa Azure MFA jest wymagana podczas logowania. |

Stan użytkownika wskazuje, czy administrator ma zarejestrowane je w usłudze Azure MFA i tego, czy ich ukończenie procesu rejestracji.

Wszyscy użytkownicy zaczynają *wyłączone*. Podczas rejestrowania się użytkowników w usłudze Azure MFA, jego stan zmieni się na *włączone*. Gdy włączonych użytkowników zalogować i dokończyć proces rejestracji, jego stan zmieni się na *wymuszone*.  

### <a name="view-the-status-for-a-user"></a>Wyświetlanie stanu użytkownika

Aby uzyskać dostęp do strony, w którym można wyświetlać i zarządzać stanów użytkowników, wykonaj następujące kroki:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **użytkowników i grup** > **wszyscy użytkownicy**.
3. Wybierz **uwierzytelnianie wieloskładnikowe**.
   ![Wybierz uwierzytelnianie wieloskładnikowe](./media/howto-mfa-userstates/selectmfa.png)
4. Zostanie otwarta nowa strona, wyświetlająca stanu użytkownika.
   ![Stan użytkownika usługi Multi-Factor authentication — zrzut ekranu](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Zmiana stanu użytkownika

1. Użyj powyższych kroków, aby uzyskać dostęp do usługi Azure Multi-Factor Authentication **użytkowników** strony.
2. Znajdź użytkownika, który chcesz włączyć dla usługi Azure MFA. Konieczne może być zmiana widoku w górnej części. 
   ![Znajdź użytkownika — zrzut ekranu](./media/howto-mfa-userstates/enable1.png)
3. Zaznacz pole obok nazwy użytkownika.
4. Po prawej stronie w obszarze **Szybkie kroki**, wybierz **Włącz** lub **wyłączyć**.
   ![Włącz wybranego użytkownika — zrzut ekranu](./media/howto-mfa-userstates/user1.png)

   >[!TIP]
   >*Włączone* użytkownicy są automatycznie przełączone do *wymuszone* podczas rejestrowania dla usługi Azure MFA. Wykonaj ręcznie nie zmiany stanu użytkownika do *wymuszone*. 

5. Potwierdź wybór w otwartym oknie podręcznym. 

Po włączeniu użytkowników, Powiadamiaj ich za pośrednictwem poczty e-mail. Poinformuj ich, że zostanie wyświetlony monit do zarejestrowania przy następnym logowaniu. Ponadto jeśli Twoja organizacja używa aplikacji niekorzystających z przeglądarki, które nie obsługują nowoczesnego uwierzytelniania, muszą utworzyć hasła aplikacji. Możesz również uwzględnić łącze do [przewodnik dla użytkowników końcowych usługi Azure MFA](../user-help/multi-factor-authentication-end-user.md) aby pomóc im rozpocząć pracę.

### <a name="use-powershell"></a>Korzystanie z programu PowerShell
Aby zmienić stan użytkownika przy użyciu [usługi Azure AD PowerShell](/powershell/azure/overview), zmień `$st.State`. Istnieją trzy stany:

* Enabled (Włączony)
* Enforced (Wymuszony)
* Disabled (Wyłączony)  

Nie bezpośrednio do przenoszenia użytkowników *wymuszone* stanu. Jeśli to zrobisz, nie opartych na przeglądarce aplikacji przestają działać, ponieważ użytkownik nie przeszli rejestracja w usłudze Azure MFA i uzyskać [hasła aplikacji](howto-mfa-mfasettings.md#app-passwords). 

Przy użyciu programu PowerShell jest dobrym rozwiązaniem, gdy trzeba zbiorcze Włączanie użytkowników. Utwórz skrypt programu PowerShell, do listy użytkowników w pętli, która umożliwia im:

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Poniższy skrypt znajduje się przykład:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>Włącz usługę Azure MFA za pomocą zasad dostępu warunkowego

_Dostęp warunkowy_ jest płatne funkcje usługi Azure Active Directory, wiele opcji konfiguracji. Te kroki opisano jeden ze sposobów tworzenia zasad. Aby uzyskać więcej informacji, przeczytaj o [dostępu warunkowego w usłudze Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) jako administrator.
2. Przejdź do **usługi Azure Active Directory** > **dostępu warunkowego**.
3. Wybierz **nowe zasady**.
4. W obszarze **przypisania**, wybierz opcję **użytkowników i grup**. Użyj **Include** i **wykluczyć** kart, aby określić, którym użytkownikom i grupom zasady służą do zarządzania.
5. W obszarze **przypisania**, wybierz opcję **aplikacje w chmurze**. Wybierz uwzględnić **wszystkie aplikacje w chmurze**.
6. W obszarze **kontrole dostępu**, wybierz opcję **Grant**. Wybierz **Wymagaj uwierzytelniania wieloskładnikowego**.
7. Włącz **Włącz zasady** do **na**, a następnie wybierz pozycję **Zapisz**.

Inne opcje zasad dostępu warunkowego zapewniają możliwość określenia dokładnie tak, gdy weryfikacja dwuetapowa była wymagana. Na przykład wprowadzisz zasad, takich jak ta: gdy wykonawców próbuje uzyskać dostęp do naszej aplikacji zamówień z niezaufanymi sieciami, na urządzeniach, które nie są przyłączone do domeny, wymagają weryfikacji dwuetapowej. 

## <a name="next-steps"></a>Kolejne kroki

- Uzyskaj porady [najlepsze rozwiązania dotyczące dostępu warunkowego](../active-directory-conditional-access-best-practices.md).

- Zarządzaj ustawieniami usługi Azure Multi-Factor Authentication [użytkowników i ich urządzeń](howto-mfa-userdevicesettings.md).
