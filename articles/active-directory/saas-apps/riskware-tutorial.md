---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Riskware | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b2bfbed33433521fd086d474ea4b754f5435f5e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092923"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Riskware

W tym samouczku dowiesz się, jak zintegrować Riskware w usłudze Azure Active Directory (Azure AD).
Integrowanie Riskware z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Riskware.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Riskware (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Riskware, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Riskware logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Riskware **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-riskware-from-the-gallery"></a>Dodawanie Riskware z galerii

Aby skonfigurować integrację Riskware w usłudze Azure AD, należy dodać Riskware z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Riskware z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Riskware**, wybierz opcję **Riskware** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Riskware na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Riskware w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Riskware musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Riskware, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Riskware logowania jednokrotnego](#configure-riskware-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Riskware](#create-riskware-test-user)**  — aby odpowiednikiem Britta Simon w Riskware połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Riskware, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Riskware** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Riskware domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca:
    
    | Środowisko| Wzorzec URL|
    |--|--|
    | Testowanie akceptacyjne przez użytkowników|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PRODUKCYJNE| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. W **identyfikator jednostki** pole tekstowe, wpisz adres URL:
    
    | Środowisko| Wzorzec URL|
    |--|--|
    | Testowanie akceptacyjne przez użytkowników| `https://riskcloud.net/uat` |
    | PRODUKCYJNE| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Riskware](mailto:support@pansoftware.com.au) można uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

6. Na **Konfigurowanie Riskware** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-riskware-single-sign-on"></a>Konfigurowanie Riskware logowanie jednokrotne

1. W oknie przeglądarki internetowej innej Zaloguj się w witrynie firmy Riskware jako administrator.

1. W prawym górnym rogu, kliknij polecenie **konserwacji** aby otworzyć stronę konserwacji.

    ![Obsługa konfiguracji Riskware](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stronie konserwacji, kliknij przycisk **uwierzytelniania**.

    ![Authen Riskware konfiguracji](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. W **konfiguracji uwierzytelniania** strony, wykonaj następujące czynności:

    ![Authenconfig Riskware konfiguracji](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Wybierz **typu** jako **SAML** do uwierzytelniania.

    b. W **kodu** polu tekstowym wpisz swój kod, takich jak AZURE_UAT.

    c. W **opis** polu tekstowym wpisz opis takich jak AZURE konfiguracji logowania jednokrotnego.

    d. W **pojedynczego logowania na stronie** pola tekstowego, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal.

    e. W **Wyloguj strony** pola tekstowego, Wklej **adres URL wylogowania** wartości, które zostały skopiowane z witryny Azure portal.

    f. W **pola formularza wpis** polu tekstowym wpisz nazwę pola, które jest obecny w odpowiedzi na wpis, zawierający SAML, takich jak SAMLResponse

    g. W **nazwa tagu tożsamości XML** atrybutu typu, który zawiera unikatowy identyfikator w odpowiedzi SAML, takich jak NameID polu tekstowym.

    h. Otwórz pobrany **Xml metadanych** skopiuj certyfikat z pliku metadanych z witryny Azure portal w programie Notatnik i wklej go w **certyfikatu** textbox

    i. W **adres URL klienta** pola tekstowego, Wklej wartość **adres URL odpowiedzi**, którego można uzyskać z zespołem pomocy technicznej.

    j. W **wystawcy** pola tekstowego, Wklej wartość **identyfikator**, którego można uzyskać z zespołem pomocy technicznej.

    > [!Note]
    > Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Riskware](mailto:support@pansoftware.com.au) do uzyskania tych wartości.

    k. Wybierz **wpisu użyj** pola wyboru.

    l. Wybierz **żądania języka SAML użyj** pola wyboru.

    m. Kliknij pozycję **Zapisz**.

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

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Riskware.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Riskware**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Riskware**.

    ![Link Riskware na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-riskware-test-user"></a>Tworzenie użytkownika testowego Riskware

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Riskware, musi być obsługiwana w Riskware. W Riskware Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Riskware jako Administrator zabezpieczeń.

1. W prawym górnym rogu, kliknij polecenie **konserwacji** aby otworzyć stronę konserwacji. 

    ![Przechowuje Riskware konfiguracji](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stronie konserwacji, kliknij przycisk **osób**.

    ![Konfiguracja Riskware osoby](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Wybierz **szczegóły** kartę i wykonaj następujące czynności:

    ![Szczegóły konfiguracji Riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Wybierz **typ osoby** , takich jak pracownika.

    b. W **imię** polu tekstowym Wprowadź imię użytkownika, takich jak **Britta**.

    c. W **nazwisko** polu tekstowym Wprowadź nazwisko użytkownika, takich jak **Simon**.

1. Na **zabezpieczeń** karcie, wykonaj następujące czynności:

    ![Riskware konfiguracji zabezpieczeń](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. W obszarze **uwierzytelniania** zaznacz **uwierzytelniania** trybu, który ma ustawienia, takie jak konfiguracji platformy AZURE dla logowania jednokrotnego.

    b. W obszarze **szczegóły logowania** sekcji w **identyfikator użytkownika** polu tekstowym wprowadź adres e-mail użytkownika, takich jak `brittasimon@contoso.com`.

    c. W **hasło** polu tekstowym Wprowadź hasło użytkownika.

1. Na **organizacji** karcie, wykonaj następujące czynności:

    ![Konfiguracja Riskware organizacji](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Wybierz opcję jako **Level1** organizacji.

    b. W obszarze **podstawowy obszar roboczy osoby** sekcji w **lokalizacji** polu tekstowym wpisz swoją lokalizację.

    c. W obszarze **pracowników** zaznacz **stanu** dorywczy, takich jak.

    d. Kliknij pozycję **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Riskware w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Riskware, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
