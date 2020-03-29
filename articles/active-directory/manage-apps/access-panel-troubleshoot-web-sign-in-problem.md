---
title: Problem z logowaniem się do witryny panelu dostępu | Dokumenty firmy Microsoft
description: Wskazówki dotyczące rozwiązywania problemów, które mogą wystąpić podczas próby zalogowania się w celu korzystania z Panelu dostępu
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c6a9c3f26c8939176197a2ecf2fcd6026e9928
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784306"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Problem z logowaniem się do witryny panelu dostępu

Panel dostępu to portal internetowy, który umożliwia użytkownikowi, który ma konto służbowe w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD przyznał im dostęp. Użytkownik, który ma wersje usługi Azure AD, może również korzystać z funkcji samoobsługowego zarządzania grupami i aplikacjami za pośrednictwem Panelu dostępu. Panel dostępu jest oddzielony od witryny Azure portal i nie wymaga od użytkowników subskrypcji platformy Azure.

Użytkownicy mogą logować się do panelu dostępu, jeśli mają konto służbowe w usłudze Azure AD.

-   Użytkownicy mogą być uwierzytelnieni bezpośrednio przez usługę Azure AD.

-   Użytkownicy mogą być uwierzytelnieni przy użyciu usług federacyjnych Active Directory (AD FS).

-   Użytkownicy mogą być uwierzytelnieni przez usługę Windows Server Active Directory.

Jeśli użytkownik ma subskrypcję platformy Azure lub usługi Office 365 i korzysta z witryny Azure portal lub aplikacji usługi Office 365, będzie mógł bezproblemowo korzystać z panelu dostępu bez konieczności ponownego logowania. Użytkownicy, którzy nie są uwierzytelnieni, są monitowani o zalogowanie się przy użyciu nazwy użytkownika i hasła dla swojego konta w usłudze Azure AD. Jeśli organizacja skonfigurowała federację, wystarczy wpisanie nazwy użytkownika.

## <a name="general-issues-to-check-first"></a>Kwestie ogólne do sprawdzenia w pierwszej kolejności 

-   Upewnij się, że użytkownik loguje się do **właściwego adresu URL:**<https://myapps.microsoft.com>

-   Upewnij się, że przeglądarka użytkownika dodała adres URL do **zaufanych witryn**

-   Upewnij się, że konto użytkownika jest **włączone** dla logowania.

-   Upewnij się, że konto użytkownika nie jest **zablokowane.**

-   Upewnij się, że hasło użytkownika **nie wygasło ani nie zostało zapomniane.**

-   Upewnij się, że **uwierzytelnianie wieloskładnikowe** nie blokuje dostępu użytkowników.

-   Upewnij się, że **zasady dostępu warunkowego** lub zasady **ochrony tożsamości** nie blokują dostępu użytkowników.

-   Upewnij się, że **informacje kontaktowe uwierzytelniania** użytkownika są aktualne, aby umożliwić wymuszanie zasad uwierzytelniania wieloskładnikowego lub dostępu warunkowego.

-   Pamiętaj też, aby wyczyścić pliki cookie przeglądarki i spróbować zalogować się ponownie.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Spełnianie wymagań przeglądarki dla Panelu dostępu

Panel dostępu wymaga przeglądarki obsługującej język JavaScript i włączonej wersji CSS. Aby w Panelu dostępu można korzystać z logowania jednokrotnego opartego na hasłach, w przeglądarce użytkownika należy zainstalować rozszerzenie Panelu dostępu. To rozszerzenie jest pobierane automatycznie, gdy użytkownik wybierze aplikację skonfigurowaną dla loga loga bez hasła.

W przypadku logów samouszeńców opartych na hasłach przeglądarki użytkownika końcowego mogą być:

-   Internet Explorer 8, 9, 10, 11 — w systemie Windows 7 lub nowszym

-   Microsoft Edge w systemie Windows 10 Anniversary Edition lub nowszym 

-   Chrome — w systemie Windows 7 lub nowszym oraz w systemie MacOS X lub nowszym

-   Firefox 26.0 lub nowszy — w systemie Windows XP z dodatkiem SP2 lub nowszym oraz w systemie Mac OS X 10.6 lub nowszym


## <a name="problems-with-the-users-account"></a>Problemy z kontem użytkownika

Dostęp do Panelu dostępu można zablokować z powodu problemu z kontem użytkownika. Oto kilka sposobów rozwiązywania problemów z użytkownikami i ustawieniami konta:

