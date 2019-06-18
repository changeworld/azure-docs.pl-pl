---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Ziflow | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: d9745bdb1cb6de86a96946564865958433d49732
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086208"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Ziflow

W tym samouczku dowiesz się, jak zintegrować Ziflow w usłudze Azure Active Directory (Azure AD).
Integrowanie Ziflow z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Ziflow.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Ziflow (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Ziflow, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Ziflow logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Ziflow **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-ziflow-from-the-gallery"></a>Dodawanie Ziflow z galerii

Aby skonfigurować integrację Ziflow w usłudze Azure AD, należy dodać Ziflow z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Ziflow z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Ziflow**, wybierz opcję **Ziflow** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Ziflow na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Ziflow w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Ziflow musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Ziflow, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Ziflow logowania jednokrotnego](#configure-ziflow-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Ziflow](#create-ziflow-test-user)**  — aby odpowiednikiem Britta Simon w Ziflow połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Ziflow, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Ziflow** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Ziflow domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Podane wyżej wartości nie są rzeczywiste. Wartość unikatowego Identyfikatora identyfikator i adres URL logowania zostaną zaktualizowane o wartości rzeczywiste, co zostało wyjaśnione w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie Ziflow** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-ziflow-single-sign-on"></a>Konfigurowanie Ziflow logowanie jednokrotne

1. W oknie przeglądarki internetowej innej Zaloguj się do Ziflow jako Administrator zabezpieczeń.

2. W prawym górnym rogu kliknij awatara, a następnie kliknij przycisk **Zarządzanie kontem**.

    ![Zarządzanie Ziflow konfiguracji](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. W lewym górnym rogu, kliknij przycisk **logowania jednokrotnego**.

    ![Ziflow konfiguracji logowania](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Na stronie **Single Sign-On**(Logowanie jednokrotne) wykonaj następujące czynności:

    ![Jednym konfiguracji Ziflow](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Wybierz **typu** jako **SAML2.0**.

    b. W **adres URL logowania** pola tekstowego, Wklej wartość **adres URL logowania**, które zostały skopiowane z witryny Azure portal.

    c. Przekaż certyfikat zakodowany base-64, pobrany z witryny Azure portal do **X509 certyfikatu podpisywania**.

    d. W **adresu URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    e. Z **ustawienia konfiguracji dla identyfikatora dostawcy** sekcji, skopiuj wartość unikatowego Identyfikatora wyróżnione i dołącz go z identyfikatorem i logowanie jednokrotne adresu URL w **podstawową konfigurację protokołu SAML** na platformie Azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola brittasimon@yourcompanydomain.extension. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Ziflow.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Ziflow**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Ziflow**.

    ![Link Ziflow na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-ziflow-test-user"></a>Tworzenie użytkownika testowego Ziflow

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Ziflow, musi być obsługiwana w Ziflow. W Ziflow Inicjowanie obsługi administracyjnej jest zadanie ręczne.

Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Ziflow jako Administrator zabezpieczeń.

2. Przejdź do **osób** u góry.

    ![Konfiguracja Ziflow osoby](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Kliknij przycisk **Dodaj** a następnie kliknij przycisk **Dodaj użytkownika**.

    ![Konfiguracja Ziflow dodawania użytkownika](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Na **Dodawanie użytkownika** okna podręcznego, wykonaj następujące czynności:

    ![Konfiguracja Ziflow dodawania użytkownika](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. W **E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak brittasimon@contoso.com.

    b. W **imię** tekstu Wprowadź imię użytkownika, takich jak Britta.

    c. W **nazwisko** tekstu wprowadź nazwisko użytkownika, takich jak Simon.

    d. Wybierz rolę Ziflow.

    e. Kliknij przycisk **Dodaj 1 użytkownika**.

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Ziflow w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Ziflow, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

