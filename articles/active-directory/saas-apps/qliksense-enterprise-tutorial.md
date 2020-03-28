---
title: 'Samouczek: Integracja usługi Azure Active Directory z qlik sense enterprise | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec9349d8ed330a00a64922a44f99910f9eeeb0df
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79136452"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Samouczek: Integracja programu Qlik Sense Enterprise z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Qlik Sense Enterprise z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Qlik Sense Enterprise z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do Qlik Sense Enterprise.
* Umożliwia automatyczne logowanie użytkowników do qlik Sense Enterprise za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać miesięczny bezpłatny okres próbny [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja qlik Sense Enterprise z obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. 
* Qlik Sense Enterprise obsługuje sso inicjowane przez **sp.**
* Qlik Sense Enterprise obsługuje **aprowizacji just-in-time**

* Po skonfigurowaniu qlik Sense Enterprise można wymusić kontrolę sesji, które chronią eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Dodawanie usługi Qlik Sense Enterprise z galerii

Aby skonfigurować integrację usługi Qlik Sense Enterprise z usługą Azure AD, należy dodać usługę Qlik Sense Enterprise z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Qlik Sense Enterprise** w polu wyszukiwania.
1. Wybierz **pozycję Qlik Sense Enterprise** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą aplikacji Qlik Sense Enterprise przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby funkcja SSO działała, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji Qlik Sense Enterprise.

Aby skonfigurować i przetestować jednostkę SSO usługi Azure AD za pomocą aplikacji Qlik Sense Enterprise, wykonaj następujące elementy konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    * **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne Qlik Sense Enterprise](#configure-qlik-sense-enterprise-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Tworzenie użytkownika testowego usługi Qlik Sense Enterprise](#create-qlik-sense-enterprise-test-user)** — aby udostępnić w usłudze Qlik Sense Enterprise odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Qlik Sense Enterprise** znajdź sekcję **Zarządzaj** i wybierz **pozycję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu **tekstowym Logowania adres URL** wpisz adres URL przy użyciu następującego wzorca:`https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu jednego z następujących wzorców:

    | |
    |--|
    | `https://<Fully Qualified Domain Name>.qlikpoc.com`|
    | `https://<Fully Qualified Domain Name>.qliksense.com`|
    | |

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami adresu URL logowania jednokrotnego, identyfikatora i adresu URL odpowiedzi (są one objaśnione w dalszej części tego samouczka) lub skontaktuj się z [zespołem pomocy technicznej klienta usługi Qlik Sense Enterprise](https://www.qlik.com/us/services/support), aby uzyskać te wartości. Domyślny port adresów URL to 443, ale można go dostosować zgodnie z potrzebami organizacji.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź **kod XML metadanych federacji** z podanych opcji zgodnie z wymaganiami i zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie Britta Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz britta simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Qlik Sense Enterprise.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **pozycję Qlik Sense Enterprise**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-qlik-sense-enterprise-sso"></a>Konfigurowanie aplikacji SYC Qlik Sense Enterprise

1. Przygotuj plik XML metadanych federacji, który można przekazać na serwer Qlik Sense.

    > [!NOTE]
    > Przed przekazaniem metadanych dostawcy tożsamości na serwer Qlik Sense należy zmodyfikować plik, aby usunąć informacje w celu zapewnienia odpowiedniego współdziałania usługi Azure AD i serwera Qlik Sense.

    ![QlikSense][qs24]

    a. Otwórz plik FederationMetaData.xml pobrany z witryny Azure Portal w edytorze tekstów.

    b. Wyszukaj wartość **RoleDescriptor**.  Istnieją cztery wpisy (dwie pary otwierających i zamykających tagów elementu).

    d. Usuń z pliku tagi RoleDescriptor i wszystkie informacje między nimi.

    d. Zapisz plik tak, aby był dostępny do użycia w dalszej części tego dokumentu.

2. Przejdź do konsoli Qlik Management Console (QMC) usługi Qlik Sense jako użytkownik, który może tworzyć konfiguracje wirtualnego serwera proxy.

3. W konsoli QMC kliknij pozycję menu **Virtual Proxies** (Wirtualne serwery proxy).

    ![QlikSense][qs6]

4. W dolnej części ekranu kliknij przycisk **Create new** (Utwórz nowy).

    ![QlikSense][qs7]

5. Zostanie wyświetlony ekran edycji wirtualnych serwerów proxy.  Po prawej stronie ekranu jest dostępne menu umożliwiające włączenie widoczności opcji konfiguracji.

    ![QlikSense][qs9]

6. Przy zaznaczonej opcji menu Identification (Identyfikacja) podaj informacje identyfikujące konfigurację wirtualnego serwera proxy platformy Azure.

    ![QlikSense][qs8]  

    a. Pole **Description** (Opis) to przyjazna nazwa dla konfiguracji wirtualnego serwera proxy.  Podaj wartość zawierającą opis.

    b. Pole **Prefix** (Prefiks) określa punkt końcowy wirtualnego serwera proxy umożliwiający funkcji logowania jednokrotnego usługi Azure AD łączenie się z usługą Qlik Sense.  Podaj unikatowy prefiks dla tego wirtualnego serwera proxy.

    d. Pole **Session inactivity timeout (minutes)** (Limit czasu bezczynności sesji w minutach) to limit czasu dla połączeń przechodzących przez wirtualny serwer proxy.

    d. Pole **Session cookie header name** (Nazwa nagłówka pliku cookie sesji) przechowuje identyfikator sesji usługi Qlik Sense, który użytkownik otrzymuje po pomyślnym uwierzytelnieniu.  Ta nazwa musi być unikatowa.

7. Kliknij opcję menu Authentication (Uwierzytelnianie), aby stała się widoczna.  Zostanie wyświetlony ekran Authentication (Uwierzytelnianie).

    ![QlikSense][qs10]

    a. Lista rozwijana **Anonymous access mode** (Tryb dostępu anonimowego) określa, czy użytkownicy anonimowi mają dostęp do usługi Qlik Sense za pośrednictwem wirtualnego serwera proxy.  Opcja domyślna to No anonymous user (Bez użytkowników anonimowych).

    b. Lista rozwijana **Authentication method** (Metoda uwierzytelniania) określa schemat uwierzytelniania używany przez wirtualny serwer proxy.  Wybierz pozycję SAML z listy rozwijanej.  Zostaną wtedy wyświetlone kolejne opcje.

    d. W polu **SAML host URI** (Identyfikator URI hosta SAML) podaj nazwę hosta używaną przez użytkowników do uzyskiwania dostępu do usługi Qlik Sense za pomocą tego wirtualnego serwera proxy protokołu SAML.  Nazwa hosta to identyfikator URI serwera Qlik Sense.

    d. W polu **SAML entity ID** (Identyfikator jednostki SAML) podaj tę samą wartość co w polu SAML host URI (Identyfikator URI hosta SAML).

    e. Pole **SAML IdP metadata** (Metadane dostawcy tożsamości SAML) określa plik edytowany wcześniej w sekcji **Edytowanie metadanych federacji z konfiguracji usługi Azure AD**.  **Przed przekazaniem metadanych dostawcy tożsamości plik należy zmodyfikować**, aby usunąć informacje w celu zapewnienia odpowiedniego współdziałania usługi Azure AD i serwera Qlik Sense.  **Zapoznaj się z instrukcjami powyżej, jeśli plik nie został jeszcze zmodyfikowany.**  Jeśli plik został już zmodyfikowany, kliknij przycisk Browse (Przeglądaj) i wybierz edytowany plik metadanych, aby przekazać go do konfiguracji wirtualnego serwera proxy.

    f. Podaj nazwę atrybutu lub odwołanie do schematu dla atrybutu SAML reprezentującego wartość **UserID** wysyłaną przez usługę Azure AD do serwera Qlik Sense.  Informacje o odwołaniu do schematu są dostępne na ekranach aplikacji platformy Azure wyświetlanych po konfiguracji.  Aby użyć nazwy atrybutu, podaj ciąg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    g. Podaj wartość dla **katalogu użytkownika**, który będzie dołączany do użytkowników po uwierzytelnieniu względem serwera Qlik Sense za pomocą usługi Azure AD.  Zapisane na stałe wartości muszą być ujęte w **nawiasy kwadratowe — []**.  Aby użyć atrybutu wysyłanego w asercji SAML usługi Azure AD, wpisz nazwę atrybutu w tym polu tekstowym **bez** nawiasów kwadratowych.

    h. Pole **SAML signing algorithm** (Algorytm podpisywania SAML) umożliwia ustawienie podpisywania certyfikatu dostawcy usług (w tym przypadku serwera Qlik Sense) dla konfiguracji wirtualnego serwera proxy.  Jeśli serwer Qlik Sense używa certyfikatu zaufanego wygenerowanego za pomocą dostawcy Microsoft Enhanced RSA and AES Cryptographic Provider, zmień algorytm podpisywania SAML na **SHA-256**.

    i. Sekcja mapowania atrybutów SAML umożliwia wysyłanie dodatkowych atrybutów, takich jak grupy, do usługi Qlik Sense na potrzeby użycia w regułach zabezpieczeń.

8. Kliknij opcję menu **LOAD BALANCING** (RÓWNOWAŻENIE OBCIĄŻENIA), aby stała się widoczna.  Zostanie wyświetlony ekran Load Balancing (Równoważenie obciążenia).

    ![QlikSense][qs11]

9. Kliknij przycisk **Add new server node** (Dodaj nowy węzeł serwera), wybierz węzeł lub węzły aparatu, do których usługa Qlik Sense będzie wysyłać sesje na potrzeby równoważenia obciążenia, a następnie kliknij przycisk **Add** (Dodaj).

    ![QlikSense][qs12]

10. Kliknij opcję menu Advanced (Zaawansowane), aby stała się widoczna. Zostanie wyświetlony ekran Advanced (Zaawansowane).

    ![QlikSense][qs13]

    Lista zezwalania na hosta identyfikuje nazwy hostów, które są akceptowane podczas łączenia się z serwerem Qlik Sense.  **Podaj nazwę hosta, którą użytkownicy będą określać podczas nawiązywania połączenia z serwerem Qlik Sense.** Nazwa hosta jest taka sama jak identyfikator URI hosta SAML, jednak bez prefiksu https://.

11. Następnie kliknij przycisk **Apply** (Zastosuj).

    ![QlikSense][qs14]

12. Kliknij przycisk OK, aby zaakceptować komunikat ostrzegawczy informujący o tym, że serwery proxy połączone z wirtualnym serwerem proxy zostaną uruchomione ponownie.

    ![QlikSense][qs15]

13. Po prawej stronie ekranu zostanie wyświetlone menu Associated items (Elementy skojarzone).  Kliknij opcję menu **Proxies** (Serwery proxy).

    ![QlikSense][qs16]

14. Zostanie wyświetlony ekran serwera proxy.  Kliknij przycisk **Link** (Połącz) w dolnej części ekranu, aby połączyć serwer proxy z wirtualnym serwerem proxy.

    ![QlikSense][qs17]

15. Wybierz węzeł serwera proxy, który będzie obsługiwać połączenie tego wirtualnego serwera proxy, a następnie kliknij przycisk **Link** (Połącz).  Po połączeniu serwer proxy zostanie wyświetlony w obszarze skojarzonych serwerów proxy.

    ![QlikSense][qs18]
  
    ![QlikSense][qs19]

16. Po około 5–10 sekundach zostanie wyświetlony komunikat odświeżenia konsoli QMC.  Kliknij przycisk **Refresh QMC** (Odśwież konsolę QMC).

    ![QlikSense][qs20]

17. Po odświeżeniu konsoli QMC kliknij pozycję menu **Virtual proxies** (Wirtualne serwery proxy). Pozycja nowego wirtualnego serwera proxy SAML zostanie wyświetlona w tabeli na ekranie.  Kliknij jednokrotnie pozycję wirtualnego serwera proxy.

    ![QlikSense][qs51]

18. W dolnej części ekranu zostanie aktywowany przycisk Download SP metadata (Pobierz metadane dostawcy usług).  Kliknij przycisk **Download SP metadata** (Pobierz metadane dostawcy usług), aby zapisać metadane do pliku.

    ![QlikSense][qs52]

19. Otwórz plik metadanych dostawcy usług.  Zwróć uwagę na pozycje **entityID** i **AssertionConsumerService**.  Te wartości są równoważne polom **Identyfikator**, **Adres URL logowania** i **Adres URL odpowiedzi** w konfiguracji aplikacji usługi Azure AD. Wklej te wartości w sekcji **Domena i adresy URL usługi Qlik Sense Enterprise** w konfiguracji aplikacji usługi Azure AD, jeśli nie są zgodne, a następnie zastąp je w kreatorze konfiguracji aplikacji usługi Azure AD.

    ![QlikSense][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Tworzenie użytkownika testowego usługi Qlik Sense Enterprise

Qlik Sense Enterprise obsługuje **aprowizacji just-in-time**, Użytkownicy automatycznie dodawane do repozytorium "UŻYTKOWNICY" Qlik Sense Enterprise, ponieważ korzystają z funkcji SSO. Ponadto klienci mogą korzystać z QMC i utworzyć UDC (User Directory Connector) do wstępnego wypełniania użytkowników w Qlik Sense Enterprise z ich LDAP z wyboru, takich jak Active Directory i innych.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Qlik Sense Enterprise w Panelu dostępu należy automatycznie zalogować się do Qlik Sense Enterprise, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png
