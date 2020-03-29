---
title: Problemy z logowaniem się do aplikacji przy użyciu łącza deeplink | Dokumenty firmy Microsoft
description: Jak rozwiązywać problemy z uzyskiwaniem dostępu do aplikacji z adresu URL deeplink przy użyciu usługi Azure AD
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44825f32a13db0a221252c042dc9f23ec43a9c8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825430"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problemy z logowaniem się do aplikacji przy użyciu łącza głębokiego

Panel dostępu to portal internetowy, który umożliwia użytkownikowi z kontem służbowym w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD przyznał im dostęp. 

Te aplikacje są konfigurowane w imieniu użytkownika w portalu usługi Azure AD. Aplikacja musi być poprawnie skonfigurowana i przypisana do użytkownika lub grupy, do niej należy użytkownik, aby wyświetlić aplikację w Panelu dostępu.

Głębokie linki lub adresy URL dostępu użytkowników są łączami, za pomocą których użytkownicy mogą uzyskiwać dostęp do swoich aplikacji jednokrotnych haseł bezpośrednio z pasków adresów URL swoich przeglądarek. Przechodząc do tego łącza, użytkownicy są automatycznie logowane do aplikacji bez konieczności przechodzenia do Panelu dostępu. Jest to ten sam łącze, którego użytkownicy używają do uzyskiwania dostępu do tych aplikacji z uruchamiania aplikacji usługi Office 365.

## <a name="general-issues-to-check-first"></a>Kwestie ogólne do sprawdzenia w pierwszej kolejności

-   Upewnij się, że korzystasz z **przeglądarki** spełniającego minimalne wymagania Panelu dostępu.

-   Upewnij się, że przeglądarka użytkownika dodała adres URL aplikacji do **zaufanych witryn**.

-   Upewnij się, że aplikacja jest poprawnie **skonfigurowana.**

-   Upewnij się, że konto użytkownika jest **włączone** dla logowania.

-   Upewnij się, że konto użytkownika nie jest **zablokowane.**

-   Upewnij się, że hasło użytkownika **nie wygasło ani nie zostało zapomniane.**

-   Upewnij się, że **uwierzytelnianie wieloskładnikowe** nie blokuje dostępu użytkowników.

-   Upewnij się, że **zasady dostępu warunkowego** lub zasady **ochrony tożsamości** nie blokują dostępu użytkowników.

-   Upewnij się, że **informacje kontaktowe uwierzytelniania** użytkownika są aktualne, aby umożliwić wymuszanie zasad uwierzytelniania wieloskładnikowego lub dostępu warunkowego.

-   Pamiętaj też, aby wyczyścić pliki cookie przeglądarki i spróbować zalogować się ponownie.

## <a name="checking-the-deeplink"></a>Sprawdzanie deeplink

Aby sprawdzić, czy masz poprawne deeplink, wykonaj następujące kroki:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

7. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

8. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

9. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

10. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

    * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

11. Wybierz aplikację, dla której chcesz sprawdzić deeplink.

