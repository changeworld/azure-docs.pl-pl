---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Syncplicity | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 041f1e9706c7d815dad1a33104e7dd15b2cc3893
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270238"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Syncplicity

W tym samouczku dowiesz się, jak zintegrować Syncplicity w usłudze Azure Active Directory (Azure AD).
Integrowanie Syncplicity z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Syncplicity.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Syncplicity (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Syncplicity, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Syncplicity logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Syncplicity **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-syncplicity-from-the-gallery"></a>Dodawanie Syncplicity z galerii

Aby skonfigurować integrację Syncplicity w usłudze Azure AD, należy dodać Syncplicity z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Syncplicity z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Syncplicity**, wybierz opcję **Syncplicity** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Syncplicity na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Syncplicity w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Syncplicity musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Syncplicity, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie Syncplicity logowania jednokrotnego](#configure-syncplicity-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego Syncplicity](#create-syncplicity-test-user)**  — aby odpowiednikiem Britta Simon w Syncplicity połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Syncplicity, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Syncplicity** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Syncplicity domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.syncplicity.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Syncplicity](https://www.syncplicity.com/contact-us) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie Syncplicity** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-syncplicity-single-sign-on"></a>Konfigurowanie Syncplicity logowanie jednokrotne

1. Zaloguj się do Twojej **Syncplicity** dzierżawy.

1. W menu u góry kliknij **administratora**, wybierz opcję **ustawienia**, a następnie kliknij przycisk **niestandardowej domeny i logowanie jednokrotne**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na **pojedynczego logowania jednokrotnego (SSO)** okna dialogowego strony, wykonaj następujące czynności:

    ![Logowanie jednokrotne \(logowania jednokrotnego\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. W **domena niestandardowa** polu tekstowym wpisz nazwę domeny.
  
    b. Wybierz **włączone** jako **pojedynczego stanu logowania jednokrotnego**.

    c. W **identyfikator jednostki** pola tekstowego, Wklej wartość **usługi Azure Ad identyfikator** skopiowanej w witrynie Azure portal.

    d. W **adres URL logowania strony** pola tekstowego, Wklej **adres URL logowania** skopiowanej w witrynie Azure portal.

    e. W **adres URL strony wylogowania** pola tekstowego, Wklej **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    f. W **certyfikatu dostawcy tożsamości**, kliknij przycisk **Choose file**, a następnie przekaż certyfikat, który został pobrany z witryny Azure portal.

    g. Kliknij przycisk **SAVE CHANGES** (ZAPISZ ZMIANY).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Syncplicity.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Syncplicity**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Syncplicity**.

    ![Link Syncplicity na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.
    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-syncplicity-test-user"></a>Tworzenie użytkownika testowego Syncplicity

Dla użytkowników usługi AAD można było się zalogować musi być obsługiwana Syncplicity aplikacji. W tej sekcji opisano sposób tworzenia konta użytkownika usługi AAD w Syncplicity.

**Aby udostępnić konto Syncplicity, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Syncplicity** dzierżawy (na przykład: `https://company.Syncplicity.com`).

1. Kliknij przycisk **administratora** i wybierz **kont użytkowników** a następnie kliknij przycisk **Dodaj użytkownik**.

    ![Zarządzanie użytkownikami](./media/syncplicity-tutorial/ic769764.png "Zarządzanie użytkownikami")

1. Typ **adresy E-mail** chcesz aprowizować konta usługi Azure AD, wybierz **użytkownika** jako **roli**, a następnie kliknij przycisk **dalej**.

    ![Informacje o koncie](./media/syncplicity-tutorial/ic769765.png "informacje o koncie")

    > [!NOTE]
    > Właściciel konta usługi AAD otrzymuje wiadomość e-mail, w tym link do potwierdzenia i aktywować konto.

1. Wybierz grupę w Twojej firmie, która nowy użytkownik powinien zostać członkiem, a następnie kliknij **dalej**.

    ![Członkostwo w grupie](./media/syncplicity-tutorial/ic769772.png "członkostwo w grupie")

    > [!NOTE]
    > Jeśli nie ma żadnych grup, na liście, kliknij przycisk **dalej**.

1. Wybierz foldery, o których chcesz umieścić pod kontrolą firmy Syncplicity na komputerze użytkownika, a następnie kliknij przycisk **dalej**.

    ![Foldery Syncplicity](./media/syncplicity-tutorial/ic769773.png "Syncplicity folderów")

> [!NOTE]
> Można użyć jakichkolwiek innych Syncplicity użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Syncplicity do aprowizacji kont użytkowników usługi AAD.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Syncplicity w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Syncplicity, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
