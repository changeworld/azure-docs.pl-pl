---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bitbucket | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Kantega Usługa rejestracji Jednokrotnej dla Bitbucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 4f0b6dc725fbce2140ede98e7924730c4426e084
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64727506"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bitbucket

W tym samouczku dowiesz się, jak zintegrować Kantega Usługa rejestracji Jednokrotnej dla Bitbucket za pomocą usługi Azure Active Directory (Azure AD).
Integrowanie Kantega Usługa rejestracji Jednokrotnej dla Bitbucket z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego Kantega Bitbucket.
* Użytkownikom można automatycznie zalogowany do Kantega Usługa rejestracji Jednokrotnej dla Bitbucket (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bitbucket, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Kantega Usługa rejestracji Jednokrotnej dla Bitbucket logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Kantega Usługa rejestracji Jednokrotnej dla Bitbucket **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Dodawanie Kantega Usługa rejestracji Jednokrotnej dla Bitbucket z galerii

Aby skonfigurować integrację Kantega Usługa rejestracji Jednokrotnej dla Bitbucket w usłudze Azure AD, należy dodać Kantega Usługa rejestracji Jednokrotnej dla Bitbucket z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Kantega Usługa rejestracji Jednokrotnej dla Bitbucket w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Kantega Usługa rejestracji Jednokrotnej dla Bitbucket**, wybierz opcję **Kantega Usługa rejestracji Jednokrotnej dla Bitbucket** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Kantega Usługa rejestracji Jednokrotnej dla Bitbucket, na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega Bitbucket zależą od użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Kantega Usługa rejestracji Jednokrotnej dla Bitbucket musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bitbucket, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie Kantega jednokrotnego uzyskać Bitbucket-](#configure-kantega-sso-for-bitbucket-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie Kantega logowania jednokrotnego dla użytkownika testowego Bitbucket](#create-kantega-sso-for-bitbucket-test-user)**  — aby odpowiednikiem Britta Simon w Kantega Usługa rejestracji Jednokrotnej dla Bitbucket, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Kantega, aby uzyskać Bitbucket, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Kantega Usługa rejestracji Jednokrotnej dla Bitbucket** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Usługa rejestracji Jednokrotnej Kantega, Bitbucket, domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Usługa rejestracji Jednokrotnej Kantega, Bitbucket, domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Te wartości są odbierane podczas konfigurowania wtyczki Bitbucket, które zostało wyjaśnione w dalszej części tego samouczka.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. Na **skonfigurować logowanie Jednokrotne Kantega, aby uzyskać Bitbucket** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-kantega-sso-for-bitbucket-single-sign-on"></a>Konfigurowanie Kantega Usługa rejestracji Jednokrotnej dla Bitbucket logowanie jednokrotne

1. W oknie przeglądarki internetowej innej Zaloguj się w portalu administracyjnym Bitbucket jako administrator.

1. Kliknij przycisk koła zębatego, a następnie kliknij przycisk **Znajdź nowe dodatki**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Wyszukiwanie **Kantega Usługa rejestracji Jednokrotnej Bitbucket SAML i protokołu Kerberos** i kliknij przycisk **zainstalować** przycisk, aby zainstalować nowe wtyczki SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. Rozpocznie się instalacja dodatku plug-in.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. Po zakończeniu instalacji. Kliknij przycisk **Zamknij**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon33.png)

1. Kliknij pozycję **Zarządzaj**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon34.png)

1. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. W **SAML** sekcji. Wybierz **usługi Azure Active Directory (Azure AD)** z **dostawcy tożsamości Dodaj** listy rozwijanej.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Wybierz poziom subskrypcji jako **podstawowe**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. Na **właściwości aplikacji** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Kopiuj **identyfikator URI Identyfikatora aplikacji** wartości i używać go jako **identyfikator, adres URL odpowiedzi i adres URL logowania** na **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    b. Kliknij przycisk **Dalej**.

1. Na **Importuj metadane** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Wybierz **plik metadanych na moim komputerze**i przekazywania pliku metadanych, który został pobrany z witryny Azure portal.

    b. Kliknij przycisk **Dalej**.

1. Na **nazwę i logowania jednokrotnego lokalizację** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Dodaj nazwę dostawcy tożsamości w **nazwę dostawcy tożsamości** (np. usługi Azure AD) w polu tekstowym.

    b. Kliknij przycisk **Dalej**.

1. Zweryfikuj certyfikat podpisywania, a następnie kliknij przycisk **dalej**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. Na **kont użytkowników Bitbucket** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Wybierz **tworzenie użytkowników w firmy Bitbucket wewnętrznej katalogu, w razie potrzeby** i wprowadź nazwę odpowiedniej grupy użytkowników (może być wiele nie. grup rozdzielone przecinkami).

    b. Kliknij przycisk **Dalej**.

1. Kliknij przycisk **Zakończ**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. Na **znane domen dla usługi Azure AD** sekcję, wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Wybierz **znane domen** z lewego panelu strony.

    b. Wprowadź nazwę domeny w **znane domen** pola tekstowego.

    c. Kliknij pozycję **Zapisz**.

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

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Kantega Usługa rejestracji Jednokrotnej dla Bitbucket.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Kantega Usługa rejestracji Jednokrotnej dla Bitbucket**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Kantega Usługa rejestracji Jednokrotnej dla Bitbucket**.

    ![Logowanie Jednokrotne Kantega Bitbucket łącza na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Tworzenie Kantega logowania jednokrotnego dla użytkownika testowego Bitbucket

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się w usłudze Bitbucket, musi być obsługiwana w Bitbucket. W przypadku logowania jednokrotnego Kantega dla Bitbucket Inicjowanie obsługi to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Bitbucket jako administrator.

1. Kliknij ikonę ustawienia.

    ![Dodawanie pracownika](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. W obszarze **administracji** kliknij sekcję pozycję **użytkowników**.

    ![Dodawanie pracownika](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Kliknij pozycję **Create user** (Utwórz użytkownika).

    ![Dodawanie pracownika](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. Na **Create User** okna dialogowego strony, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    b. W polu tekstowym **Full Name** (Imię i nazwisko) wpisz imię i nazwisko użytkownika, np. Britta Simon.

    c. W polu tekstowym **Email address** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    d. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.

    e. W **Potwierdź hasło** polu tekstowym Wprowadź ponownie hasło użytkownika.

    f. Kliknij pozycję **Create user** (Utwórz użytkownika).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu przycisku logowania jednokrotnego Kantega kafelka Bitbucket w panelu dostępu powinny być automatycznie zarejestrowaniu w usłudze logowania jednokrotnego Kantega dla Bitbucket, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