12. Znajdź adres **URL dostępu użytkownika**etykiety . Twój deeplink powinien być zgodny z tym adresem URL.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  Otwórz [Panel dostępu](https://myapps.microsoft.com) w jednej z obsługiwanych przeglądarek i zaloguj się jako **użytkownik** w usłudze Azure AD.

2.  kliknij **aplikację** logajną hasła w Panelu dostępu.

3.  W wierszu z prośbą o zainstalowanie oprogramowania wybierz pozycję **Zainstaluj teraz**.

4.  Na podstawie przeglądarki jesteś kierowany do linku do pobrania. **Dodaj** rozszerzenie do przeglądarki.

5.  Jeśli twoja przeglądarka poprosi, wybierz opcję **Włącz** lub **Zezwól** na rozszerzenie.

6.  Po zainstalowaniu **uruchom ponownie** sesję przeglądarki.

7.  Zaloguj się do Panelu dostępu i sprawdź, czy możesz **uruchomić** aplikacje logowania bez logowania do haseł

Możesz również pobrać rozszerzenie dla Chrome i Firefox z tych bezpośrednich linków:

-   [Rozszerzenie panelu dostępu do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu do Firefoksa](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować logowanie jednokrotne hasła dla aplikacji galerii usługi Azure AD

Aby skonfigurować aplikację z galerii usługi Azure AD, należy:

-   [Dodawanie aplikacji z galerii usługi Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD Gallery, wykonaj następujące kroki:

1.  Otwórz [witrynę Azure portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**.

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  kliknij przycisk **Dodaj** w prawym górnym rogu okienka **Aplikacje dla przedsiębiorstw.**

6.  W polach tekstowych **Wprowadź nazwę** z sekcji Dodaj **z galerii** wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

8.  Przed dodaniem aplikacji można zmienić jej nazwę z pola tekstowego **Nazwa.**

9.  Aby dodać aplikację, kliknij przycisk **Dodaj**.

Po krótkim czasie można wyświetlić okienko konfiguracji aplikacji.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj następujące kroki:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. Wybierz tryb **logowania opartego na hasłach.**

9. [Przypisz użytkowników do aplikacji](#how-to-assign-a-user-to-an-application-directly).

10. Ponadto można również podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając **przycisk Aktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy są monitowani o wprowadzenie poświadczeń samodzielnie po uruchomieniu.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować logowanie jednokrotne hasła dla aplikacji spoza galerii

Aby skonfigurować aplikację z galerii usługi Azure AD, należy:

-   [Dodawanie aplikacji spoza galerii](#add-a-non-gallery-application)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji spoza galerii

Aby dodać aplikację z galerii usługi Azure AD Gallery, wykonaj następujące kroki:

1.  Otwórz [witrynę Azure portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**.

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  kliknij przycisk **Dodaj** w prawym górnym rogu okienka **Aplikacje dla przedsiębiorstw.**

6.  kliknij pozycję **Aplikacja niezwiązane z galerią.**

7.  Wprowadź nazwę aplikacji w polach tekstowych **Nazwa.** Wybierz **pozycję Dodaj.**

Po krótkim czasie można wyświetlić okienko konfiguracji aplikacji.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj następujące kroki:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

    1.  Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6.  Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Wybierz tryb **logowania opartego na hasłach.**

9.  Wprowadź **adres URL logowania**, adres URL, w którym użytkownicy wprowadź swoją nazwę użytkownika i hasło, aby się zalogować. Upewnij się, że pola logowania są widoczne pod adresem URL.

10. Przypisz użytkowników do aplikacji.

11. Ponadto można również podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając **przycisk Aktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy są monitowani o wprowadzenie poświadczeń samodzielnie po uruchomieniu.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak przypisać użytkownika bezpośrednio do aplikacji

Aby bezpośrednio przypisać jednego lub więcej użytkowników do aplikacji, wykonaj następujące kroki:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** z menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** u góry listy **Użytkownicy i grupy,** aby otworzyć okienko **Dodaj przypisanie.**

9. kliknij selektor **Użytkownicy i grupy** w okienku **Dodawanie przypisania.**

10. Wpisz **pełną nazwę** lub **adres e-mail** użytkownika, którego jesteś zainteresowany, aby przypisać do pola wyszukiwania według nazwy lub adresu **e-mail.**

11. Umieść wskaźnik myszy na **użytkowniku** na liście, aby wyświetlić **pole wyboru**. Aby dodać użytkownika do listy **Zaznaczone,** kliknij pole wyboru obok zdjęcia profilowego lub logo użytkownika.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika,** wpisz inne imię i **nazwisko** lub **adres e-mail** w polu wyszukiwania według nazwy lub adresu **e-mail,** a następnie kliknij to pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników kliknij przycisk **Wybierz,** aby dodać ich do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **wybierz rolę** w okienku **Dodaj przypisanie,** aby wybrać rolę przypisaną do wybranych użytkowników.

15. Kliknij przycisk **Przypisz,** aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie wybrani użytkownicy będą mogli uruchamiać te aplikacje w Panelu dostępu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiążą problemu. 

otwórz bilet pomocy technicznej z następującymi informacjami, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Strefa czasowa i czas/ramy czasowe podczas wystąpienia błędu

-   Ślady skrzypka

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
