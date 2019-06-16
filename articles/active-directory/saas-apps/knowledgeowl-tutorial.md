---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą KnowledgeOwl | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: ab80b6efef71c71feea1359112d09bae90a7ab84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098886"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą KnowledgeOwl

W tym samouczku dowiesz się, jak zintegrować KnowledgeOwl w usłudze Azure Active Directory (Azure AD).
Integrowanie KnowledgeOwl z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do KnowledgeOwl.
* Aby umożliwić użytkownikom można automatycznie zalogowany do KnowledgeOwl (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą KnowledgeOwl, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* KnowledgeOwl logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje KnowledgeOwl **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez
* Obsługuje KnowledgeOwl **Just In Time** aprowizacji użytkowników

## <a name="adding-knowledgeowl-from-the-gallery"></a>Dodawanie KnowledgeOwl z galerii

Aby skonfigurować integrację KnowledgeOwl w usłudze Azure AD, należy dodać KnowledgeOwl z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać KnowledgeOwl z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **KnowledgeOwl**, wybierz opcję **KnowledgeOwl** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![KnowledgeOwl na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą KnowledgeOwl w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w KnowledgeOwl musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą KnowledgeOwl, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie KnowledgeOwl logowania jednokrotnego](#configure-knowledgeowl-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego KnowledgeOwl](#create-knowledgeowl-test-user)**  — aby odpowiednikiem Britta Simon w KnowledgeOwl połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z KnowledgeOwl, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **KnowledgeOwl** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![KnowledgeOwl domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    ||
    |-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    ||
    |-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![KnowledgeOwl domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca:

    ||
    |-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy zaktualizować te wartości z rzeczywistego identyfikatora, adres URL odpowiedzi i URL logowania jednokrotnego, co zostało wyjaśnione w dalszej części tego samouczka.

6. Aplikacja KnowledgeOwl oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij przycisk **Edytuj** ikonę, aby otworzyć **atrybutów użytkownika** okna dialogowego.

    ![image](common/edit-attribute.png)

7. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności: 

    | Name (Nazwa) | Atrybut źródłowy | Przestrzeń nazw |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Na liście **Przestrzeń nazw** podaj wartość przestrzeni nazw pokazaną dla tego wiersza.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (nieprzetworzony)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificateraw.png)

9. Na **Konfigurowanie KnowledgeOwl** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-knowledgeowl-single-sign-on"></a>Konfigurowanie KnowledgeOwl logowanie jednokrotne

1. W oknie przeglądarki innej witryny sieci web należy zalogować się do witryny firmy KnowledgeOwl jako administrator.

1. Kliknij pozycję **ustawienia** , a następnie wybierz **zabezpieczeń**.

    ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

1. Przewiń do **integracja logowania jednokrotnego SAML** i wykonaj następujące czynności:

    ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

    a. Wybierz **włączenia funkcji logowania jednokrotnego SAML**.

    b. Kopiuj **identyfikator jednostki SP** wartość i wklej go w **identyfikator jednostki** w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    c. Kopiuj **adres URL logowania SP** wartość i wklej go w **adres URL logowania i adres URL odpowiedzi** pola tekstowe w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    d. W **entityID tożsamości** pola tekstowego, Wklej **usługi Azure AD identyfikator** wartości, które zostały skopiowane z witryny Azure portal.

    e. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal.

    f. W **adres URL wylogowania dostawcy tożsamości** pola tekstowego, Wklej **adres URL wylogowania** wartości, które zostały skopiowane z witryny Azure portal

    g. Przekazywanie formularza pobranego certyfikatu witryny Azure portal, klikając **Przekaż certyfikat tożsamości**.

    h. Kliknij pozycję **atrybutów SAML mapy** do mapowania atrybutów, a następnie wykonaj następujące czynności:

    ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` do **identyfikator logowania jednokrotnego** textbox
    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` do **nazwy użytkownika/Poczta E-mail** pola tekstowego.
    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` do **imię** pola tekstowego.
    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` do **nazwisko** pola tekstowego.
    * Kliknij polecenie **Zapisz**.

    i. Kliknij przycisk **Zapisz** w dolnej części strony.

    ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do KnowledgeOwl.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **KnowledgeOwl**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **KnowledgeOwl**.

    ![Link KnowledgeOwl na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-knowledgeowl-test-user"></a>Tworzenie użytkownika testowego KnowledgeOwl

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w KnowledgeOwl. KnowledgeOwl obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w KnowledgeOwl, nowy katalog jest tworzony po uwierzytelnieniu.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka KnowledgeOwl w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze KnowledgeOwl, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)