---
title: Problem z logowaniem do panelu dostępu do witryny sieci Web | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące rozwiązywania problemów, które można napotkać podczas próby zarejestrowania się za pomocą panelu dostępu
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f7418c16d5d5312d2a9a7913da49941ef0a7870
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60294008"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Problem z logowaniem do panelu dostępu do witryny sieci Web

Panelu dostępu jest wyświetlanie i uruchamianie aplikacji opartej na chmurze procesów, które administrator usługi Azure AD udzielił, ich dostęp do portalu opartego na sieci web, który umożliwia użytkownikowi, który ma konto służbowe lub szkolne w usłudze Azure Active Directory (Azure AD). Użytkownik, który ma wersje usługi Azure AD umożliwia również grupa samoobsługi i możliwości zarządzania aplikacjami za pomocą panelu dostępu. Panel dostępu różni się w witrynie Azure portal, a nie wymagać od użytkowników posiadania subskrypcji platformy Azure.

Użytkownicy mogą logować się do panelu dostępu, jeśli w usłudze Azure AD istnieją konta firmowego lub szkolnego.

-   Użytkownicy mogą być uwierzytelniane przez usługę Azure AD bezpośrednio.

-   Użytkownicy mogą być uwierzytelniane przy użyciu usługi Active Directory Federation Services (AD FS).

-   Użytkownicy mogą być uwierzytelniane przez usługę Active Directory systemu Windows Server.

Jeśli użytkownik ma subskrypcję usługi Azure lub Office 365 i doświadczenie z witryny Azure portal lub aplikacji usługi Office 365, będzie mógł używać panelu dostępu bezproblemowo bez konieczności ponownego zalogowania. Użytkownicy, którzy nie zostali uwierzytelnieni. monit logowania przy użyciu nazwy użytkownika i hasła do swojego konta w usłudze Azure AD. Jeśli organizacja ma skonfigurowane Federacji, wpisując nazwę użytkownika jest wystarczająca.

## <a name="general-issues-to-check-first"></a>Zagadnienia ogólne w celu sprawdzenia 

-   Upewnij się, że użytkownik loguje się do **Popraw adres URL**: <https://myapps.microsoft.com>

-   Upewnij się, w przeglądarce dodano adres URL do jego **Zaufane witryny**

-   Upewnij się, że konto użytkownika jest **włączone** do logowania.

-   Upewnij się, że konto użytkownika jest **bez blokady.**

-   Upewnij się, że użytkownika **nie wygasł lub zapomnienia hasła.**

-   Upewnij się, że **uwierzytelnianie wieloskładnikowe** nie blokuje dostęp użytkownika.

-   Upewnij się, że **zasad dostępu warunkowego** lub **Identity Protection** zasad nie blokuje dostęp użytkownika.

-   Upewnij się, że użytkownik **informacje kontaktowe uwierzytelniania** jest aktualna, aby umożliwić uwierzytelnianie wieloskładnikowe lub dostępu warunkowego zasad, które mają być egzekwowane.

-   Upewnij się, że także spróbuj wyczyszczenie plików cookie w przeglądarce i podjęcie próby ponownego zalogowania.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Spełniających wymagania dotyczące przeglądarki dla panelu dostępu

Panel dostępu wymaga przeglądarki, która obsługuje język JavaScript i CSS została włączona. Aby użyć opartego na hasłach logowanie jednokrotne (SSO) w panelu dostępu, rozszerzenie panelu dostępu musi być zainstalowany w przeglądarce użytkownika. To rozszerzenie jest pobierany automatycznie, gdy użytkownik wybierze aplikacji, która jest skonfigurowana na podstawie hasła logowania jednokrotnego.

W przypadku logowania jednokrotnego opartego na hasłach może być przeglądarki przez użytkownika końcowego:

-   Internet Explorer 8, 9, 10, 11 — Windows 7 lub nowszy

-   Microsoft Edge w systemie Windows 10 Anniversary Edition lub nowszy 

-   Chrome — W systemie Windows 7 lub nowszy i System MacOS x lub nowszym

-   Firefox 26.0 lub później — w Windows XP z dodatkiem SP2 lub nowszy oraz w systemie Mac OS X 10,6 lub nowszej


## <a name="problems-with-the-users-account"></a>Problemy z konta użytkownika

Ze względu na problem z kontem użytkownika, może zostać zablokowany dostęp do panelu dostępu. Poniżej przedstawiono kilka sposobów, rozwiązywanie problemów oraz rozwiązywanie problemów z użytkownikami i ich ustawienia konta:

