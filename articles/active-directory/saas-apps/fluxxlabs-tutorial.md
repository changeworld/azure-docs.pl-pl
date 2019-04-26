---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Fluxx Labs | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Fluxx Labs.
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
ms.openlocfilehash: e1951a65c48c32f2ce4af722400d03c20dfa684b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60279007"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Fluxx Labs

W tym samouczku dowiesz się, jak integrować laboratoria Fluxx za pomocą usługi Azure Active Directory (Azure AD).
Integrowanie laboratoriów Fluxx z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do laboratoriów Fluxx.
* Użytkownikom można automatycznie zalogowany do laboratoriów Fluxx (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Fluxx Labs, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Laboratoria Fluxx pojedynczego logowania jednokrotnego włączonych subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Fluxx Labs **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-fluxx-labs-from-the-gallery"></a>Dodawanie Fluxx laboratoria z galerii

Aby skonfigurować integrację Fluxx laboratoriów w usłudze Azure AD, należy dodać Fluxx laboratoria z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Fluxx laboratoria z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Fluxx Labs**, wybierz opcję **Fluxx Labs** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

     ![Laboratoria Fluxx na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą Fluxx Labs w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w laboratoriach Fluxx musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Fluxx Labs, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie Fluxx Labs logowania jednokrotnego](#configure-fluxx-labs-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego Fluxx Labs](#create-fluxx-labs-test-user)**  — aby odpowiednikiem Britta Simon w laboratoriach Fluxx, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą Fluxx Labs, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Fluxx Labs** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Fluxx Labs domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | Środowisko | Wzorzec URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io` |
    | Produkcja wstępna | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    | Środowisko | Wzorzec URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Produkcja wstępna | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Labs Fluxx](mailto:travis@fluxxlabs.com) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. Na **skonfiguruj laboratoria Fluxx** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-fluxx-labs-single-sign-on"></a>Konfigurowanie Fluxx Labs logowania jednokrotnego

1. W oknie przeglądarki internetowej innej należy Zaloguj się jako administrator do witryny firmy Fluxx Labs.

2. Wybierz **administratora** poniżej **ustawienia** sekcji.

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config1.png)

3. W panelu administracyjnym wybierz **wtyczek** > **integracje** , a następnie wybierz **SAML SSO-(Disabled)**

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config2.png)

4. W sekcji atrybut wykonaj następujące czynności:

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config3.png)

    a. Wybierz **logowania jednokrotnego SAML** pola wyboru.

    b. W **ścieżkę żądania** polu tekstowym wpisz **saml/auth/**.

    c. W **ścieżki wywołania zwrotnego** polu tekstowym wpisz **/auth/saml/callback**.

    d. W **Url(Single Sign-On URL) usługi konsumenta potwierdzenie** polu tekstowym wprowadź **adres URL odpowiedzi** wartości, które zostały wprowadzone w witrynie Azure portal.

    e. W **odbiorców (identyfikator jednostki SP)** polu tekstowym wprowadź **identyfikator** wartości, które zostały wprowadzone w witrynie Azure portal.

    f. W **adres URL docelowej logowania jednokrotnego dostawcy tożsamości** pola tekstowego, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal.

    g. Otwórz certyfikat kodowany algorytmem base-64 w Notatniku, skopiuj jego zawartość do schowka, a następnie wklej ją w polu tekstowym **Certyfikat dostawcy tożsamości**.

    h. W **identyfikator nazwy formatu** polu tekstowym wprowadź wartość `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Gdy zawartość zapisane, pole zostanie wyświetlona pusta dla zabezpieczeń, ale wartość został zapisany w konfiguracji.

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

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do laboratoriów Fluxx za pomocą platformy Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Fluxx Labs**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Fluxx Labs**.

    ![Link Fluxx Labs na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-fluxx-labs-test-user"></a>Tworzenie użytkownika testowego Fluxx Labs

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do laboratoriów Fluxx, musi być obsługiwana laboratoriów Fluxx. W przypadku laboratoriów Fluxx aprowizacji to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Fluxx Labs jako administrator.

2. Kliknij poniżej wyświetlana **ikonę**.

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config6.png)

3. Na pulpicie nawigacyjnym kliknij poniżej wyświetlone ikonę, aby otworzyć **nowych osób** karty.

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config4.png)

4. Na **nowych osób** sekcji, wykonaj następujące czynności:

    ![Konfiguracja Labs Fluxx](./media/fluxxlabs-tutorial/config5.png)

    a. Laboratoria Fluxx za pomocą adresu e-mail jako unikatowy identyfikator logowania jednokrotnego logowania. Wypełnij **UID logowania jednokrotnego** pole za pomocą adresu e-mail użytkownika, który jest zgodny z adresem e-mail i używają podczas logowania przy użyciu logowania jednokrotnego.

    b. Kliknij pozycję **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Fluxx Labs w panelu dostępu, możesz powinny być automatycznie zalogowany do laboratoriów Fluxx, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

