---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Ziflow | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Ziflow.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086208"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Ziflow

W tym samouczku dowiesz się, jak zintegrować ziflow z usługą Azure Active Directory (Azure AD).
Integracja ziflow z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do Ziflow.
* Można włączyć użytkowników, aby automatycznie zalogować się do Ziflow (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Ziflow, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Ziflow **obsługuje** sp zainicjowane SSO

## <a name="adding-ziflow-from-the-gallery"></a>Dodawanie Ziflow z galerii

Aby skonfigurować integrację Ziflow z usługą Azure AD, należy dodać Ziflow z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Ziflow z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **polecenie Ziflow**, wybierz **pozycję Ziflow** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Ziflow na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Ziflow na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w ziflow.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi Ziflow, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne Ziflow](#configure-ziflow-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Ziflow](#create-ziflow-test-user)** — aby mieć odpowiednik Britta Simon w Ziflow, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą usługi Ziflow, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Ziflow** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie Ziflow i adresach URL z logami jednokrotnymi](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Podane wyżej wartości nie są rzeczywiste. Unikatowa wartość identyfikatora zostanie zaktualizowana w identyfikatorze i zarejestruj adres URL z rzeczywistą wartością, co zostanie wyjaśnione w dalszej części samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie Ziflow** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-ziflow-single-sign-on"></a>Konfigurowanie logowania jednokrotnego ziflow

1. W innym oknie przeglądarki internetowej zaloguj się do Ziflow jako administrator zabezpieczeń.

2. Kliknij awatar w prawym górnym rogu, a następnie kliknij pozycję **Zarządzaj kontem**.

    ![Zarządzanie konfiguracją Ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. W lewym górnym rogu kliknij pozycję **Logowanie jednokrotne**.

    ![Znak konfiguracji Ziflow](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Na stronie **Single Sign-On**(Logowanie jednokrotne) wykonaj następujące czynności:

    ![Konfiguracja Ziflow Pojedynczy](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Wybierz **opcję Wpisz** jako **SAML2.0**.

    b. W polu tekstowym **Adresu URL logowania wklej** wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    d. Przekaż certyfikat zakodowany w bazie-64 pobrany z witryny Azure portal do **certyfikatu podpisywania X509**.

    d. W polu tekstowym **Wyloguj adres URL** wklej wartość **adresu URL wylogowania,** który został skopiowany z witryny Azure portal.

    e. W sekcji **Ustawienia konfiguracji dostawcy identyfikatora** skopiuj wyróżnioną unikatową wartość identyfikatora i dołącz ją do identyfikatora i zaloguj się pod adresem URL w **podstawowej konfiguracji SAML** w witrynie Azure portal.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** brittasimon@yourcompanydomain.extensionużytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Ziflow.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **Ziflow**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Ziflow**.

    ![Łącze Ziflow na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-ziflow-test-user"></a>Tworzenie użytkownika testowego Ziflow

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Ziflow, muszą być aprowizować do Ziflow. W Ziflow inicjowania obsługi administracyjnej jest zadanie ręczne.

Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Ziflow jako administrator zabezpieczeń.

2. Przejdź do **pozycji Kontakty** na górze.

    ![Ziflow Konfiguracji ludzi](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Kliknij **pozycję Dodaj,** a następnie kliknij pozycję **Dodaj użytkownika**.

    ![Konfiguracja Ziflow dodawanie użytkownika](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. W **wyskakującym okienku Dodaj użytkownika** wykonaj następujące czynności:

    ![Konfiguracja Ziflow dodawanie użytkownika](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. W polu tekstowym **Email** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład brittasimon@contoso.com.

    b. W polu tekstowym **Imię** wprowadź imię użytkownika, takiego jak Britta.

    d. W polu **tekstowym Nazwisko** wprowadź nazwisko użytkownika takiego jak Simon.

    d. Wybierz rolę Ziflow.

    e. Kliknij **pozycję Dodaj 1 użytkownika**.

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Ziflow w Panelu dostępu należy automatycznie zalogować się do ziflow, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