-   [Sprawdź, czy konto użytkownika istnieje w usłudze Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Sprawdź stan konta użytkownika](#check-a-users-account-status)

-   [Resetowanie hasła użytkownika](#reset-a-users-password)

-   [Włącz samoobsługowe resetowanie haseł](#enable-self-service-password-reset)

-   [Sprawdź stan uwierzytelniania wieloskładnikowego dla użytkownika](#check-a-users-multi-factor-authentication-status)

-   [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

-   [Sprawdzanie członkostwa grupy użytkownika](#check-a-users-group-memberships)

-   [Sprawdź przypisanych licencji użytkownika](#check-a-users-assigned-licenses)

-   [Przypisywanie licencji użytkownika](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Sprawdź, czy konto użytkownika istnieje w usłudze Azure Active Directory

Aby sprawdzić, czy konto użytkownika jest obecny, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Sprawdź właściwości obiektu użytkownika, należy upewnić się, że spojrzeć zgodnie z oczekiwaniami i żadne dane nie istnieje.

### <a name="check-a-users-account-status"></a>Sprawdź stan konta użytkownika

Aby sprawdzić stan konta użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **profilu**.

8.  W obszarze **ustawienia** upewnij się, że **Blokuj logowanie** ustawiono **nie**.

### <a name="reset-a-users-password"></a>Resetowanie hasła użytkownika

Aby zresetować hasło użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **Resetuj hasło** znajdujący się u góry okienka użytkownika.

8.  Kliknij przycisk **Resetuj hasło** znajdujący się na **Resetuj hasło** wyświetlonym okienku.

9.  Kopiuj **hasło tymczasowe** lub **wprowadź nowe hasło** dla użytkownika.

10. Komunikować się z tego nowego hasła dla użytkownika, mogą być wymagane, aby zmienić to hasło podczas jego następnego logowania w usłudze Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

Aby włączyć samoobsługowe Resetowanie hasła, wykonaj następujące kroki wdrożenia:

-   [Umożliwianie użytkownikom resetowania swoich haseł w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Umożliwianie użytkownikom Resetowanie lub zmienianie swoich haseł lokalnych w Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Sprawdź stan uwierzytelniania wieloskładnikowego dla użytkownika

Aby sprawdzić stan uwierzytelniania wieloskładnikowego dla użytkownika, wykonaj następujące kroki:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5. Kliknij przycisk **wszyscy użytkownicy**.

6. Kliknij przycisk **uwierzytelnianie wieloskładnikowe** znajdujący się u góry okienka.

7. Raz **portalu administratora usługi Multi-Factor Authentication** obciążeniami, upewnij się, znajdują się na **użytkowników** kartę.

8. Znajdź użytkownika, na liście użytkowników przez wyszukiwanie, filtrowanie i sortowanie.

9. Wybierz użytkownika z listy użytkowników i **Włącz**, **wyłączyć**, lub **Wymuś** uwierzytelnianie wieloskładnikowe zgodnie z potrzebami.

   >[!NOTE]
   >Jeśli użytkownik znajduje się w **wymuszone** stanu, możesz je ustawić **wyłączone** tymczasowo w celu umożliwienia im do swojego konta. Gdy są one ponownie, można zmienić ich stan, aby **włączone** ponownie, aby wymagać od nich, aby ponownie zarejestrować informacje kontaktowe podczas jego następnego logowania. Alternatywnie, możesz wykonać kroki opisane w [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info) Sprawdź lub ustaw te dane dla nich.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Sprawdź informacje kontaktowe uwierzytelniania użytkownika

Aby sprawdzić informacje kontaktowe uwierzytelniania użytkownika używane do uwierzytelniania wieloskładnikowego, dostępu warunkowego, ochrony tożsamości i resetowania hasła, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **profilu**.

8.  Przewiń w dół do **informacje kontaktowe uwierzytelniania**.

9.  **Przegląd** dane zarejestrowane dla użytkowników i aktualizacji, zgodnie z potrzebami.

### <a name="check-a-users-group-memberships"></a>Sprawdzanie członkostwa grupy użytkownika

Aby sprawdzić członkostwa w grupach użytkowników, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **grup** aby zobaczyć, który grupuje użytkownik jest członkiem.

### <a name="check-a-users-assigned-licenses"></a>Sprawdź przypisanych licencji użytkownika

Aby sprawdzić przypisanych licencji użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencji użytkownika został przypisany.

### <a name="assign-a-user-a-license"></a>Przypisywanie licencji użytkownika 

Aby przypisać licencję do użytkownika, wykonaj następujące kroki:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszyscy użytkownicy**.

6.  **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencji użytkownika został przypisany.

8.  Kliknij przycisk **przypisać** przycisku.

9.  Wybierz **jeden lub więcej produktów** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij **opcje przydziału** element do szczegółowego przypisania produktów. Kliknij przycisk **Ok** po zakończeniu.

11. Kliknij przycisk **przypisać** przycisk, aby przypisać licencje do tego użytkownika.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiąże problemu

Otwórz bilet pomocy technicznej, używając następujących informacji, jeśli jest dostępny:

-   Identyfikator błędu korelacji

-   Nazwy UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Występuje, strefę czasową i przedziału czasu/czasu podczas błędu

-   Ślady narzędzia fiddler

## <a name="next-steps"></a>Kolejne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
