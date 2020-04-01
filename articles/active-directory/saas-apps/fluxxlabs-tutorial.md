---
title: 'Samouczek: Integracja usługi Azure Active Directory z fluxx labs | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a fluxx labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102389"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Samouczek: Integracja usługi Azure Active Directory z fluxx labs

W tym samouczku dowiesz się, jak zintegrować fluxx labs z usługą Azure Active Directory (Azure AD).
Integracja fluxx labs z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do Fluxx Labs.
* Można włączyć użytkowników do automatycznego logowania się do Fluxx Labs (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z fluxx labs, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego Fluxx Labs

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Fluxx Labs obsługuje zainicjowane przez **IDP** SSO

## <a name="adding-fluxx-labs-from-the-gallery"></a>Dodawanie Fluxx Labs z galerii

Aby skonfigurować integrację fluxx labs z usługą Azure AD, należy dodać Fluxx Labs z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Fluxx Labs z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Fluxx Labs**, wybierz **Fluxx Labs** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Fluxx Labs na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Fluxx Labs na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w fluxx labs.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą programu Fluxx Labs, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne Fluxx Labs](#configure-fluxx-labs-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Fluxx Labs](#create-fluxx-labs-test-user)** — aby mieć odpowiednik Britta Simon w Fluxx Labs, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą programu Fluxx Labs, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Fluxx Labs** wybierz opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Informacje o domenie fluxx Labs i adresach URL — logowanie jednokrotne](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 

    | Środowisko | Wzorzec adresu URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io` |
    | Produkcja wstępna | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    | Środowisko | Wzorzec adresu URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Produkcja wstępna | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta Fluxx Labs,](mailto:travis@fluxxlabs.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie Fluxx Labs** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-fluxx-labs-single-sign-on"></a>Konfigurowanie logowania jednokrotnego fluxx labs

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Fluxx Labs jako administrator.

2. Wybierz **pozycję Administrator** poniżej sekcji **Ustawienia.**

    ![Konfiguracja Fluxx Labs](./media/fluxxlabs-tutorial/config1.png)

3. W panelu administracyjnym wybierz **wtyczki** > **integracji,** a następnie wybierz **SAML SSO-(Wyłączone)**

    ![Konfiguracja Fluxx Labs](./media/fluxxlabs-tutorial/config2.png)

4. W sekcji atrybutów wykonaj następujące czynności:

    ![Konfiguracja Fluxx Labs](./media/fluxxlabs-tutorial/config3.png)

    a. Zaznacz pole wyboru **SAML SSO.**

    b. W polach tekstowych **Ścieżka żądania** wpisz **/auth/saml**.

    d. W polach tekstowych **Ścieżka wywołania zwrotnego** wpisz **/auth/saml/callback**.

    d. W polu tekstowym **Url usługi konsumenta oświadczeń (adres URL logowania jednokrotnego)** wprowadź wartość **adresu URL odpowiedzi,** która została wprowadzona w witrynie Azure portal.

    e. W polu **tekstowym Odbiorcy(IDENTYFIKATOR JEDNOSTKI SP)** wprowadź wartość **identyfikatora,** która została wprowadzona w witrynie Azure portal.

    f. W polu tekstowym **docelowego adresu URL dostawcy tożsamości** wklej wartość adresu URL **logowania,** która została skopiowana z witryny Azure portal.

    g. Otwórz certyfikat zakodowany w bazie podstawowej-64 w notatniku, skopiuj jego zawartość do schowka, a następnie wklej go do pola tekstowego **Certyfikat dostawcy tożsamości.**

    h. W polu **tekstowym Format identyfikatora nazwy** wprowadź wartość `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Kliknij przycisk **Zapisz**.

    > [!NOTE]
    > Po zapisaniu zawartości pole będzie puste ze względów bezpieczeństwa, ale wartość została zapisana w konfiguracji.

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

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, udzielając dostępu do Fluxx Labs.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz **pozycję Fluxx Labs**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Fluxx Labs**.

    ![Łącze Fluxx Labs na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-fluxx-labs-test-user"></a>Utwórz użytkownika testowego Fluxx Labs

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do fluxx labs, muszą one być aprowizować w Fluxx Labs. W przypadku Fluxx Labs inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się na stronie firmy Fluxx Labs jako administrator.

2. Kliknij na poniższą wyświetlaną **ikonę**.

    ![Konfiguracja Fluxx Labs](./media/fluxxlabs-tutorial/config6.png)

3. Na pulpicie nawigacyjnym kliknij na poniższą wyświetlaną ikonę, aby otworzyć kartę **Nowe OSOBY.**

    ![Konfiguracja Fluxx Labs](./media/fluxxlabs-tutorial/config4.png)

4. W sekcji **NOWE OSOBY** wykonaj następujące czynności:

    ![Konfiguracja Fluxx Labs](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs używa poczty e-mail jako unikatowego identyfikatora logowania logowania do logowania. Populate the **SSO UID** field with the user's email address, that matches the email address, which they are using as login with SSO.

    b. Kliknij przycisk **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Fluxx Labs w Panelu dostępu należy automatycznie zalogować się do Fluxx Labs, dla którego skonfigurowano logującą się do rejestru jednośmiękowego. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

