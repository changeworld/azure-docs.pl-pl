---
title: Problemy z logowaniem się do aplikacji z panelu dostępu | Dokumenty firmy Microsoft
description: Jak rozwiązywać problemy z uzyskiwaniem dostępu do aplikacji z Panelu dostępu usługi Microsoft Azure AD w myapps.microsoft.com
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
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b93ee38666b93253c7cda6c756d4f58daaea236
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082159"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problemy z logowaniem się do aplikacji z panelu dostępu

Panel dostępu to portal internetowy, który umożliwia użytkownikowi z kontem służbowym w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD przyznał im dostęp. 

Te aplikacje są konfigurowane w imieniu użytkownika w portalu usługi Azure AD. Aplikacja musi być poprawnie skonfigurowana i przypisana do użytkownika lub grupy, do niej należy użytkownik, aby wyświetlić aplikację w Panelu dostępu.

Typ aplikacji, które użytkownik może widzieć, należy do następujących kategorii:

-   Aplikacje usługi Office 365

-   Microsoft i aplikacje innych firm skonfigurowane z przysłówkiem samosoi opartym na federacji

-   Aplikacje logowania logowania bez hasła

-   Aplikacje z istniejącymi rozwiązaniami SSO

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

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Spełnianie wymagań przeglądarki dla Panelu dostępu

Panel dostępu wymaga przeglądarki obsługującej język JavaScript i włączonej wersji CSS. Aby w Panelu dostępu można korzystać z logowania jednokrotnego opartego na hasłach, w przeglądarce użytkownika należy zainstalować rozszerzenie Panelu dostępu. To rozszerzenie jest pobierane automatycznie, gdy użytkownik wybierze aplikację skonfigurowaną dla loga loga bez hasła.

W przypadku logów samouszeńców opartych na hasłach przeglądarki użytkownika końcowego mogą być:

-   Internet Explorer 8, 9, 10, 11 — w systemie Windows 7 lub nowszym

-   Microsoft Edge w systemie Windows 10 Anniversary Edition lub nowszym

-   Chrome — w systemie Windows 7 lub nowszym oraz w systemie MacOS X lub nowszym

-   Firefox 26.0 lub nowszy — w systemie Windows XP z dodatkiem SP2 lub nowszym oraz w systemie Mac OS X 10.6 lub nowszym

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj poniższe czynności:

