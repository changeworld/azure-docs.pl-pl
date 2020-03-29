---
title: Przypisana aplikacja nie pojawia się w panelu dostępu | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z tym, dlaczego aplikacja nie pojawia się w Panelu dostępu
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
ms.date: 09/09/2018
ms.author: mimart
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10dfcf337dc75a202e781e931f38783291a72fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67272748"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Przypisana aplikacja nie pojawia się w panelu dostępu

Panel dostępu to portal internetowy, który umożliwia użytkownikowi z kontem służbowym w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD przyznał im dostęp. Te aplikacje są konfigurowane w imieniu użytkownika w portalu usługi Azure AD. Aplikacja musi być poprawnie skonfigurowana i przypisana do użytkownika lub grupy, do niej należy użytkownik, aby wyświetlić aplikację w Panelu dostępu.

Typ aplikacji, które użytkownik może widzieć, należy do następujących kategorii:

-   Aplikacje usługi Office 365

-   Microsoft i aplikacje innych firm skonfigurowane z przysłówkiem samosoi opartym na federacji

-   Aplikacje logowania logowania bez hasła

-   Aplikacje z istniejącymi rozwiązaniami SSO

## <a name="general-issues-to-check-first"></a>Kwestie ogólne do sprawdzenia w pierwszej kolejności

-   Jeśli aplikacja została właśnie dodana do użytkownika, spróbuj zalogować się i ponownie zalogować do panelu dostępu użytkownika po kilku minutach, aby sprawdzić, czy aplikacja jest dodana.

-   Jeśli licencja została właśnie usunięta z użytkownika lub grupy, użytkownik jest członkiem tego może zająć dużo czasu, w zależności od rozmiaru i złożoności grupy dla zmian, które mają być wprowadzone. Przed zalogowaniem się do Panelu dostępu pozostaw na dodatkowy czas.

## <a name="problems-related-to-application-configuration"></a>Problemy związane z konfiguracją aplikacji

Aplikacja może nie być wyświetlana w Panelu dostępu użytkownika, ponieważ nie jest poprawnie skonfigurowana. Poniżej znajduje się kilka sposobów rozwiązywania problemów związanych z konfiguracją aplikacji:

-   [Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji galerii usługi Azure AD](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji spoza galerii](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Jak skonfigurować aplikację logowania jednokrotnego hasła dla aplikacji galerii usługi Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Jak skonfigurować hasło aplikacji logowania jednokrotnego dla aplikacji spoza galerii](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji galerii usługi Azure AD

Wszystkie aplikacje w galerii usługi Azure AD włączone z możliwością logowania jednokrotnego w przedsiębiorstwie ma dostępny samouczek krok po kroku. Można uzyskać dostęp do [listy samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory, aby](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) uzyskać szczegółowe wskazówki krok po kroku.

Aby skonfigurować aplikację z galerii usługi Azure AD, należy:

-   [Dodawanie aplikacji z galerii usługi Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (logowanie do adresu URL, identyfikatora, adresu URL odpowiedzi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Wybierz identyfikator użytkownika i dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobieranie metadanych i certyfikatów usługi Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurowanie wartości metadanych usługi Azure AD w aplikacji (Logowanie do adresu URL, wystawca, adres URL wylogowania i certyfikat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

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

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurowanie logowania jednokrotnego dla aplikacji z galerii usługi Azure AD

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8. Z listy rozwijanej **Tryb** wybierz **opcję Logowanie oparte na SAML.**

9. Wprowadź wymagane wartości w **obszarze Domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

   1. Aby skonfigurować aplikację jako logowania sytego inicjowane jako spso, Sign on URL jest to wartość wymagana. W przypadku niektórych aplikacji identyfikator jest również wymaganą wartością.

   2. Aby skonfigurować aplikację jako identyfikatora SSO inicjowanego przez IdP, adres URL odpowiedzi jest wymaganą wartością. W przypadku niektórych aplikacji identyfikator jest również wymaganą wartością.

10. **Opcjonalnie:** kliknij pokaż **zaawansowane ustawienia adresu URL,** jeśli chcesz zobaczyć niezajęte wartości.

11. W **atrybutach Użytkownik**wybierz unikatowy identyfikator dla użytkowników w rozwijaniu **Identyfikator użytkownika.**

12. **Opcjonalnie:** kliknij pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika,** aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownicy się logują.

    Aby dodać atrybut:

    1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

    2. Kliknij pozycję **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

13. Kliknij **pozycję &lt;Konfiguruj&gt; nazwę aplikacji,** aby uzyskać dostęp do dokumentacji dotyczącej konfigurowania logowania jednokrotnego w aplikacji. Ponadto masz adresy URL metadanych i certyfikat wymagany do skonfigurowania aplikacji SSO.

14. kliknij przycisk **Zapisz,** aby zapisać konfigurację.

15. Przypisz użytkowników do aplikacji.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i dodaj atrybuty użytkownika do wysłania do aplikacji

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
   >Usługa Azure AD wybiera format atrybutu NameID (identyfikator użytkownika) na podstawie wybranej wartości lub formatu żądanego przez aplikację w saml AuthRequest. Aby uzyskać więcej informacji, odwiedź artykuł [Protokół saml logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika,** aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownicy się logują.

   Aby dodać atrybut:

   1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij pozycję **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobieranie metadanych lub certyfikatu usługi Azure AD

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji spoza galerii

Aby skonfigurować aplikację spoza galerii, musisz mieć usługę Azure AD premium, a aplikacja obsługuje SAML 2.0. Aby uzyskać więcej informacji na temat wersji usługi Azure AD, odwiedź stronę [Cennik usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (logowanie do adresu URL, identyfikatora, adresu URL odpowiedzi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Wybierz identyfikator użytkownika i dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobieranie metadanych i certyfikatów usługi Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurowanie wartości metadanych usługi Azure AD w aplikacji (Logowanie do adresu URL, wystawca, adres URL wylogowania i certyfikat)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurowanie wartości metadanych aplikacji w usłudze Azure AD (logowanie do adresu URL, identyfikatora, adresu URL odpowiedzi)

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

10. Wybierz **pozycję Logowanie oparte na SAML w** menu rozwijanym **Tryb.**

11. Wprowadź wymagane wartości w **obszarze Domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

    1. Aby skonfigurować aplikację jako wpisałem SSO inicjowanym przez IdP, wprowadź adres URL odpowiedzi i identyfikator.

    2.  **Opcjonalnie:** Aby skonfigurować aplikację jako logowania sytego inicjowane jako spso, Sign on URL jest to wartość wymagana.

12. W **atrybutach Użytkownik**wybierz unikatowy identyfikator dla użytkowników w rozwijaniu **Identyfikator użytkownika.**

13. **Opcjonalnie:** kliknij pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika,** aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownicy się logują.

    Aby dodać atrybut:

    1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

    2. Kliknij **pozycję Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

14. Kliknij **pozycję &lt;Konfiguruj&gt; nazwę aplikacji,** aby uzyskać dostęp do dokumentacji dotyczącej konfigurowania logowania jednokrotnego w aplikacji. Ponadto masz adresy URL usługi Azure AD i certyfikaty wymagane dla aplikacji.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i dodaj atrybuty użytkownika do wysłania do aplikacji

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
   >Usługa Azure AD wybiera format atrybutu NameID (identyfikator użytkownika) na podstawie wybranej wartości lub formatu żądanego przez aplikację w saml AuthRequest. Aby uzyskać więcej informacji, odwiedź artykuł [Protokół saml logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) w sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij pozycję **Wyświetl i edytuj wszystkie inne atrybuty użytkownika,** aby edytować atrybuty, które mają być wysyłane do aplikacji w tokenie SAML, gdy użytkownicy się logują.

   Aby dodać atrybut:

   1. Kliknij pozycję **Dodaj atrybut**. Wprowadź **nazwę** i wybierz **wartość** z listy rozwijanej.

   2. Kliknij **pozycję Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobieranie metadanych lub certyfikatu usługi Azure AD

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować logowanie jednokrotne hasła dla aplikacji galerii usługi Azure AD

Aby skonfigurować aplikację z galerii usługi Azure AD, należy:

-   [Dodawanie aplikacji z galerii usługi Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

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

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

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

9. [Przypisz użytkowników do aplikacji](#how-to-assign-a-user-to-an-application-directly).

10. Ponadto można również podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając **przycisk Aktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy są monitowani o wprowadzenie poświadczeń samodzielnie po uruchomieniu.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować logowanie jednokrotne hasła dla aplikacji spoza galerii

Aby skonfigurować aplikację z galerii usługi Azure AD, należy:

-   [Dodawanie aplikacji spoza galerii](#add-a-non-gallery-application)

-   [Konfigurowanie aplikacji do logowania jednokrotnego hasła](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji spoza galerii

Aby dodać aplikację z galerii usługi Azure AD Gallery, wykonaj poniższe czynności:

1.  Otwórz [witrynę Azure portal](https://portal.azure.com) i zaloguj się jako **administrator globalny** lub **współadministrator**.

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  kliknij przycisk **Dodaj** w prawym górnym rogu okienka **Aplikacje dla przedsiębiorstw.**

6.  kliknij pozycję **Aplikacja niezwiązane z galerią.**

7.  Wprowadź nazwę aplikacji w polach tekstowych **Nazwa.** Wybierz **pozycję Dodaj.**

Po krótkim czasie można wyświetlić okienko konfiguracji aplikacji.

#### <a name="configure-the-application-for-password-single-sign-on"></a><a name="configure-the-application-for-password-single-sign-on-1"></a>Konfigurowanie aplikacji do logowania jednokrotnego hasła

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj poniższe czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny** lub **współadministrator.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

    1.  Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6.  Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji kliknij **logowanie jednokrotne** z menu nawigacji po lewej stronie aplikacji.

8.  Wybierz tryb **logowania opartego na hasłach.**

9.  Wprowadź **adres URL logowania**. Jest to adres URL, w którym użytkownicy wejdą swoją nazwę użytkownika i hasło, aby się zalogować. Upewnij się, że pola logowania są widoczne pod adresem URL.

10. [Przypisz użytkowników do aplikacji](#how-to-assign-a-user-to-an-application-directly).

11. Ponadto można również podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając **przycisk Aktualizuj poświadczenia** i wprowadzając nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy są monitowani o wprowadzenie poświadczeń samodzielnie po uruchomieniu.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemy związane z przypisywaniem aplikacji użytkownikom

Użytkownik może nie widzieć aplikacji w panelu dostępu, ponieważ nie jest przypisany do aplikacji. Poniżej znajduje się kilka sposobów, aby sprawdzić:

-   [Sprawdź, czy użytkownik jest przypisany do aplikacji](#check-if-a-user-is-assigned-to-the-application)

-   [Jak przypisać użytkownika bezpośrednio do aplikacji](#how-to-assign-a-user-to-an-application-directly)

-   [Sprawdzanie, czy użytkownik jest przypisany do licencji związanej z aplikacją](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Jak przypisać licencję do użytkownika](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Sprawdź, czy użytkownik jest przypisany do aplikacji

Aby sprawdzić, czy użytkownik jest przypisany do aplikacji, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

6. **Wyszukaj** nazwę danej aplikacji.

7. kliknij pozycję **Użytkownicy i grupy**.

8. Sprawdź, czy użytkownik jest przypisany do aplikacji.

   * Jeśli nie wykonaj kroki w "Jak przypisać użytkownika do aplikacji bezpośrednio", aby to zrobić.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak przypisać użytkownika bezpośrednio do aplikacji

Aby bezpośrednio przypisać jednego lub więcej użytkowników do aplikacji, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny**.

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

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Sprawdź, czy użytkownik ma licencję związaną z aplikacją

Aby sprawdzić przypisane licencje użytkownika, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5. kliknij pozycję **Wszyscy użytkownicy**.

6. **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7. kliknij **pozycję Licencje,** aby zobaczyć, które licencje użytkownik aktualnie ma przypisane.

   * Jeśli użytkownik jest przypisany do licencji pakietu Office, umożliwia to wyświetlanie aplikacji pakietu Office pierwszej firmy w Panelu dostępu użytkownika.

### <a name="how-to-assign-a-user-a-license"></a>Jak przypisać użytkownikowi licencję 

Aby przypisać licencję do użytkownika, wykonaj poniższe czynności:

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

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemy związane z przypisywaniem aplikacji do grup

Użytkownik może widzieć aplikację w panelu dostępu, ponieważ jest częścią grupy, do którą przypisano aplikację. Poniżej znajduje się kilka sposobów, aby sprawdzić:

-   [Sprawdzanie członkostwa w grupie użytkownika](#check-a-users-group-memberships)

-   [Jak przypisać aplikację bezpośrednio do grupy](#how-to-assign-an-application-to-a-group-directly)

-   [Sprawdzanie, czy użytkownik jest częścią grupy przypisanej do licencji](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Jak przypisać licencję do grupy](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Sprawdzanie członkostwa w grupie użytkownika

Aby sprawdzić przynależność do grupy, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5. kliknij pozycję **Wszyscy użytkownicy**.

6. **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7. kliknij pozycję **Grupy**.

8. Sprawdź, czy użytkownik jest częścią grupy przypisanej do aplikacji.

   * Jeśli chcesz usunąć użytkownika z grupy, **kliknij wiersz** grupy i wybierz pozycję Usuń.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Jak przypisać aplikację bezpośrednio do grupy

Aby bezpośrednio przypisać jedną lub więcej grup do aplikacji, wykonaj poniższe czynności:

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny**.

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij polecenie **Aplikacje przedsiębiorstwa** z menu nawigacji po lewej stronie usługi Azure Active Directory.

5. kliknij pozycję **Wszystkie aplikacje,** aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, która ma być pokazana w tym miejscu, użyj kontrolki **Filtruj** u góry **listy Wszystkie aplikacje** i ustaw opcję **Pokaż** na **Wszystkie aplikacje.**

6. Wybierz aplikację, do której chcesz przypisać użytkownika z listy.

7. Po załadowaniu aplikacji kliknij pozycję **Użytkownicy i grupy** z menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** u góry listy **Użytkownicy i grupy,** aby otworzyć okienko **Dodaj przypisanie.**

9. kliknij selektor **Użytkownicy i grupy** w okienku **Dodawanie przypisania.**

10. Wpisz **pełną nazwę grupy,** którą chcesz przypisać do pola wyszukiwania **według nazwy lub adresu e-mail.**

11. Umieść wskaźnik myszy na **grupie** na liście, aby wyświetlić **pole wyboru**. Kliknij pole wyboru obok zdjęcia profilowego lub logo grupy, aby dodać go do **wybranej** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jedną grupę,** wpisz inną pełną nazwę **grupy** w polu wyszukiwania według nazwy lub **adresu e-mail,** a następnie kliknij to pole wyboru, aby dodać tę grupę do **wybranej** listy.

13. Po zakończeniu wybierania grup kliknij przycisk **Wybierz,** aby dodać je do listy użytkowników i grup, które mają być przypisane do aplikacji.

14. **Opcjonalnie:** kliknij selektor **wybierz rolę** w okienku **Dodaj przypisanie,** aby wybrać rolę przypisaną do wybranych grup.

15. Kliknij przycisk **Przypisz,** aby przypisać aplikację do wybranych grup.

Po krótkim czasie wybrani użytkownicy będą mogli uruchamiać te aplikacje w Panelu dostępu.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Sprawdzanie, czy użytkownik jest częścią grupy przypisanej do licencji

1. Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3. Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4. kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5. kliknij pozycję **Wszyscy użytkownicy**.

6. **Wyszukaj** użytkownika, którego jesteś zainteresowany, i **kliknij wiersz,** aby wybrać.

7. kliknij pozycję **Grupy**.

8. kliknij wiersz określonej grupy.

9. kliknij **pozycję Licencje,** aby zobaczyć, które licencje grupa została do niej przypisana.

   * Jeśli grupa jest przypisana do licencji pakietu Office, może to umożliwić niektórym aplikacjom pakietu Office pierwszej firmy wyświetlenie w Panelu dostępu użytkownika.

### <a name="how-to-assign-a-license-to-a-group"></a>Jak przypisać licencję do grupy

Aby przypisać licencję do grupy, wykonaj poniższe czynności:

1.  Otwórz [**witrynę Azure portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie usługi Azure Active Directory,** klikając **pozycję Wszystkie usługi** u góry głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Usługa Azure Active Directory"** w polu wyszukiwania filtru i wybierz element **usługi Azure Active Directory.**

4.  kliknij **pozycję Użytkownicy i grupy** w menu nawigacyjnym.

5.  kliknij pozycję **Wszystkie grupy**.

6.  **Wyszukaj** interesującą Cię grupę i **kliknij wiersz,** aby ją zaznaczyć.

7.  kliknij **pozycję Licencje,** aby zobaczyć, które licencje grupa jest aktualnie przypisana.

8.  kliknij przycisk **Przypisz.**

9.  Wybierz **jeden lub więcej produktów** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij element **opcji przydziału,** aby dokładnie przypisać produkty. Po zakończeniu kliknij przycisk **Ok.**

11. Kliknij przycisk **Przypisz,** aby przypisać te licencje do tej grupy. Może to zająć dużo czasu, w zależności od wielkości i złożoności grupy.

>[!NOTE]
>Aby to zrobić szybciej, należy rozważyć tymczasowe przypisanie licencji do użytkownika bezpośrednio. 
>
>

## <a name="next-steps"></a>Następne kroki
[Dodawanie nowych użytkowników do usługi Azure Active Directory](./../fundamentals/add-users-azure-active-directory.md)

