---
title: Nie widać przypisanej aplikacji na panelu dostępu | Dokumentacja firmy Microsoft
description: Rozwiązywania problemów związanych z aplikacji nie są wyświetlane w panelu dostępu
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
ms.openlocfilehash: 9d16efbd8ea2fa37ac9572e9dc1ba3ef67deaeaf
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784394"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Nie widać przypisanej aplikacji na panelu dostępu

Panel dostępu jest portal sieci web, która pozwala użytkownikom przy użyciu konta służbowego w usłudze Azure Active Directory (Azure AD) do wyświetlania i uruchamiania aplikacji opartych na chmurze, że administrator usługi Azure AD udzielił im dostępu do. Te aplikacje są skonfigurowane w imieniu użytkownika w portalu usługi Azure AD. Aplikacja musi prawidłowo skonfigurowane i przypisane do użytkownika lub grupy, których użytkownik jest członkiem, aby wyświetlić aplikację w panelu dostępu.

Typ aplikacji, którą użytkownik może być widoczny można podzielić na następujące kategorie:

-   Aplikacje usługi Office 365

-   Aplikacje firmy Microsoft i innych firm, skonfigurowaną opartych na federacyjnego logowania jednokrotnego

-   Aplikacje oparte na hasłach logowania jednokrotnego

-   Aplikacje z istniejącymi rozwiązaniami logowania jednokrotnego

## <a name="general-issues-to-check-first"></a>Zagadnienia ogólne w celu sprawdzenia

-   Jeśli aplikacja została właśnie został dodany do użytkownika, spróbuj zarejestrować i pomniejszać ponownie do panelu dostępu użytkownika po kilku minutach aby zobaczyć, jeśli jest dodawana aplikacja.

-   Jeśli licencja właśnie został usunięty z użytkownika lub grupę, do których użytkownik jest członkiem to może potrwać długo w zależności od rozmiaru i złożoności grupy zmian, które ma zostać wykonane. Zezwalaj na dodatkowy czas, przed zalogowaniem się do panelu dostępu.

## <a name="problems-related-to-application-configuration"></a>Problemy związane z konfiguracji aplikacji

Aplikacja może być wyświetlana w panelu dostępu użytkownika, ponieważ nie został prawidłowo skonfigurowany. Poniżej przedstawiono kilka sposobów, można rozwiązywać problemy związane z konfiguracji aplikacji:

