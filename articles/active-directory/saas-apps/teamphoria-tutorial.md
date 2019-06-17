---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Teamphoria | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 05387d2fd9b38ffa8137668e01b6d7cff3346ac8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089010"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Teamphoria

W tym samouczku dowiesz się, jak zintegrować Teamphoria w usłudze Azure Active Directory (Azure AD).
Integrowanie Teamphoria z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Teamphoria.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Teamphoria (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Teamphoria, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Teamphoria logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Teamphoria **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-teamphoria-from-the-gallery"></a>Dodawanie Teamphoria z galerii

Aby skonfigurować integrację Teamphoria w usłudze Azure AD, należy dodać Teamphoria z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Teamphoria z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Teamphoria**, wybierz opcję **Teamphoria** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Teamphoria na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Teamphoria w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Teamphoria musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Teamphoria, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Teamphoria logowania jednokrotnego](#configure-teamphoria-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Teamphoria](#create-teamphoria-test-user)**  — aby odpowiednikiem Britta Simon w Teamphoria połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Teamphoria, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Teamphoria** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Teamphoria domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-intiated.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<sub-domain>.teamphoria.com/login`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Musisz zaktualizować tę wartość za pomocą adresu URL logowania rzeczywistych. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Teamphoria](https://www.teamphoria.com/) można pobrać adresu URL logowania jednokrotnego. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie Teamphoria** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-teamphoria-single-sign-on"></a>Konfigurowanie Teamphoria logowanie jednokrotne

1. Aby skonfigurować logowanie jednokrotne na **Teamphoria** strony logowania do aplikacji Teamphoria jako administrator.

1. Przejdź do **ustawienia administratora** kliknij opcję w pasku narzędzi po lewej stronie i na karcie Konfigurowanie **logowanie Jednokrotne** można otworzyć okna konfiguracji logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/admin_sso_configure.png)

1. Kliknij pozycję **Dodaj nowego DOSTAWCĘ tożsamości** opcję w prawym górnym rogu, aby otworzyć formularz służący do dodawania ustawień logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. Wprowadź szczegółowe informacje w polach, zgodnie z opisem poniżej-

    ![Konfigurowanie logowania jednokrotnego](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **NAZWA WYŚWIETLANA**: Na stronie administratora, wprowadź nazwę wyświetlaną wtyczki.

    b. **NAZWA PRZYCISKU**: Nazwa karty, która będzie wyświetlana na stronie logowania dla logowania za pomocą logowania jednokrotnego.

    c. **CERTYFIKAT**: Otwórz certyfikat pobrany wcześniej z witryny Azure portal w programie Notatnik, skopiuj zawartość tego samego i wklej go poniżej w polu.

    d. **PUNKT WEJŚCIA**: Wklej **adres URL logowania** skopiowany wcześniej z witryny Azure portal.

    e. Przełącz opcję **ON** i kliknij pozycję **ZAPISZ**.

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

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Teamphoria.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Teamphoria**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Teamphoria**.

    ![Link Teamphoria na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-teamphoria-test-user"></a>Tworzenie użytkownika testowego Teamphoria

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Teamphoria, musi być obsługiwana w Teamphoria. W przypadku Teamphoria Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Teamphoria jako administrator.

1. Kliknij pozycję **administratora** ustawień na pasku narzędzi po lewej stronie i w obszarze **ZARZĄDZAJ** karcie kliknij **użytkowników** o otwarcie strony administratora dla użytkowników.

    ![Dodawanie pracownika](./media/teamphoria-tutorial/admin_manage_users.png)

1. Kliknij pozycję **ZAPROSIĆ ręczne** opcji.

    ![Zaproś inne osoby](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Na tej stronie wykonaj następujące działania.

    ![Zaproś inne osoby](./media/teamphoria-tutorial/manual_user_invite.png)

    a. W **adres E-mail** polu tekstowym wprowadź **adres e-mail** użytkownika, takich jak BrittaSimon.

    b. W **imię** polu tekstowym Wprowadź imię użytkownika, takich jak **Britta**.

    c. W **nazwisko** polu tekstowym Wprowadź nazwisko użytkownika, takich jak **Simon**.

    d. Kliknij przycisk **zaproszenia 1 użytkownika**. Użytkownik musi zaakceptować zaproszenie tworzone w systemie.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Teamphoria w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Teamphoria, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

