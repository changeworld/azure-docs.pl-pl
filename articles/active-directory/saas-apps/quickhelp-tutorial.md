---
title: 'Samouczek: Integracja usługi Azure Active Directory z quickhelp | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a quickhelp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 655c9ad3-2076-4e2c-8e47-9ed3bf04be56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: f4b41098a3b374506e655bf90f972b57195e0958
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093374"
---
# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>Samouczek: Integracja usługi Azure Active Directory z pomocą quickhelp

W tym samouczku dowiesz się, jak zintegrować QuickHelp z usługą Azure Active Directory (Azure AD).
Integracja quickhelp z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do QuickHelp.
* Można włączyć użytkowników, aby automatycznie zalogować się do QuickHelp (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pomocą quickhelp, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego QuickHelp

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* QuickHelp obsługuje sso inicjowane przez **SP**

* QuickHelp obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time

## <a name="adding-quickhelp-from-the-gallery"></a>Dodawanie QuickHelp z galerii

Aby skonfigurować integrację quickhelp z usługą Azure AD, należy dodać QuickHelp z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać quickhelp z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **QuickHelp**, wybierz **pozycję QuickHelp** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![QuickHelp na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z QuickHelp na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie QuickHelp.

Aby skonfigurować i przetestować logowanie jednookrotne usługi Azure AD za pomocą programu QuickHelp, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne QuickHelp](#configure-quickhelp-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego QuickHelp](#create-quickhelp-test-user)** — aby mieć odpowiednik Britta Simon w QuickHelp, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą programu QuickHelp, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **QuickHelp** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o loguchu quickhelp i adresach URL](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://quickhelp.com/<ROUTEURL>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://auth.quickhelp.com`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać wartość, skontaktuj się z administratorem quickhelp w organizacji lub menedżerem sukcesu klienta burzy mózgów. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie quickhelp** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-quickhelp-single-sign-on"></a>Konfigurowanie logowania jednokrotnego quickhelp

1. Zaloguj się do witryny firmy QuickHelp jako administrator.

2. W menu u góry kliknij pozycję **Admin** (Administrator).
   
    ![Konfigurowanie logowania jednokrotnego][21]

3. W menu **Administrator programu QuickHelp** kliknij polecenie **Ustawienia**.
   
    ![Konfigurowanie logowania jednokrotnego][22]

4. Kliknij **pozycję Ustawienia uwierzytelniania**.

5. Na stronie **Ustawienia uwierzytelniania** wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego][23]
   
    a. Jako **typ SSO**wybierz **WSFederation**.
   
    b. Aby przekazać pobrany plik metadanych platformy Azure, kliknij przycisk **Przeglądaj,** przejdź do pliku, zakończ, a następnie kliknij przycisk **Przekaż metadane**.
   
    d. W polu tekstowym **Email** (Adres e-mail) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
   
    d. W **First Name** polach tekstowych `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`Imię , .
   
    e. W **Last Name** polach tekstowych `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`Nazwisko .
   
    f. Na **pasku akcji**kliknij pozycję **Zapisz**.

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

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, przyznając dostęp do QuickHelp.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **QuickHelp**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **QuickHelp**.

    ![Łącze QuickHelp na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-quickhelp-test-user"></a>Utwórz użytkownika testowego QuickHelp

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w QuickHelp. QuickHelp obsługuje just-in-time użytkownika inicjowania obsługi administracyjnej, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w QuickHelp, nowy jest tworzony po uwierzytelnieniu.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka QuickHelp w Panelu dostępu należy automatycznie zalogować się do quickhelp, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[21]: ./media/quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/quickhelp-tutorial/tutorial_quickhelp_07.png