-   [Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji z galerii usługi Azure AD](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji spoza galerii](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Jak skonfigurować hasło pojedynczej logowania jednokrotnego aplikacji dla aplikacji z galerii usługi Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Jak skonfigurować hasło pojedynczej logowania jednokrotnego aplikacji dla aplikacji spoza galerii](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji z galerii usługi Azure AD

Wszystkie aplikacje w galerii usługi Azure AD, włączone za pomocą funkcji logowania jednokrotnego dla firm ma dostępne samouczek krok po kroku. Możesz uzyskać dostęp [listę samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) szczegółowe wskazówki krok po kroku.

Aby skonfigurować aplikację z galerii usługi Azure AD, które należy:

-   [Dodawanie aplikacji z galerii usługi Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Skonfiguruj wartości metadanych aplikacji w usłudze Azure AD (adres URL odpowiedzi adresu URL, identyfikator logowania)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobieranie metadanych usługi Azure AD i certyfikatów](#download-the-azure-ad-metadata-or-certificate)

-   [Skonfiguruj wartości metadanych usługi Azure AD w aplikacji (logowanie jednokrotne adresu URL, wystawcy, adres URL wylogowania i certyfikat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

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

Po krótkim czasie można zobaczyć okienko konfiguracji aplikacji.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurowanie logowania jednokrotnego dla aplikacji z galerii usługi Azure AD

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7. Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8. Wybierz **opartej na SAML logowania jednokrotnego** z **tryb** listy rozwijanej.

9. Wprowadź wymagane wartości w **domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

   1. Aby skonfigurować aplikację jako logowanie Jednokrotne zainicjowane przez Dostawcę, adres URL logowania jest wymagana wartość. W niektórych aplikacjach identyfikator jest również wymagana wartość.

   2. Aby skonfigurować aplikację jako logowanie Jednokrotne zainicjowane przez dostawcę tożsamości, adres URL odpowiedzi jest wymagana wartość. W niektórych aplikacjach identyfikator jest również wymagana wartość.

10. **Opcjonalnie:** kliknij **Pokaż zaawansowane ustawienia adresu URL** aby zobaczyć wartości — wymagane.

11. W **atrybutów użytkownika**, wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej.

12. **Opcjonalnie:** kliknij **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** atrybuty do wysłania do aplikacji w tokenie języka SAML, gdy użytkownicy logują się.

    Aby dodać atrybut:

    1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** a następnie wybierz **wartość** z listy rozwijanej.

    2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

13. Kliknij przycisk **Konfiguruj &lt;Nazwa aplikacji&gt;**  dokumentacji dostępu dotyczące sposobu konfigurowania logowania jednokrotnego w aplikacji. Ponadto trzeba metadanych adresów URL i certyfikatu wymagane do skonfigurowania logowania jednokrotnego w aplikacji.

14. Kliknij przycisk **Zapisz** Aby zapisać konfigurację.

15. Przypisywanie użytkowników do aplikacji.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji

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
   >Usługa Azure AD wybiera formacie dla atrybutu NameID (identyfikator użytkownika) na podstawie wartości wybrane lub żądany przez aplikację w SAML AuthRequest. Więcej informacji na ten temat można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** atrybuty do wysłania do aplikacji w tokenie języka SAML, gdy użytkownicy logują się.

   Aby dodać atrybut:

   1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** a następnie wybierz **wartość** z listy rozwijanej.

   2. Kliknij przycisk **Zapisz.** Zostanie wyświetlony nowy atrybut w tabeli.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobierz metadane usługi Azure AD lub certyfikat

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować federacyjne logowanie jednokrotne dla aplikacji spoza galerii

Aby skonfigurować aplikacji spoza galerii, musisz mieć usługa Azure AD premium i aplikacja obsługuje SAML 2.0. Aby uzyskać więcej informacji o wersji usługi Azure AD, odwiedź stronę [cennika usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Skonfiguruj wartości metadanych aplikacji w usłudze Azure AD (adres URL odpowiedzi adresu URL, identyfikator logowania)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Pobieranie metadanych usługi Azure AD i certyfikatów](#download-the-azure-ad-metadata-or-certificate)

-   [Skonfiguruj wartości metadanych usługi Azure AD w aplikacji (logowanie jednokrotne adresu URL, wystawcy, adres URL wylogowania i certyfikat)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Skonfiguruj wartości metadanych aplikacji w usłudze Azure AD (adres URL odpowiedzi adresu URL, identyfikator logowania)

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

10. Wybierz **opartej na SAML logowania jednokrotnego** w **tryb** listy rozwijanej.

11. Wprowadź wymagane wartości w **domena i adresy URL.** Te wartości należy uzyskać od dostawcy aplikacji.

    1. Aby skonfigurować aplikację jako logowanie Jednokrotne zainicjowane przez dostawcę tożsamości, wprowadź adres URL odpowiedzi i identyfikator.

    2.  **Opcjonalnie:** Aby skonfigurować aplikację jako logowanie Jednokrotne zainicjowane przez Dostawcę, adres URL logowania jest wymagana wartość.

12. W **atrybutów użytkownika**, wybierz Unikatowy identyfikator dla użytkowników w **identyfikator użytkownika** listy rozwijanej.

13. **Opcjonalnie:** kliknij **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** atrybuty do wysłania do aplikacji w tokenie języka SAML, gdy użytkownicy logują się.

    Aby dodać atrybut:

    1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** a następnie wybierz **wartość** z listy rozwijanej.

    2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

14. Kliknij przycisk **Konfiguruj &lt;Nazwa aplikacji&gt;**  dokumentacji dostępu dotyczące sposobu konfigurowania logowania jednokrotnego w aplikacji. Ponadto trzeba adresy URL platformy Azure AD i certyfikaty wymagane do zastosowania.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Wybierz identyfikator użytkownika i Dodaj atrybuty użytkownika do wysłania do aplikacji

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
   >Usługa Azure AD wybiera formacie dla atrybutu NameID (identyfikator użytkownika) na podstawie wartości wybrane lub żądany przez aplikację w SAML AuthRequest. Więcej informacji na ten temat można znaleźć w artykule [protokołu SAML rejestracji jednokrotnej](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) sekcji NameIDPolicy.
   >
   >

9. Aby dodać atrybuty użytkownika, kliknij **Wyświetl i Edytuj wszystkie inne atrybuty użytkownika** atrybuty do wysłania do aplikacji w tokenie języka SAML, gdy użytkownicy logują się.

   Aby dodać atrybut:

   1. Kliknij przycisk **Dodaj atrybut**. Wprowadź **nazwa** a następnie wybierz **wartość** z listy rozwijanej.

   2. Kliknij przycisk **Zapisz.** Nowy atrybut zostanie wyświetlony w tabeli.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Pobierz metadane usługi Azure AD lub certyfikat

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak skonfigurować hasło logowania jednokrotnego dla aplikacji z galerii usługi Azure AD

Aby skonfigurować aplikację z galerii usługi Azure AD, które należy:

-   [Dodawanie aplikacji z galerii usługi Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurowanie aplikacji dla hasła logowania jednokrotnego](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

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

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji dla hasła logowania jednokrotnego

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

9. [Przypisywanie użytkowników do aplikacji](#how-to-assign-a-user-to-an-application-directly).

10. Ponadto, możesz także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając **poświadczenia aktualizacji** i wprowadzić nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy monit o podanie poświadczeń, się po uruchomieniu.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak skonfigurować hasło logowania jednokrotnego dla aplikacji spoza galerii

Aby skonfigurować aplikację z galerii usługi Azure AD, które należy:

-   [Dodawanie aplikacji spoza galerii](#add-a-non-gallery-application)

-   [Konfigurowanie aplikacji dla hasła logowania jednokrotnego](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Dodawanie aplikacji spoza galerii

Aby dodać aplikację z galerii usługi Azure AD, wykonaj następujące czynności:

1.  Otwórz [witryny Azure portal](https://portal.azure.com) i zaloguj się jako **administratora globalnego** lub **współadministrator**.

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **Dodaj** znajdujący się w prawym górnym rogu na **aplikacje dla przedsiębiorstw** okienka.

6.  Kliknij przycisk **aplikacji spoza galerii.**

7.  Wprowadź nazwę aplikacji na platformie **nazwa** pola tekstowego. Wybierz **Dodaj.**

Po krótkim czasie można zobaczyć okienko konfiguracji aplikacji.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurowanie aplikacji dla hasła logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne dla aplikacji, wykonaj następujące czynności:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego** lub **Współadministratora.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5.  Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

    1.  Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6.  Wybierz aplikację, którą chcesz skonfigurować logowanie jednokrotne.

7.  Po załadowaniu aplikacji, kliknij przycisk **logowanie jednokrotne** menu nawigacji po lewej stronie aplikacji.

8.  Wybierz tryb **opartego na hasłach logowania jednokrotnego.**

9.  Wprowadź **adres URL logowania**. To jest adres URL, w którym użytkownicy wprowadzają swoją nazwę i hasło do logowania się na. Upewnij się, że pola logowania są widoczne pod adresem URL.

10. [Przypisywanie użytkowników do aplikacji](#how-to-assign-a-user-to-an-application-directly).

11. Ponadto, możesz także podać poświadczenia w imieniu użytkownika, wybierając wiersze użytkowników i klikając **poświadczenia aktualizacji** i wprowadzić nazwę użytkownika i hasło w imieniu użytkowników. W przeciwnym razie użytkownicy monit o podanie poświadczeń, się po uruchomieniu.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problemy związane z przypisaniem aplikacji do użytkowników

Użytkownik może nie być widoczny aplikację w swoim panelu dostępu, ponieważ nie są one przypisane do aplikacji. Poniżej przedstawiono kilka sposobów, aby sprawdzić:

-   [Sprawdź, jeśli użytkownik jest przypisany do aplikacji](#check-if-a-user-is-assigned-to-the-application)

-   [Jak przypisać użytkownika do aplikacji bezpośrednio](#how-to-assign-a-user-to-an-application-directly)

-   [Sprawdź, jeśli użytkownik jest przypisany do licencji związane z aplikacji](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Jak przypisać licencję do użytkownika](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Sprawdź, jeśli użytkownik jest przypisany do aplikacji

Aby sprawdzić, jeśli użytkownik jest przypisany do aplikacji, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

6. **Wyszukiwanie** dla nazwy aplikacji w danym.

7. Kliknij przycisk **użytkowników i grup**.

8. Sprawdź, czy użytkownik ma przypisaną aplikacji.

   * W przeciwnym razie wykonaj kroki opisane w artykule "Jak bezpośrednio przypisać użytkownika do aplikacji" w tym celu.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak przypisać użytkownika do aplikacji bezpośrednio

Aby przypisać co najmniej jednego użytkownika do aplikacji bezpośrednio, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego**.

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

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Sprawdź, czy użytkownik jest w ramach licencji, związane z aplikacji

Aby sprawdzić przypisanych licencji użytkownika, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5. Kliknij przycisk **wszyscy użytkownicy**.

6. **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7. Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencji użytkownika został przypisany.

   * Jeśli użytkownik jest przypisany do licencji usługi Office, umożliwia to aplikacjom pierwszej strony pakietu Office są wyświetlane na panelu dostępu użytkownika.

### <a name="how-to-assign-a-user-a-license"></a>Jak przypisać licencję użytkownika 

Aby przypisać licencję do użytkownika, wykonaj następujące czynności:

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

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problemy związane z przypisaniem aplikacji do grup

Użytkownik może być widoczny aplikację w swoim panelu dostępu, ponieważ są one częścią grupy, która ma przypisane aplikacji. Poniżej przedstawiono kilka sposobów, aby sprawdzić:

-   [Sprawdzanie członkostwa grupy użytkownika](#check-a-users-group-memberships)

-   [Jak przypisać aplikację do grupy bezpośrednio](#how-to-assign-an-application-to-a-group-directly)

-   [Sprawdź, czy użytkownik jest częścią grupy przypisane do licencji](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Jak przypisać licencję do grupy](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Sprawdzanie członkostwa grupy użytkownika

Aby sprawdzić członkostwa w grupie, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5. Kliknij przycisk **wszyscy użytkownicy**.

6. **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7. Kliknij przycisk **grup**.

8. Sprawdź, czy użytkownik jest częścią grupy przypisane do aplikacji.

   * Jeśli chcesz usunąć użytkownika z grupy, **kliknij wiersz** grupy i wybierz pozycję Usuń.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Jak przypisać aplikację do grupy bezpośrednio

Aby przypisać co najmniej jedną grupę aplikacji bezpośrednio, wykonaj następujące czynności:

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego**.

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **aplikacje dla przedsiębiorstw** menu nawigacji po lewej stronie usługi Azure Active Directory.

5. Kliknij przycisk **wszystkie aplikacje** Aby wyświetlić listę wszystkich aplikacji.

   * Jeśli nie widzisz aplikacji, chcesz, aby wyświetlić tutaj użyć **filtru** formant w górnej części **listę wszystkich aplikacji** i ustaw **Pokaż** opcję **wszystkie Aplikacje.**

6. Wybierz aplikację, którą chcesz przypisać do użytkownika z listy.

7. Po załadowaniu aplikacji, kliknij przycisk **użytkowników i grup** menu nawigacji po lewej stronie aplikacji.

8. Kliknij przycisk **Dodaj** przycisk w górnej części **użytkowników i grup** liście, aby otworzyć **Dodaj przydziału** okienka.

9. Kliknij przycisk **użytkowników i grup** selektor z **Dodaj przydziału** okienka.

10. Wpisz **Nazwa grupy pełną** grupy osób zainteresowanych przypisywania do **wyszukiwanie według nazwy lub adresu e-mail** pola wyszukiwania.

11. Umieść kursor nad **grupy** na liście, aby wyświetlić **wyboru**. Kliknij pole wyboru obok zdjęcie w profilu lub logo, aby dodać użytkownika do grupy **wybrane** listy.

12. **Opcjonalnie:** Jeśli chcesz **dodać więcej niż jednej grupy**, typ w innym **Nazwa grupy pełną** do **wyszukiwanie według nazwy lub adresu e-mail** polu wyszukiwania, a następnie kliknij pole wyboru, aby dodać tę grupę Aby **wybrane** listy.

13. Po wybraniu grup kliknij **wybierz** przycisk, aby dodać je do listy użytkowników i grup do przypisania do aplikacji.

14. **Opcjonalnie:** kliknij **wybierz rolę** selektorze **Dodaj przydziału** okienku wybierz rolę do przypisania do wybranych grupach.

15. Kliknij przycisk **przypisać** przycisk, aby przypisać aplikację do wybranych grup.

Po krótkim czasie użytkowników, dla których wybrano można uruchamiać te aplikacje w panelu dostępu.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Sprawdź, czy użytkownik jest częścią grupy przypisane do licencji

1. Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2. Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4. Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5. Kliknij przycisk **wszyscy użytkownicy**.

6. **Wyszukiwanie** dla użytkownika, jesteś zainteresowany i **kliknij wiersz** do wybrania.

7. Kliknij przycisk **grup**.

8. Kliknij wiersz określonej grupy.

9. Kliknij przycisk **licencji** aby zobaczyć, który licencji grupy został przypisany do niego.

   * Jeśli grupa jest przypisana do licencji usługi Office, może to umożliwić niektórych aplikacji pierwszy Office innych firm są wyświetlane na panelu dostępu użytkownika.

### <a name="how-to-assign-a-license-to-a-group"></a>Jak przypisać licencję do grupy

Aby przypisać licencję do grupy, wykonaj następujące czynności:

1.  Otwórz [ **witryny Azure portal** ](https://portal.azure.com/) i zaloguj się jako **administratora globalnego.**

2.  Otwórz **rozszerzenia usługi Azure Active Directory** , klikając **wszystkich usług** w górnej części menu główne menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz pozycję **usługi Azure Active Directory** elementu.

4.  Kliknij przycisk **użytkowników i grup** w menu nawigacji.

5.  Kliknij przycisk **wszystkich grup**.

6.  **Wyszukiwanie** grupy jesteś zainteresowany i **kliknij wiersz** do wybrania.

7.  Kliknij przycisk **licencji** aby zobaczyć, które obecnie licencji grupy został przypisany.

8.  Kliknij przycisk **przypisać** przycisku.

9.  Wybierz **jeden lub więcej produktów** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij **opcje przydziału** element do szczegółowego przypisania produktów. Kliknij przycisk **Ok** po zakończeniu.

11. Kliknij przycisk **przypisać** przycisk, aby przypisać licencje do tej grupy. Może to potrwać długo w zależności od rozmiaru i złożoności grupy.

>[!NOTE]
>Aby szybciej to zrobić, należy wziąć pod uwagę tymczasowo bezpośrednie przypisanie licencji do użytkownika. 
>
>

## <a name="next-steps"></a>Kolejne kroki
[Dodawanie nowych użytkowników do usługi Azure Active Directory](./../fundamentals/add-users-azure-active-directory.md)

