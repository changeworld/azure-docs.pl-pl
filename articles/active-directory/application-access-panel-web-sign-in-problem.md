---
title: Problem z logowaniem do panelu dostępu do witryny sieci Web | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące rozwiązywania problemów, które można napotkać podczas próby rejestrowania się do panelu dostępu
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviwer: japere,asteen
ms.openlocfilehash: 84df32000892274c2b77b0bf5ce286bb94171494
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332579"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Problem z logowaniem do panelu dostępu do witryny sieci Web

Widok, a następnie uruchom chmurowych aplikacji, które administrator usługi Azure AD udzielił im dostępu do panelu dostępu jest portal sieci web, który umożliwia użytkownikowi, który ma konto służbowe w usłudze Azure Active Directory (Azure AD). Użytkownik, który ma wersje usługi Azure AD umożliwia także grupami samoobsługi i funkcje zarządzania aplikacjami za pomocą panelu dostępu. Panel dostępu jest oddzielony od portalu Azure i nie wymaga użytkownikom posiadania subskrypcji platformy Azure.

Użytkownicy mogą Zaloguj się do panelu dostępu jeśli ma konto służbowe w usłudze Azure AD.

-   Użytkownicy mogą być uwierzytelniane przez usługę Azure AD bezpośrednio.

-   Użytkownicy mogą uwierzytelniać za pomocą usługi Active Directory Federation Services (AD FS).

-   Użytkownicy mogą być uwierzytelniane przez usługę Active Directory systemu Windows Server.

Jeśli użytkownik ma subskrypcję platformy Azure lub usługi Office 365 i korzysta z portalu Azure lub aplikacji usługi Office 365, będzie mógł używać panelu dostępu bezproblemowo bez konieczności ponownego logowania się. Użytkownicy, którzy nie zostali uwierzytelnieni monit logowania przy użyciu nazwy użytkownika i hasło dla swojego konta w usłudze Azure AD. Jeśli organizacja ma skonfigurowany federacyjnego, wpisując nazwę użytkownika jest wystarczająca.

## <a name="general-issues-to-check-first"></a>Ogólne problemy, aby sprawdzić w pierwszej kolejności 

-   Upewnij się, że użytkownik loguje się do **Popraw adres URL**: <https://myapps.microsoft.com>

-   Upewnij się, że przeglądarka użytkownika został dodany adres URL do jego **Zaufane witryny**

-   Upewnij się, że konto użytkownika jest **włączone** logowania.

-   Upewnij się, że konto użytkownika jest **bez blokady.**

-   Upewnij się, że użytkownika **nie wygasł lub zapomnienia hasła.**

-   Upewnij się, że **uwierzytelnianie wieloskładnikowe** nie blokuje dostępu użytkownika.

-   Upewnij się, że **zasady dostępu warunkowego** lub **Identity Protection** zasad nie blokuje dostępu użytkownika.

-   Upewnij się, że użytkownik **informacje kontaktowe uwierzytelniania** jest aktualna, aby umożliwić uwierzytelnianie wieloskładnikowe lub dostępu warunkowego zasad, które mają być egzekwowane.

-   Upewnij się, że również spróbuj wyczyszczenie plików cookie w przeglądarce, a następnie spróbuj się ponownie zalogować.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Spełniające wymagania przeglądarki do panelu dostępu

Panel dostępu wymaga przeglądarki obsługującej JavaScript i włączył CSS. Aby użyć opartego na hasłach rejestracji jednokrotnej (SSO) w panelu dostępu, rozszerzenia Panelu dostępu musi być zainstalowany w przeglądarce. To rozszerzenie jest pobierany automatycznie, gdy użytkownik wybierze aplikacji, która jest skonfigurowana do opartego na hasłach logowania jednokrotnego.

Logowanie Jednokrotne opartego na hasłach można przeglądarki przez użytkownika końcowego:

-   Internet Explorer 8, 9, 10, 11 — w systemie Windows 7 lub nowszy

-   Krawędź w systemie Windows 10 Anniversary Edition lub nowszy 

-   Chrome — W systemie Windows 7 lub nowszy oraz System MacOS x lub nowszych

-   Firefox 26.0 lub później — w systemie Windows XP z dodatkiem SP2 lub nowszy oraz w systemie Mac OS X 10,6 lub nowszy


## <a name="problems-with-the-users-account"></a>Problemy z kontem użytkownika

Dostęp do panelu dostępu mogą zostać zablokowane z powodu problemu z kontem użytkownika. Poniżej przedstawiono kilka sposobów umożliwiają rozwiązywanie problemów oraz rozwiązywania problemów z użytkownikami i ich ustawienia konta:

-   [Sprawdź, czy konto użytkownika istnieje w usłudze Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Sprawdź stan konta użytkownika](#check-a-users-account-status)

-   [Resetowanie hasła użytkownika](#reset-a-users-password)

-   [Włącz samoobsługowe resetowanie haseł](#enable-self-service-password-reset)

-   [Sprawdź stan usługi Multi-Factor authentication użytkownika](#check-a-users-multi-factor-authentication-status)

-   [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

-   [Sprawdzanie członkostwa w grupach użytkownika](#check-a-users-group-memberships)

-   [Sprawdź przypisane licencje użytkownika](#check-a-users-assigned-licenses)

-   [Przypisywanie licencji użytkownika](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Sprawdź, czy konto użytkownika istnieje w usłudze Azure Active Directory

Aby sprawdzić, czy konto użytkownika jest obecne, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Sprawdź właściwości obiektu użytkownika, należy upewnić się, że wyglądają zgodnie z oczekiwaniami i żadne dane nie istnieje.

### <a name="check-a-users-account-status"></a>Sprawdź stan konta użytkownika

Aby sprawdzić stan konta użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **profilu**.

8.  W obszarze **ustawienia** upewnij się, że **Zaloguj bloku** ustawiono **nr**.

### <a name="reset-a-users-password"></a>Resetowanie hasła użytkownika

Aby zresetować hasło użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **resetowania hasła** w górnej części okienka użytkownika.

8.  Kliknij przycisk **resetowania hasła** znajdującego się na **resetowania hasła** okienko zostanie wyświetlone.

9.  Kopiuj **hasło tymczasowe** lub **wprowadź nowe hasło** dla użytkownika.

10. Komunikować się z tego nowego hasła dla użytkownika, konieczności zmianę hasła podczas ich następnego logowania do usługi Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Włącz samoobsługowe Resetowanie hasła

Aby włączyć samoobsługowe Resetowanie hasła, wykonaj następujące kroki wdrażania:

-   [Umożliwianie użytkownikom resetowania swoich haseł w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [Umożliwianie użytkownikom resetowania lub zmieniania swoich haseł lokalnej usługi Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>Sprawdź stan usługi Multi-Factor authentication użytkownika

Aby sprawdzić stan usługi Multi-Factor authentication użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  Kliknij przycisk **uwierzytelnianie wieloskładnikowe** u góry okienka.

7.  Raz **portalu administracyjnego usługi Multi-Factor Authentication** obciążeń, upewnij się, czy na **użytkowników** kartę.

8.  Znajdź użytkownika, na liście użytkowników przez wyszukiwanie, filtrowanie i sortowanie.

9.  Wybierz użytkownika z listy użytkowników i **włączyć**, **wyłączyć**, lub **Wymuś** usługi Multi-Factor authentication zgodnie z potrzebami.

   >[!NOTE]
   >Jeśli użytkownik znajduje się w **wymuszone** stanu, użytkownik może ustawić ich **wyłączone** tymczasowo w celu umożliwienia im wrócić do swojego konta. Gdy są one ponownie, można zmienić ich stan, aby **włączone** ponownie, aby wymagały ponownie zarejestrować swoje informacje kontaktowe podczas ich następnego logowania. Alternatywnie możesz wykonać kroki opisane w [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info) Sprawdź lub ustaw dla nich dane.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Sprawdź informacje kontaktowe uwierzytelniania użytkownika

Aby sprawdzić informacje kontaktowe uwierzytelniania użytkownika używane do uwierzytelniania wieloskładnikowego, dostępu warunkowego, ochrony tożsamości i resetowania hasła, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **profilu**.

8.  Przewiń w dół do **informacje kontaktowe uwierzytelniania**.

9.  **Przegląd** danych zarejestrowanych dla użytkowników i aktualizacji zgodnie z potrzebami.

### <a name="check-a-users-group-memberships"></a>Sprawdzanie członkostwa w grupach użytkownika

Aby sprawdzić członkostwa w grupach użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **grup** Aby wyświetlić grupy, które użytkownik jest członkiem.

### <a name="check-a-users-assigned-licenses"></a>Sprawdź przypisane licencje użytkownika

Aby sprawdzić przypisane licencje użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencje użytkownika został przypisany.

### <a name="assign-a-user-a-license"></a>Przypisywanie licencji użytkownika 

Aby przypisać licencję do użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **portalu Azure** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usług Azure Active Directory** klikając **wszystkie usługi** w górnej części menu nawigacji po lewej stronie głównej.

3.  Wpisz w **"Azure Active Directory**" w polu wyszukiwania filtr a wybierz **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika planuje się i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencje użytkownika został przypisany.

8.  Kliknij przycisk **przypisać** przycisku.

9.  Wybierz **jeden lub więcej produktów** z listy dostępnych produktów.

10. **Opcjonalne** kliknij **opcje przydziału** element, aby przypisać częściami produktów. Kliknij przycisk **Ok** po zakończeniu.

11. Kliknij przycisk **przypisać** przycisk, aby przypisać licencje do tego użytkownika.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiąże problemu

Otwórz bilet pomocy technicznej następujące informacje, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   Nazwa UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Strefa czasowa i przedziału czasu/czasu podczas błąd występuje

-   Ślady fiddler

## <a name="next-steps"></a>Kolejne kroki
[Podaj logowanie jednokrotne do aplikacji przy użyciu serwera Proxy aplikacji](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
