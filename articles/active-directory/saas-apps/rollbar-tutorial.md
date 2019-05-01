---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą pałąka zabezpieczającego | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i pałąka zabezpieczającego.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57537e54-9388-4272-a610-805ce45a451f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/15/2019
ms.author: jeedes
ms.openlocfilehash: 7f6e6b262dccd5b68c5a55c3d96a894205df8933
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695890"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą pałąka zabezpieczającego

W tym samouczku dowiesz się, jak zintegrować pałąka zabezpieczającego w usłudze Azure Active Directory (Azure AD).
Integrowanie pałąka zabezpieczającego z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do pałąka zabezpieczającego.
* Aby umożliwić użytkownikom można automatycznie zalogowany do pałąka zabezpieczającego (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą pałąka zabezpieczającego, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Pałąka zabezpieczającego logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje pałąka zabezpieczającego **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-rollbar-from-the-gallery"></a>Dodawanie pałąka zabezpieczającego z galerii

Aby skonfigurować integrację pałąka zabezpieczającego w usłudze Azure AD, należy dodać pałąka zabezpieczającego z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać pałąka zabezpieczającego z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **pałąka zabezpieczającego**, wybierz opcję **pałąka zabezpieczającego** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Pałąka zabezpieczającego na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą pałąka zabezpieczającego w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w pałąka zabezpieczającego musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą pałąka zabezpieczającego, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie pałąka zabezpieczającego logowania jednokrotnego](#configure-rollbar-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego pałąka zabezpieczającego](#create-rollbar-test-user)**  — aby odpowiednikiem Britta Simon w pałąka zabezpieczającego, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z pałąka zabezpieczającego, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **pałąka zabezpieczającego** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Pałąka zabezpieczającego domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `https://saml.rollbar.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://rollbar.com/<accountname>/saml/sso/azure/`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Pałąka zabezpieczającego domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://rollbar.com/<accountname>/saml/login/azure/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta pałąka zabezpieczającego](mailto:support@rollbar.com) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. Na **Konfigurowanie pałąka zabezpieczającego** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-rollbar-single-sign-on"></a>Konfigurowanie pałąka zabezpieczającego logowanie jednokrotne

1. W oknie przeglądarki internetowej innej Zaloguj się w witrynie pałąka zabezpieczającego firmy jako administrator.

1. Kliknij pozycję **ustawienia profilu** w prawym górnym rogu, a następnie kliknij przycisk **ustawienia nazwy konta**.

    ![Konfigurowanie](./media/rollbar-tutorial/general.png)

1. Kliknij przycisk **dostawcy tożsamości** w obszarze zabezpieczeń.

    ![Konfigurowanie](./media/rollbar-tutorial/configure1.png)

1. W **dostawcy tożsamości SAML** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie](./media/rollbar-tutorial/configure2.png)

    a. Wybierz **AZURE** z **dostawcy tożsamości SAML** listy rozwijanej.

    b. Otwórz plik metadanych w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **metadanych SAML** pola tekstowego.

    c. Kliknij pozycję **Zapisz**.

1. Po kliknięciu przycisku Zapisz przycisku ekranu będą się następująco:

    ![Konfigurowanie](./media/rollbar-tutorial/configure3.png)

    > [!NOTE]
    > Aby wykonać poniższe czynności, należy najpierw dodać siebie jako użytkownik pałąka zabezpieczającego aplikacji na platformie Azure.
    >

    a. Jeśli chcesz wymagać od wszystkich użytkowników, aby uwierzytelnić się za pośrednictwem platformy Azure, a następnie kliknij przycisk **zalogowania się za pośrednictwem dostawcy tożsamości** konieczność ponownego uwierzytelnienia za pośrednictwem platformy Azure.  

    b.  Gdy następuje powrót do ekranu, wybierz **wymagają logowania za pomocą dostawcy tożsamości SAML** pola wyboru.

    b. Kliknij pozycję **Zapisz**.

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

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do pałąka zabezpieczającego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **pałąka zabezpieczającego**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pałąka zabezpieczającego**.

    ![Link pałąka zabezpieczającego na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-rollbar-test-user"></a>Tworzenie użytkownika testowego pałąka zabezpieczającego

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do pałąka zabezpieczającego, musi być obsługiwana w pałąka zabezpieczającego. W przypadku pałąka zabezpieczającego Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy pałąka zabezpieczającego jako administrator.

1. Kliknij pozycję **ustawienia profilu** w prawym górnym rogu, a następnie kliknij przycisk **ustawienia nazwy konta**.

    ![Użytkownik](./media/rollbar-tutorial/general.png)

1. Kliknij pozycję **Użytkownicy**.

    ![Dodawanie pracownika](./media/rollbar-tutorial/user1.png)

1. Kliknij przycisk **Zaproś członków zespołu**.

    ![Zaproś inne osoby](./media/rollbar-tutorial/user2.png)

1. W polu tekstowym wprowadź nazwę użytkownika, takich jak **brittasimon\@contoso.com** i kliknięcie **Add/zaproszenia**.

    ![Zaproś inne osoby](./media/rollbar-tutorial/user3.png)

1. Użytkownik otrzymuje wiadomość z zaproszeniem i po zaakceptowaniu go on utworzony w systemie.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka pałąka zabezpieczającego w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze pałąka zabezpieczającego, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

