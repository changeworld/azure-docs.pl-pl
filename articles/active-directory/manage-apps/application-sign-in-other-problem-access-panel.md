---
title: Problemy z logowaniem do aplikacji przy użyciu panelu dostępu | Dokumentacja firmy Microsoft
description: Jak rozwiązywać problemy z dostępu do aplikacji z poziomu panelu dostępu usługi Microsoft Azure AD w myapps.microsoft.com
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
ms.openlocfilehash: 51e486e8eb2fef04c1b876dff3de644dda4aaf2e
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65781134"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problemy z logowaniem do aplikacji przy użyciu panelu dostępu

Panel dostępu jest portal sieci web, która pozwala użytkownikom przy użyciu konta służbowego w usłudze Azure Active Directory (Azure AD) do wyświetlania i uruchamiania aplikacji opartych na chmurze, że administrator usługi Azure AD udzielił im dostępu do. 

Te aplikacje są skonfigurowane w imieniu użytkownika w portalu usługi Azure AD. Aplikacja musi prawidłowo skonfigurowane i przypisane do użytkownika lub grupy, których użytkownik jest członkiem, aby wyświetlić aplikację w panelu dostępu.

Typ aplikacji, którą użytkownik może być widoczny można podzielić na następujące kategorie:

-   Aplikacje usługi Office 365

-   Aplikacje firmy Microsoft i innych firm, skonfigurowaną opartych na federacyjnego logowania jednokrotnego

-   Aplikacje oparte na hasłach logowania jednokrotnego

-   Aplikacje z istniejącymi rozwiązaniami logowania jednokrotnego

## <a name="general-issues-to-check-first"></a>Zagadnienia ogólne w celu sprawdzenia

-   Upewnij się, że w sieci za pomocą **przeglądarki** który spełnia minimalne wymagania dotyczące panelu dostępu.

-   Upewnij się, w przeglądarce dodano adres URL aplikacji w celu jego **Zaufane witryny**.

-   Upewnij się sprawdzić, aplikacja jest **skonfigurowane** poprawnie.

-   Upewnij się, że konto użytkownika jest **włączone** do logowania.

-   Upewnij się, że konto użytkownika jest **bez blokady.**

-   Upewnij się, że użytkownika **nie wygasł lub zapomnienia hasła.**

-   Upewnij się, że **uwierzytelnianie wieloskładnikowe** nie blokuje dostęp użytkownika.

-   Upewnij się, że **zasad dostępu warunkowego** lub **Identity Protection** zasad nie blokuje dostęp użytkownika.

-   Upewnij się, że użytkownik **informacje kontaktowe uwierzytelniania** pozostają aktualne, aby umożliwić uwierzytelnianie wieloskładnikowe lub dostępu warunkowego zasad, które mają być egzekwowane.

-   Upewnij się, że także spróbuj wyczyszczenie plików cookie w przeglądarce i podjęcie próby ponownego zalogowania.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Spełniających wymagania dotyczące przeglądarki dla panelu dostępu

Panel dostępu wymaga przeglądarki, która obsługuje język JavaScript i CSS została włączona. Aby użyć opartego na hasłach logowanie jednokrotne (SSO) w panelu dostępu, rozszerzenie panelu dostępu musi być zainstalowany w przeglądarce użytkownika. To rozszerzenie jest pobierany automatycznie, gdy użytkownik wybierze aplikacji, która jest skonfigurowana na podstawie hasła logowania jednokrotnego.

W przypadku logowania jednokrotnego opartego na hasłach może być przeglądarki przez użytkownika końcowego:

-   Internet Explorer 8, 9, 10, 11 — Windows 7 lub nowszy

-   Microsoft Edge w systemie Windows 10 Anniversary Edition lub nowszy

-   Chrome — W systemie Windows 7 lub nowszy i System MacOS x lub nowszym

-   Firefox 26.0 lub później — w Windows XP z dodatkiem SP2 lub nowszy oraz w systemie Mac OS X 10,6 lub nowszej

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak zainstalować rozszerzenie przeglądarki panelu dostępu

Aby zainstalować rozszerzenie przeglądarki panelu dostępu, wykonaj następujące czynności:

1.  Otwórz [panelu dostępu](https://myapps.microsoft.com) w jednym z obsługiwanych przeglądarek i zaloguj się jako **użytkownika** w usłudze Azure AD.

2.  Kliknij przycisk **logowania jednokrotnego hasła aplikacji** w panelu dostępu.

3.  W oknie komunikatu z pytaniem zainstalować oprogramowanie, wybierz **Zainstaluj teraz**.

4.  Oparte na przeglądarce, które są kierowane do łącza pobierania. **Dodaj** rozszerzenie do przeglądarki.

5.  Jeśli przeglądarka zapyta, wybrać opcję **Włącz** lub **Zezwalaj** rozszerzenia.

6.  Po zainstalowaniu **ponowne uruchomienie** sesji przeglądarki.

7.  Zaloguj się do panelu dostępu i zobacz, jeśli możesz to zrobić **Uruchom** logowania jednokrotnego hasła aplikacji

Mogą również pobrać rozszerzenie dla programu Chrome i Microsoft Edge z poniższych linków bezpośrednich:

-   [Rozszerzenie panelu dostępu dla programu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozszerzenie panelu dostępu do przeglądarki Microsoft Edge](https://www.microsoft.com/store/apps/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji z galerii usługi Azure AD

Wszystkie aplikacji w galerii usługi Azure AD, włączone za pomocą funkcji logowania jednokrotnego dla firm ma dostępne samouczek krok po kroku. Możesz uzyskać dostęp [listę samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) szczegółowe wskazówki krok po kroku.

Aby skonfigurować aplikację z galerii usługi Azure AD, które należy:

-   Dodawanie aplikacji z galerii usługi Azure AD

-   [Skonfiguruj wartości metadanych aplikacji w usłudze Azure AD (adres URL odpowiedzi adresu URL, identyfikator logowania)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobieranie metadanych usługi Azure AD i certyfikatów](#download-the-azure-ad-metadata-or-certificate)

-   [Skonfiguruj wartości metadanych usługi Azure AD w aplikacji (logowanie jednokrotne adresu URL, wystawcy, adres URL wylogowania i certyfikat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Przypisywanie użytkowników do aplikacji

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [witryny Azure portal](https://portal.azure.com) i zaloguj się jako **administratora globalnego** lub **współadministratora**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **Dodaj** znajdujący się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** okienka.

6.  W **wprowadź nazwę** pole tekstowe z **Dodaj z galerii** sekcji, wpisz nazwę aplikacji.

7.  Wybierz aplikację, którą chcesz skonfigurować dla logowania jednokrotnego.

8.  Przed dodaniem aplikacji, można zmienić jego nazwę, z **nazwa** pola tekstowego.

9.  Kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

Po krótkim czasie możesz zobaczyć okienko konfiguracji aplikacji.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurowanie logowania jednokrotnego dla aplikacji z galerii usługi Azure AD

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Wybierz **opartej na SAML logowania jednokrotnego** z **tryb** listy rozwijanej.

9. Wprowadź wymagane wartości w **domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

   1. Aby skonfigurować aplikację jako zainicjowanego przez dostawcę usług rejestracji Jednokrotnej, adres URL logowania jest wymagana wartość. W niektórych aplikacjach identyfikator jest również wymagana wartość.

   2. Aby skonfigurować aplikację jako logowanie Jednokrotne zainicjowane przez dostawcę tożsamości, adres URL odpowiedzi jest wymagana wartość. W niektórych aplikacjach identyfikator jest również wymagana wartość.

10. **Opcjonalnie:** kliknij **Pokaż zaawansowane ustawienia adresu URL** aby zobaczyć wartości — wymagane.

11. W **atrybutów użytkownika**, wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej.

12. **Opcjonalnie:** kliknij **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** atrybuty do wysłania do aplikacji w tokenie języka SAML, gdy użytkownicy logują się.

    Aby dodać atrybut:

    1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** a następnie wybierz **wartość** z listy rozwijanej.

    2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

13. Kliknij przycisk **Konfiguruj &lt;Nazwa aplikacji&gt;**  dokumentacji dostępu dotyczące sposobu konfigurowania logowania jednokrotnego w aplikacji. Ponadto trzeba metadanych adresów URL i certyfikatu wymagane do konfiguracji logowania jednokrotnego w aplikacji.

14. Kliknij przycisk **Zapisz** Aby zapisać konfigurację.

15. Przypisywanie użytkowników do aplikacji.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji

Aby wybrać identyfikator użytkownika lub Dodaj atrybuty użytkowników, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji ma być wyświetlane w tym miejscu, należy użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję  **Wszystkie aplikacje.**

6. Wybierz aplikację, zostały skonfigurowane logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. W obszarze **atrybutów użytkownika** wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej. Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji w celu uwierzytelnienia użytkownika.

   >[!NOTE]
   >Wybrana usługa Azure AD wybierz pozycję formatu NameID atrybutu (identyfikator użytkownika) na podstawie wartości lub format żądany przez aplikację w SAML AuthRequest. Więcej informacji na ten temat można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** atrybuty do wysłania do aplikacji w tokenie języka SAML, gdy użytkownicy logują się.

   Aby dodać atrybut:

   1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** a następnie wybierz **wartość** z listy rozwijanej.

   2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobierz metadane usługi Azure AD lub certyfikat

Aby pobrać metadanych aplikacji lub certyfikatu z usługi Azure AD, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, zostały skonfigurowane logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Przejdź do **certyfikat podpisywania SAML** sekcji, a następnie kliknij przycisk **Pobierz** wartości w kolumnie. W zależności od tego, jakie aplikacji wymagane jest skonfigurowanie logowania jednokrotnego zobaczysz opcję Pobierz kod XML metadanych albo certyfikatu.

   Usługa Azure AD nie zawiera adres URL, aby pobrać metadane. Można pobrać metadanych jako plik XML.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji spoza galerii

Aby skonfigurować aplikacji spoza galerii, musisz mieć usługa Azure AD premium i aplikacja obsługuje SAML 2.0. Aby uzyskać więcej informacji o wersji usługi Azure AD, odwiedź stronę [cennika usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   Skonfiguruj wartości metadanych aplikacji w usłudze Azure AD (adres URL odpowiedzi adresu URL, identyfikator logowania)

-   [Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobieranie metadanych usługi Azure AD i certyfikatów](#download-the-azure-ad-metadata-or-certificate)

-   Skonfiguruj wartości metadanych usługi Azure AD w aplikacji (logowanie jednokrotne adresu URL, wystawcy, adres URL wylogowania i certyfikat)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Skonfiguruj wartości metadanych aplikacji w usłudze Azure AD (adres URL odpowiedzi adresu URL, identyfikator logowania)

Aby skonfigurować logowanie jednokrotne dla aplikacji, która nie znajduje się w galerii usługi Azure AD, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **Dodaj** znajdujący się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** okienka.

6. Kliknij przycisk **aplikacji spoza galerii** w **Dodaj własną aplikację** sekcji.

7. Wprowadź nazwę aplikacji w **nazwa** pola tekstowego.

8. Kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

9. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

10. Wybierz **opartej na SAML logowania jednokrotnego** w **tryb** listy rozwijanej

11. Wprowadź wymagane wartości w **domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

    1. Aby skonfigurować aplikację jako logowanie Jednokrotne zainicjowane przez dostawcę tożsamości, wprowadź adres URL odpowiedzi i identyfikator.

    2. **Opcjonalnie:** Aby skonfigurować aplikację jako zainicjowanego przez dostawcę usług rejestracji Jednokrotnej, adres URL logowania jest wymagana wartość.

12. W **atrybutów użytkownika**, wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej.

13. **Opcjonalnie:** kliknij **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** atrybuty do wysłania do aplikacji w tokenie języka SAML, gdy użytkownicy logują się.

    Aby dodać atrybut:

    1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** a następnie wybierz **wartość** z listy rozwijanej.

    2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

14. Kliknij przycisk **Konfiguruj &lt;Nazwa aplikacji&gt;**  dokumentacji dostępu dotyczące sposobu konfigurowania logowania jednokrotnego w aplikacji. Ponadto trzeba adresy URL platformy Azure AD i certyfikat jest wymagany dla aplikacji.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji

Aby wybrać identyfikator użytkownika lub Dodaj atrybuty użytkowników, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, zostały skonfigurowane logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. W obszarze **atrybutów użytkownika** wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej. Wybrana opcja musi być zgodna z oczekiwaną wartością w aplikacji w celu uwierzytelnienia użytkownika.

   >[!NOTE]
   >Wybrana usługa Azure AD wybierz pozycję formatu NameID atrybutu (identyfikator użytkownika) na podstawie wartości lub format żądany przez aplikację w SAML AuthRequest. Więcej informacji na ten temat można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** atrybuty do wysłania do aplikacji w tokenie języka SAML, gdy użytkownicy logują się.

   Aby dodać atrybut:

   1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** a następnie wybierz **wartość** z listy rozwijanej.

   Kliknij 2 **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobierz metadane usługi Azure AD lub certyfikat

Aby pobrać metadanych aplikacji lub certyfikatu z usługi Azure AD, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, zostały skonfigurowane logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Przejdź do **certyfikat podpisywania SAML** sekcji, a następnie kliknij przycisk **Pobierz** wartości w kolumnie. W zależności od tego, jakie aplikacji wymagane jest skonfigurowanie logowania jednokrotnego zobaczysz opcję Pobierz kod XML metadanych albo certyfikatu.

   Usługa Azure AD nie zawiera adres URL, aby pobrać metadane. Można pobrać metadanych jako plik XML.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować hasło logowania jednokrotnego dla aplikacji z galerii usługi Azure AD

Aby skonfigurować aplikację z galerii usługi Azure AD, które należy:

-   Dodawanie aplikacji z galerii usługi Azure AD

-   Konfigurowanie aplikacji dla hasła logowania jednokrotnego

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [witryny Azure portal](https://portal.azure.com) i zaloguj się jako **administratora globalnego** lub **współadministratora**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **Dodaj** znajdujący się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** okienka.

6.  W **wprowadź nazwę** pole tekstowe z **Dodaj z galerii** sekcji, wpisz nazwę aplikacji

7.  Wybierz aplikację, dla której chcesz skonfigurować logowanie jednokrotne

8.  Przed dodaniem aplikacji, można zmienić jego nazwę, z **nazwa** pola tekstowego.

9.  Kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

Po krótkim czasie możesz zobaczyć okienko konfiguracji aplikacji.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji dla hasła logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Wybierz tryb **opartego na hasłach logowania jednokrotnego.**

9. Przypisywanie użytkowników do aplikacji.

10. Ponadto, możesz także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając **poświadczenia aktualizacji** i wprowadzić nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy monit o podanie poświadczeń, się po uruchomieniu.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować hasło logowania jednokrotnego dla aplikacji spoza galerii

Aby skonfigurować aplikację z galerii usługi Azure AD, które należy:

-   [Dodawanie aplikacji spoza galerii](#add-a-non-gallery-application)

-   [Konfigurowanie aplikacji dla hasła logowania jednokrotnego](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji spoza galerii

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [witryny Azure portal](https://portal.azure.com) i zaloguj się jako **administratora globalnego** lub **współadministratora**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **Dodaj** znajdujący się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** okienka.

6.  Kliknij przycisk **aplikacji spoza galerii.**

7.  Wprowadź nazwę aplikacji na platformie **nazwa** pola tekstowego. Wybierz **Dodaj.**

Po krótkim czasie można zobaczyć okienko konfiguracji aplikacji.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji dla hasła logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Wybierz tryb **opartego na hasłach logowania jednokrotnego.**

9. Wprowadź **adres URL logowania**. To jest adres URL, w którym użytkownicy wprowadzają swoją nazwę i hasło do logowania. Upewnij się, że pola logowania są widoczne pod adresem URL.

10. Przypisywanie użytkowników do aplikacji.

11. Ponadto, możesz także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając **poświadczenia aktualizacji** i wprowadzić nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy monit o podanie poświadczeń, się po uruchomieniu.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak przypisać użytkownika do aplikacji bezpośrednio

Aby przypisać co najmniej jednego użytkownika do aplikacji bezpośrednio, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7. Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** przycisk w górnej części **użytkowników i grup** liście, aby otworzyć **Dodaj przydziału** okienka.

9. Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Pełna nazwa** lub **adres e-mail** użytkownika, jesteś zainteresowany przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **użytkownika** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok logo, aby dodać użytkownika, aby lub zdjęcie w profilu użytkownika **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednego użytkownika**, typ w innym **Pełna nazwa** lub **adres e-mail** do **wyszukiwanie według nazwy lub adresu e-mail** pole wyszukiwania, a następnie kliknij pole wyboru, aby dodać użytkownika do **wybrane** listy.

13. Gdy to zrobisz, Wybieranie użytkowników, kliknij przycisk **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę, aby przypisać użytkownikom wybrania.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych użytkowników.

Po krótkim czasie użytkowników, dla których wybrano można uruchamiać te aplikacje w panelu dostępu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie Rozwiąż ten problem

Otwórz bilet pomocy technicznej, używając następujących informacji, jeśli jest dostępny:

-   Identyfikator błędu korelacji

-   Nazwy UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Występuje, strefę czasową i przedziału czasu/czasu podczas błędu

-   Ślady narzędzia fiddler

## <a name="next-steps"></a>Kolejne kroki
[Udostępnij logowanie jednokrotne do aplikacji serwera Proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)

