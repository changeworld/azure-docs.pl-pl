---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Pingboard | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Pingboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 341d8dd712b858572ec5df76b176258ca87c8857
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094427"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Pingboard

W tym samouczku dowiesz się, jak zintegrować Pingboard w usłudze Azure Active Directory (Azure AD).
Integrowanie Pingboard z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Pingboard.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Pingboard (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Pingboard, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Pingboard logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Pingboard **SP** i **tożsamości** jednokrotne logowanie inicjowane przez

* Obsługuje Pingboard [automatyczne Inicjowanie obsługi użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial) 

## <a name="adding-pingboard-from-the-gallery"></a>Dodawanie Pingboard z galerii

Aby skonfigurować integrację Pingboard w usłudze Azure AD, należy dodać Pingboard z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Pingboard z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Pingboard**, wybierz opcję **Pingboard** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Pingboard na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Pingboard w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Pingboard musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Pingboard, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Pingboard logowania jednokrotnego](#configure-pingboard-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Pingboard](#create-pingboard-test-user)**  — aby odpowiednikiem Britta Simon w Pingboard połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Pingboard, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Pingboard** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Pingboard domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `http://app.pingboard.com/sp`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<entity-id>.pingboard.com/auth/saml/consume`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Pingboard domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Pingboard](https://support.pingboard.com/) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

7. Na **Konfigurowanie Pingboard** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-pingboard-single-sign-on"></a>Konfigurowanie Pingboard logowanie jednokrotne

1. Aby skonfigurować logowanie Jednokrotne stronie Pingboard, Otwórz nowe okno przeglądarki i zaloguj się do swojego konta Pingboard. Musi być kontem administratora Pingboard, aby skonfigurować logowanie jednokrotne.

2. Wybierz z górnego menu,, **aplikacji > integracje**

    ![Konfigurowanie logowania jednokrotnego](./media/pingboard-tutorial/Pingboard_integration.png)

3. Na **integracje** strony, Znajdź **"Azure Active Directory"** kafelka, a następnie kliknij go.

    ![Pingboard pojedynczego logowania jednokrotnego integracji](./media/pingboard-tutorial/Pingboard_aad.png)

4. W modalne, kliknij przycisk poniżej **"Konfiguruj"**

    ![Przycisk Konfiguracja Pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

5. Na następnej stronie można zauważyć, że "Integracja logowania jednokrotnego usługi Azure jest włączone". Otwórz pobrany plik XML metadanych w programie Notatnik i Wklej zawartość **metadanych tożsamości**.

    ![Ekran konfiguracji logowania jednokrotnego Pingboard](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. Plik jest weryfikowana, a jeśli wszystko jest poprawna, logowania jednokrotnego zostanie ono włączone.

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

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Pingboard.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Pingboard**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Pingboard**.

    ![Link Pingboard na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-pingboard-test-user"></a>Tworzenie użytkownika testowego Pingboard

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Pingboard. Pingboard obsługuje automatyczna aprowizacja użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznej aprowizacji użytkowników można znaleźć [tutaj](pingboard-provisioning-tutorial.md).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Pingboard jako administrator.

2. Kliknij przycisk **"Dodaj Employee"** znajdujący się na **katalogu** strony.

    ![Dodawanie pracownika](./media/pingboard-tutorial/create_testuser_add.png)

3. Na **"Dodaj Employee"** okna dialogowego strony, wykonaj następujące czynności:

    ![Zaproś inne osoby](./media/pingboard-tutorial/create_testuser_name.png)

    a. W **imię i nazwisko** polu tekstowym wpisz pełną nazwę użytkownika, takie jak **Britta Simon**.

    b. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak **brittasimon@contoso.com** .

    c. W **stanowisko** polu tekstowym wpisz stanowisko Britta Simon.

    d. W **lokalizacji** listę rozwijaną, wybierz lokalizację, w pozycji Britta simon.

    e. Kliknij pozycję **Add** (Dodaj).

4. Ekran potwierdzenia pojawi się, aby upewnić się, Dodawanie użytkownika.

    ![Upewnij się](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Pingboard w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Pingboard, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurowanie aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial)
