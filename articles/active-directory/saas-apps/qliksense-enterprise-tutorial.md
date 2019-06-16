---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Qlik Sense Enterprise | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Qlik Sense Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8c27e340-2b25-47b6-bf1f-438be4c14f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e72ec4f9c512f6525f790d555794c1a120ac07c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093420"
---
# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Qlik Sense Enterprise

Z tego samouczka dowiesz się, jak zintegrować usługę Qlik Sense Enterprise z usługą Azure Active Directory (Azure AD).
Zintegrowanie usługi Qlik Sense Enterprise z usługą Azure AD daje następujące korzyści:

* Możliwość kontrolowania dostępu do usługi Qlik Sense Enterprise za pomocą usługi Azure AD.
* Możliwość skonfigurowania użytkowników pod kątem automatycznego logowania do usługi Qlik Sense Enterprise przy użyciu kont usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Qlik Sense Enterprise, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi Qlik Sense Enterprise z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Qlik Sense Enterprise obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Dodawanie usługi Qlik Sense Enterprise z galerii

Aby skonfigurować integrację usługi Qlik Sense Enterprise z usługą Azure AD, należy dodać usługę Qlik Sense Enterprise z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać usługę Qlik Sense Enterprise z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz ciąg **Qlik Sense Enterprise**, wybierz pozycję **Qlik Sense Enterprise** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Usługa Qlik Sense Enterprise na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w usłudze Qlik Sense Enterprise, korzystając z danych użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usługi Qlik Sense Enterprise.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w usłudze Qlik Sense Enterprise, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego usługi Qlik Sense Enterprise](#configure-qlik-sense-enterprise-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego usługi Qlik Sense Enterprise](#create-qlik-sense-enterprise-test-user)** — aby udostępnić w usłudze Qlik Sense Enterprise odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w usłudze Qlik Sense Enterprise, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji usługi **Qlik Sense Enterprise** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL pojedynczego logowania usługi Qlik Sense Enterprise](common/sp-identifier-reply.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<Qlik Sense Fully Qualifed Hostname>:4443/azure/hub`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qlikpoc.com`|
    | `https://<Qlik Sense Fully Qualifed Hostname>.qliksense.com`|
    | |

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    `https://<Qlik Sense Fully Qualifed Hostname>:4443/samlauthn/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami adresu URL logowania jednokrotnego, identyfikatora i adresu URL odpowiedzi (są one objaśnione w dalszej części tego samouczka) lub skontaktuj się z [zespołem pomocy technicznej klienta usługi Qlik Sense Enterprise](https://www.qlik.com/us/services/support), aby uzyskać te wartości.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-qlik-sense-enterprise-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Qlik Sense Enterprise

1. Przygotuj plik XML metadanych federacji, który można przekazać na serwer Qlik Sense.

    > [!NOTE]
    > Przed przekazaniem metadanych dostawcy tożsamości na serwer Qlik Sense należy zmodyfikować plik, aby usunąć informacje w celu zapewnienia odpowiedniego współdziałania usługi Azure AD i serwera Qlik Sense.

    ![QlikSense][qs24]

    a. Otwórz plik FederationMetaData.xml pobrany z witryny Azure Portal w edytorze tekstów.

    b. Wyszukaj wartość **RoleDescriptor**.  Istnieją cztery wpisy (dwie pary otwierających i zamykających tagów elementu).

    c. Usuń z pliku tagi RoleDescriptor i wszystkie informacje między nimi.

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

    c. Pole **Session inactivity timeout (minutes)** (Limit czasu bezczynności sesji w minutach) to limit czasu dla połączeń przechodzących przez wirtualny serwer proxy.

    d. Pole **Session cookie header name** (Nazwa nagłówka pliku cookie sesji) przechowuje identyfikator sesji usługi Qlik Sense, który użytkownik otrzymuje po pomyślnym uwierzytelnieniu.  Ta nazwa musi być unikatowa.

7. Kliknij opcję menu Authentication (Uwierzytelnianie), aby stała się widoczna.  Zostanie wyświetlony ekran Authentication (Uwierzytelnianie).

    ![QlikSense][qs10]

    a. Lista rozwijana **Anonymous access mode** (Tryb dostępu anonimowego) określa, czy użytkownicy anonimowi mają dostęp do usługi Qlik Sense za pośrednictwem wirtualnego serwera proxy.  Opcja domyślna to No anonymous user (Bez użytkowników anonimowych).

    b. Lista rozwijana **Authentication method** (Metoda uwierzytelniania) określa schemat uwierzytelniania używany przez wirtualny serwer proxy.  Wybierz pozycję SAML z listy rozwijanej.  Zostaną wtedy wyświetlone kolejne opcje.

    c. W polu **SAML host URI** (Identyfikator URI hosta SAML) podaj nazwę hosta używaną przez użytkowników do uzyskiwania dostępu do usługi Qlik Sense za pomocą tego wirtualnego serwera proxy protokołu SAML.  Nazwa hosta to identyfikator URI serwera Qlik Sense.

    d. W polu **SAML entity ID** (Identyfikator jednostki SAML) podaj tę samą wartość co w polu SAML host URI (Identyfikator URI hosta SAML).

    e. Pole **SAML IdP metadata** (Metadane dostawcy tożsamości SAML) określa plik edytowany wcześniej w sekcji **Edytowanie metadanych federacji z konfiguracji usługi Azure AD**.  **Przed przekazaniem metadanych dostawcy tożsamości plik należy zmodyfikować**, aby usunąć informacje w celu zapewnienia odpowiedniego współdziałania usługi Azure AD i serwera Qlik Sense.  **Zapoznaj się z instrukcjami powyżej, jeśli plik nie został jeszcze zmodyfikowany.**  Jeśli plik został już zmodyfikowany, kliknij przycisk Browse (Przeglądaj) i wybierz edytowany plik metadanych, aby przekazać go do konfiguracji wirtualnego serwera proxy.

    f. Podaj nazwę atrybutu lub odwołanie do schematu dla atrybutu SAML reprezentującego wartość **UserID** wysyłaną przez usługę Azure AD do serwera Qlik Sense.  Informacje o odwołaniu do schematu są dostępne na ekranach aplikacji platformy Azure wyświetlanych po konfiguracji.  Aby użyć nazwy atrybutu, podaj ciąg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    g. Podaj wartość dla **katalogu użytkownika**, który będzie dołączany do użytkowników po uwierzytelnieniu względem serwera Qlik Sense za pomocą usługi Azure AD.  Zapisane na stałe wartości muszą być ujęte w **nawiasy kwadratowe — []** .  Aby użyć atrybutu wysyłanego w asercji SAML usługi Azure AD, wpisz nazwę atrybutu w tym polu tekstowym **bez** nawiasów kwadratowych.

    h. Pole **SAML signing algorithm** (Algorytm podpisywania SAML) umożliwia ustawienie podpisywania certyfikatu dostawcy usług (w tym przypadku serwera Qlik Sense) dla konfiguracji wirtualnego serwera proxy.  Jeśli serwer Qlik Sense używa certyfikatu zaufanego wygenerowanego za pomocą dostawcy Microsoft Enhanced RSA and AES Cryptographic Provider, zmień algorytm podpisywania SAML na **SHA-256**.

    i. Sekcja mapowania atrybutów SAML umożliwia wysyłanie dodatkowych atrybutów, takich jak grupy, do usługi Qlik Sense na potrzeby użycia w regułach zabezpieczeń.

8. Kliknij opcję menu **LOAD BALANCING** (RÓWNOWAŻENIE OBCIĄŻENIA), aby stała się widoczna.  Zostanie wyświetlony ekran Load Balancing (Równoważenie obciążenia).

    ![QlikSense][qs11]

9. Kliknij przycisk **Add new server node** (Dodaj nowy węzeł serwera), wybierz węzeł lub węzły aparatu, do których usługa Qlik Sense będzie wysyłać sesje na potrzeby równoważenia obciążenia, a następnie kliknij przycisk **Add** (Dodaj).

    ![QlikSense][qs12]

10. Kliknij opcję menu Advanced (Zaawansowane), aby stała się widoczna. Zostanie wyświetlony ekran Advanced (Zaawansowane).

    ![QlikSense][qs13]

    Pole Host white list (Lista dozwolonych hostów) określa nazwy hostów akceptowane podczas nawiązywania połączenia z serwerem Qlik Sense.  **Podaj nazwę hosta, którą użytkownicy będą określać podczas nawiązywania połączenia z serwerem Qlik Sense.** Nazwa hosta jest taka sama jak identyfikator URI hosta SAML, jednak bez prefiksu https://.

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

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji udostępnisz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi Qlik Sense Enterprise.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw** i **Wszystkie aplikacje**, a następnie **Qlik Sense Enterprise**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz ciąg **Qlik Sense Enterprise** i wybierz odpowiednią pozycję.

    ![Link usługi Qlik Sense Enterprise na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-qlik-sense-enterprise-test-user"></a>Tworzenie użytkownika testowego usługi Qlik Sense Enterprise

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze Qlik Sense Enterprise. Skontaktuj się z  [zespołem pomocy technicznej usługi Qlik Sense Enterprise](https://www.qlik.com/us/services/support), aby dodać użytkowników na platformie Qlik Sense Enterprise. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Qlik Sense Enterprise na panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi Qlik Sense Enterprise, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

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