-   [Sprawdzanie, czy konto użytkownika istnieje w usłudze Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Sprawdzanie stanu konta użytkownika](#check-a-users-account-status)

-   [Resetowanie hasła użytkownika](#reset-a-users-password)

-   [Włącz samoobsługowe resetowanie haseł](#enable-self-service-password-reset)

-   [Sprawdzanie stanu uwierzytelniania wieloskładnikowego użytkownika](#check-a-users-multi-factor-authentication-status)

-   [Sprawdzanie informacji kontaktowych uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

-   [Sprawdzanie członkostwa w grupie użytkownika](#check-a-users-group-memberships)

-   [Sprawdzanie przypisanych licencji użytkownika](#check-a-users-assigned-licenses)

-   [Przypisywanie użytkownikowi licencji](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Sprawdzanie, czy konto użytkownika istnieje w usłudze Azure Active Directory

Aby sprawdzić, czy konto użytkownika jest obecne, wykonaj następujące kroki:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  Sprawdź właściwości obiektu użytkownika, aby upewnić się, że wyglądają zgodnie z oczekiwaniami i nie brakuje żadnych danych.

### <a name="check-a-users-account-status"></a>Sprawdzanie stanu konta użytkownika

Aby sprawdzić stan konta użytkownika, wykonaj następujące czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  kliknij pozycję **Profil**.

8.  W obszarze **Ustawienia** upewnij się, że **logowanie bloku** jest ustawione na **Nie**.

### <a name="reset-a-users-password"></a>Resetowanie hasła użytkownika

Aby zresetować hasło użytkownika, wykonaj następujące czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  kliknij przycisk **Resetuj hasło** w górnej części okienka użytkownika.

8.  kliknij przycisk **Resetuj hasło** w wyświetlonym okienku **Resetowanie hasła.**

9.  Skopiuj **hasło tymczasowe** lub **wprowadź nowe hasło** dla użytkownika.

10. Przekaż to nowe hasło do użytkownika, muszą zmienić to hasło podczas następnego logowania do usługi Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

Aby włączyć samoobsługowe resetowanie hasła, wykonaj następujące kroki wdrażania:

-   [Umożliwianie użytkownikom resetowania haseł usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Umożliwianie użytkownikom resetowania lub zmieniania haseł lokalnych usługi Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Sprawdzanie stanu uwierzytelniania wieloskładnikowego użytkownika

Aby sprawdzić stan uwierzytelniania wieloskładnikowego użytkownika, wykonaj następujące czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5. kliknij pozycję **Wszyscy użytkownicy**.

6. kliknij przycisk **Uwierzytelnianie wieloskładnikowe** w górnej części okienka.

7. Po załadowaniu **portalu zarządzania uwierzytelnianiem wieloskładnikowym** upewnij się, że znajdujesz się na karcie **Użytkownicy.**

8. Znajdź użytkownika na liście użytkowników, wyszukując, filtrując lub sortując.

9. Wybierz użytkownika z listy użytkowników i **Włącz**, **Wyłącz**lub **Wymuń** uwierzytelnianie wieloskładnikowe zgodnie z potrzebami.

   >[!NOTE]
   >Jeśli użytkownik jest w stanie **wymuszone,** można ustawić je **na Wyłączone** tymczasowo, aby umożliwić mu powrót do swojego konta. Po powrocie można zmienić ich stan na **Włączone** ponownie, aby wymagać od nich ponownego zarejestrowania swoich informacji kontaktowych podczas następnego logowania. Alternatywnie można wykonać kroki opisane w [sprawdź informacje kontaktowe uwierzytelniania użytkownika,](#check-a-users-authentication-contact-info) aby zweryfikować lub ustawić te dane dla nich.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Sprawdzanie informacji kontaktowych uwierzytelniania użytkownika

Aby sprawdzić informacje kontaktowe uwierzytelniania użytkownika używane do uwierzytelniania wieloskładnikowego, dostępu warunkowego, ochrony tożsamości i resetowania hasła, wykonaj następujące kroki:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  kliknij pozycję **Profil**.

8.  Przewiń w dół do **informacji kontaktowych uwierzytelniania**.

9.  **Przejrzyj** dane zarejestrowane dla użytkownika i w razie potrzeby zaktualizuj.

### <a name="check-a-users-group-memberships"></a>Sprawdzanie członkostwa w grupie użytkownika

Aby sprawdzić członkostwo użytkownika w grupach, wykonaj następujące czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  kliknij pozycję **Grupy,** aby zobaczyć, do których grup jest członkiem.

### <a name="check-a-users-assigned-licenses"></a>Sprawdzanie przypisanych licencji użytkownika

Aby sprawdzić przypisane licencje użytkownika, wykonaj następujące czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  kliknij **pozycję Licencje,** aby zobaczyć, które licencje użytkownik aktualnie ma przypisane.

### <a name="assign-a-user-a-license"></a>Przypisywanie użytkownikowi licencji 

Aby przypisać licencję do użytkownika, wykonaj następujące czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7.  kliknij **pozycję Licencje,** aby zobaczyć, które licencje użytkownik aktualnie ma przypisane.

8.  kliknij przycisk **Przypisz.**

9.  Wybierz **jeden lub więcej produktów** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij element **opcji przydziału,** aby dokładnie przypisać produkty. Po zakończeniu kliknij przycisk **Ok.**

11. Kliknij przycisk **Przypisz,** aby przypisać te licencje do tego użytkownika.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiążą problemu

otwórz bilet pomocy technicznej z następującymi informacjami, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Strefa czasowa i czas/ramy czasowe podczas wystąpienia błędu

-   Ślady skrzypka

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