1.  Otwórz [Panel dostępu](https://myapps.microsoft.com) w jednej z obsługiwanych przeglądarek i zaloguj się jako **użytkownik** w usłudze Azure AD.

2.  Kliknij aplikację logajną jako **loga** w Panelu dostępu.

3.  W wierszu z prośbą o zainstalowanie oprogramowania wybierz pozycję **Zainstaluj teraz**.

4.  Na podstawie przeglądarki jesteś kierowany do linku do pobrania. **Dodaj** rozszerzenie do przeglądarki.

5.  Jeśli twoja przeglądarka poprosi, wybierz opcję **Włącz** lub **Zezwól** na rozszerzenie.

6.  Po zainstalowaniu **uruchom ponownie** sesję przeglądarki.

7.  Zaloguj się do Panelu dostępu i sprawdź, czy możesz **uruchomić** aplikacje logowania bez logowania do haseł

Możesz również pobrać rozszerzenie dla Chrome i Microsoft Edge z poniższych bezpośrednich linków:

-   [Rozszerzenie panelu dostępu do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu microsoft edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji galerii usługi Azure AD

Wszystkie aplikacje w galerii usługi Azure AD włączone z możliwością logowania jednokrotnego w przedsiębiorstwie ma dostępny samouczek krok po kroku. Można uzyskać dostęp do [listy samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory, aby](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) uzyskać szczegółowe wskazówki krok po kroku.

Aby skonfigurować aplikację z galerii usługi Azure AD, należy:

-   Dodawanie aplikacji z galerii usługi Azure AD

-   [Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (logowanie do adresu URL, identyfikatora, adresu URL odpowiedzi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Wybierz identyfikator użytkownika i dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobieranie metadanych i certyfikatów usługi Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurowanie wartości metadanych usługi Azure AD w aplikacji (Logowanie do adresu URL, wystawca, adres URL wylogowania i certyfikat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Przypisywanie użytkowników do aplikacji

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD Gallery, wykonaj poniższe czynności:

1.  Otwórz [witrynę Azure portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  kliknij przycisk **Dodaj** w prawym górnym rogu okienka **Aplikacje dla przedsiębiorstw.**

6.  W polach tekstowych **Wprowadź nazwę** z sekcji Dodaj **z galerii** wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować do logowania jednokrotnego.

8.  Przed dodaniem aplikacji można zmienić jej nazwę z pola tekstowego **Nazwa.**

9.  Kliknij przycisk **Dodaj,** aby dodać aplikację.

Po krótkim czasie można wyświetlić okienko konfiguracji aplikacji.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurowanie logowania jednokrotnego dla aplikacji z galerii usługi Azure AD

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj poniższe czynności:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. Z listy rozwijanej **Tryb** wybierz **opcję Logowanie oparte na SAML.**

9. Wprowadź wymagane wartości w **obszarze Domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

   1. Aby skonfigurować aplikację jako logowania sytego inicjowane jako spso, adres URL logowania jest wartością wymaganą. W przypadku niektórych aplikacji identyfikator jest również wymaganą wartością.

   2. Aby skonfigurować aplikację jako identyfikatora SSO inicjowanego przez IdP, adres URL odpowiedzi jest wartością wymaganą. W przypadku niektórych aplikacji identyfikator jest również wymaganą wartością.

10. **Opcjonalnie:** kliknij pokaż **zaawansowane ustawienia adresu URL,** jeśli chcesz zobaczyć niezajęte wartości.

11. W **atrybutach Użytkownik**wybierz unikatowy identyfikator dla użytkowników w rozwijaniu **Identyfikator użytkownika.**

12. **Opcjonalnie:** kliknij pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika,** aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownicy się logują.

    Aby dodać atrybut:

    1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

    2. Kliknij **pozycję Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

13. Kliknij **pozycję &lt;Konfiguruj&gt; nazwę aplikacji,** aby uzyskać dostęp do dokumentacji dotyczącej konfigurowania logowania jednokrotnego w aplikacji. Ponadto masz adresy URL metadanych i certyfikat wymagany do skonfigurowania aplikacji SSO z aplikacją.

14. Kliknij **przycisk Zapisz,** aby zapisać konfigurację.

15. Przypisz użytkowników do aplikacji.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i dodaj atrybuty użytkownika do wysłania do aplikacji

Aby wybrać identyfikator użytkownika lub dodać atrybuty użytkownika, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację skonfigurowaną logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. W sekcji **Atrybuty użytkownika** wybierz unikatowy identyfikator użytkowników w rozwijaniu **Identyfikator użytkownika.** Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji, aby uwierzytelnić użytkownika.

   >[!NOTE]
   >Usługa Azure AD wybierz format atrybutu NameID (identyfikator użytkownika) na podstawie wybranej wartości lub formatu żądanego przez aplikację w saml AuthRequest. Aby uzyskać więcej informacji, odwiedź artykuł [Protokół saml logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika,** aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownicy się logują.

   Aby dodać atrybut:

   1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij **pozycję Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobieranie metadanych lub certyfikatu usługi Azure AD

Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację skonfigurowaną logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. Przejdź do sekcji **Saml Podpisywanie certyfikatów,** a następnie kliknij pozycję **Pobierz** wartość kolumny. W zależności od tego, co aplikacja wymaga konfigurowania logowania jednokrotnego, zostanie wyświetlone możliwe pobranie metadanych XML lub certyfikatu.

   Usługa Azure AD nie udostępnia adresu URL, aby uzyskać metadane. Metadane można pobrać tylko jako plik XML.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji spoza galerii

Aby skonfigurować aplikację spoza galerii, musisz mieć usługę Azure AD premium, a aplikacja obsługuje SAML 2.0. Aby uzyskać więcej informacji na temat wersji usługi Azure AD, odwiedź stronę [Cennik usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (logowanie do adresu URL, identyfikatora, adresu URL odpowiedzi)

-   [Wybierz identyfikator użytkownika i dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobieranie metadanych i certyfikatów usługi Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   Konfigurowanie wartości metadanych usługi Azure AD w aplikacji (Logowanie do adresu URL, wystawca, adres URL wylogowania i certyfikat)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (logowanie do adresu URL, identyfikatora, adresu URL odpowiedzi)

Aby skonfigurować logowanie jednokrotne dla aplikacji, która nie znajduje się w galerii usługi Azure AD, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij przycisk **Dodaj** w prawym górnym rogu okienka **Aplikacje dla przedsiębiorstw.**

6. kliknij pozycję **Aplikacja spoza galerii** w sekcji **Dodawanie własnej aplikacji.**

7. Wprowadź nazwę aplikacji w polach tekstowych **Nazwa.**

8. Kliknij przycisk **Dodaj,** aby dodać aplikację.

9. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

10. Wybieranie **logowania opartego na SAML w** menu rozwijanym **Tryb**

11. Wprowadź wymagane wartości w **obszarze Domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

    1. Aby skonfigurować aplikację jako wpisałem SSO inicjowanym przez IdP, wprowadź adres URL odpowiedzi i identyfikator.

    2. **Opcjonalnie:** Aby skonfigurować aplikację jako logowania sytego inicjowane jako spso, adres URL logowania jest wartością wymaganą.

12. W **atrybutach Użytkownik**wybierz unikatowy identyfikator dla użytkowników w rozwijaniu **Identyfikator użytkownika.**

13. **Opcjonalnie:** kliknij pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika,** aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownicy się logują.

    Aby dodać atrybut:

    1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

    2. Kliknij **pozycję Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

14. Kliknij **pozycję &lt;Konfiguruj&gt; nazwę aplikacji,** aby uzyskać dostęp do dokumentacji dotyczącej konfigurowania logowania jednokrotnego w aplikacji. Ponadto masz adresy URL usługi Azure AD i certyfikat wymagany dla aplikacji.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i dodaj atrybuty użytkownika do wysłania do aplikacji

Aby wybrać identyfikator użytkownika lub dodać atrybuty użytkownika, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację skonfigurowaną logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. W sekcji **Atrybuty użytkownika** wybierz unikatowy identyfikator użytkowników w rozwijaniu **Identyfikator użytkownika.** Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji, aby uwierzytelnić użytkownika.

   >[!NOTE]
   >Usługa Azure AD wybierz format atrybutu NameID (identyfikator użytkownika) na podstawie wybranej wartości lub formatu żądanego przez aplikację w saml AuthRequest. Aby uzyskać więcej informacji, odwiedź artykuł [Protokół saml logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika,** aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownicy się logują.

   Aby dodać atrybut:

   1.click **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

   2 Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobieranie metadanych lub certyfikatu usługi Azure AD

Aby pobrać metadane aplikacji lub certyfikat z usługi Azure AD, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację skonfigurowaną logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. Przejdź do sekcji **Saml Podpisywanie certyfikatów,** a następnie kliknij pozycję **Pobierz** wartość kolumny. W zależności od tego, co aplikacja wymaga konfigurowania logowania jednokrotnego, zostanie wyświetlone możliwe pobranie metadanych XML lub certyfikatu.

   Usługa Azure AD nie udostępnia adresu URL, aby uzyskać metadane. Metadane można pobrać tylko jako plik XML.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować logowanie jednokrotne hasła dla aplikacji galerii usługi Azure AD

Aby skonfigurować aplikację z galerii usługi Azure AD, należy:

-   Dodawanie aplikacji z galerii usługi Azure AD

-   Konfigurowanie aplikacji do logowania jednokrotnego hasła

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD Gallery, wykonaj poniższe czynności:

1.  Otwórz [witrynę Azure portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  kliknij przycisk **Dodaj** w prawym górnym rogu okienka **Aplikacje dla przedsiębiorstw.**

6.  W polach **tekstowych Wprowadź nazwę** z sekcji **Dodaj z galerii** wpisz nazwę aplikacji

7.  Wybierz aplikację, dla której chcesz skonfigurować logowanie jednokrotne

8.  Przed dodaniem aplikacji można zmienić jej nazwę z pola tekstowego **Nazwa.**

9.  Kliknij przycisk **Dodaj,** aby dodać aplikację.

Po krótkim czasie można wyświetlić okienko konfiguracji aplikacji.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. Wybierz tryb **logowania opartego na hasłach.**

9. Przypisz użytkowników do aplikacji.

10. Ponadto można również podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając **przycisk Aktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy są monitowani o wprowadzenie poświadczeń samodzielnie po uruchomieniu.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować logowanie jednokrotne hasła dla aplikacji spoza galerii

Aby skonfigurować aplikację z galerii usługi Azure AD, należy:

-   [Dodawanie aplikacji spoza galerii](#add-a-non-gallery-application)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji spoza galerii

Aby dodać aplikację z galerii usługi Azure AD Gallery, wykonaj poniższe czynności:

1.  Otwórz [witrynę Azure portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  kliknij przycisk **Dodaj** w prawym górnym rogu okienka **Aplikacje dla przedsiębiorstw.**

6.  kliknij pozycję **Aplikacja niezwiązane z galerią.**

7.  Wprowadź nazwę aplikacji w polach tekstowych **Nazwa.** Wybierz **pozycję Dodaj.**

Po krótkim czasie można wyświetlić okienko konfiguracji aplikacji.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. Wybierz tryb **logowania opartego na hasłach.**

9. Wprowadź **adres URL logowania**. Jest to adres URL, w którym użytkownicy wejdą swoją nazwę użytkownika i hasło, aby się zalogować. Upewnij się, że pola logowania są widoczne pod adresem URL.

10. Przypisz użytkowników do aplikacji.

11. Ponadto można również podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając **przycisk Aktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy są monitowani o wprowadzenie poświadczeń samodzielnie po uruchomieniu.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak przypisać użytkownika bezpośrednio do aplikacji

Aby bezpośrednio przypisać jednego lub więcej użytkowników do aplikacji, wykonaj poniższe czynności:

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

11. Umieść wskaźnik myszy na **użytkowniku** na liście, aby wyświetlić **pole wyboru**. Kliknij pole wyboru obok zdjęcia profilowego lub logo użytkownika, aby dodać go do **wybranej** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika,** wpisz inne imię i **nazwisko** lub **adres e-mail** w polu wyszukiwania według nazwy lub adresu **e-mail,** a następnie kliknij to pole wyboru, aby dodać tego użytkownika do **wybranej** listy.

13. Po zakończeniu wybierania użytkowników kliknij przycisk **Wybierz,** aby dodać ich do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **wybierz rolę** w okienku **Dodaj przypisanie,** aby wybrać rolę przypisaną do wybranych użytkowników.

15. Kliknij przycisk **Przypisz,** aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie wybrani użytkownicy będą mogli uruchamiać te aplikacje w Panelu dostępu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiążą problemu

otwórz bilet pomocy technicznej z następującymi informacjami, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Strefa czasowa i czas/ramy czasowe podczas wystąpienia błędu

-   Ślady skrzypka

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)

