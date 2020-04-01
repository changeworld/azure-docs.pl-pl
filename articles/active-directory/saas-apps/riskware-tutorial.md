---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem Riskware | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a oprogramowaniem riskware.
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
ms.openlocfilehash: 6eaa1be81d3ac0733c0829bc45e1b62f8aae5755
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72027119"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem riskware

W tym samouczku dowiesz się, jak zintegrować oprogramowanie riskware z usługą Azure Active Directory (Azure AD).
Integracja oprogramowania Riskware z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do Riskware.
* Można włączyć użytkowników do automatycznego logowania się do riskware (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem Riskware, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego riskware

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Riskware obsługuje sso inicjowane przez **SP**

## <a name="adding-riskware-from-the-gallery"></a>Dodawanie oprogramowania riskware z galerii

Aby skonfigurować integrację oprogramowania Riskware z usługą Azure AD, należy dodać oprogramowanie Riskware z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać oprogramowanie Riskware z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Riskware**, wybierz **Riskware** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Oprogramowanie riskware na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Riskware na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w oprogramowaniu Riskware.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą oprogramowania Riskware, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne riskware](#configure-riskware-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Riskware](#create-riskware-test-user)** — aby mieć odpowiednik Britta Simon w oprogramowaniu riskware, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą oprogramowania Riskware, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Riskware** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie riskware i adresach URL z logami jednokrotnymi](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: 
    
    | Środowisko| Wzorzec adresu URL|
    |--|--|
    | Testowanie akceptacyjne przez użytkowników|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | Prod| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | Demo| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: 
    
    | Środowisko| Wzorzec adresu URL|
    |--|--|
    | Testowanie akceptacyjne przez użytkowników| `https://riskcloud.net/uat` |
    | Prod| `https://riskcloud.net/prod` |
    | Demo| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Riskware,](mailto:support@pansoftware.com.au) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie oprogramowania ryzyka** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-riskware-single-sign-on"></a>Konfigurowanie logowania jednokrotnego oprogramowania Riskware

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Riskware jako administrator.

1. W prawym górnym rogu kliknij pozycję **Konserwacja,** aby otworzyć stronę konserwacji.

    ![Konfiguracje oprogramowania riskware utrzymują](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stronie konserwacji kliknij pozycję **Uwierzytelnianie**.

    ![Konfiguracja riskware authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Na stronie **Konfiguracja uwierzytelniania** wykonaj następujące czynności:

    ![Konfiguracja riskware authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Wybierz **pozycję Wpisz** jako **SAML** do uwierzytelniania.

    b. W polu tekstowym **Kod** wpisz kod, taki jak AZURE_UAT.

    d. W polu tekstowym **Opis** wpisz opis, taki jak konfiguracja platformy AZURE dla usługi SSO.

    d. W polu tekstowym **logowania jednokrotnego na stronie** wklej wartość adresu URL **logowania,** która została skopiowana z witryny Azure portal.

    e. W polu tekstowym **Wyloguj stronę** wklej wartość **adresu URL wylogowania,** która została skopiowana z witryny Azure Portal.

    f. W polu tekstowym **Pole formularza księgowania** wpisz nazwę pola obecnego w polu Odpowiedź księgowania zawierająca SAML, taki jak SAMLResponse

    g. W polu tekstowym **Nazwa tagu tożsamości XML** wpisz atrybut, który zawiera unikatowy identyfikator w odpowiedzi SAML, taki jak NameID.

    h. Otwórz pobrany **kod Xml metadanych** z witryny Azure portal w notatniku, skopiuj certyfikat z pliku Metadanych i wklej go do pola tekstowego **certyfikatu**

    i. W polu tekstowym **Adres URL konsumenta** wklej wartość **adresu URL odpowiedzi,** którą otrzymujesz od zespołu pomocy technicznej.

    j. W polu tekstowym **Wystawca** wklej wartość **identyfikatora**, którą otrzymujesz od zespołu pomocy technicznej.

    > [!Note]
    > Skontaktuj się z [zespołem pomocy technicznej klienta Riskware,](mailto:support@pansoftware.com.au) aby uzyskać te wartości

    k. Zaznacz pole wyboru **Użyj POST.**

    l. Zaznacz pole wyboru **Użyj żądania SAML.**

    m. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension`  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, udzielając dostępu do riskware.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz **opcję Riskware**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz opcję **Riskware**.

    ![Łącze Riskware na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-riskware-test-user"></a>Utwórz użytkownika testowego Riskware

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do riskware, muszą one być aprowidzone w riskware. W riskware inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do riskware jako administrator zabezpieczeń.

1. W prawym górnym rogu kliknij pozycję **Konserwacja,** aby otworzyć stronę konserwacji. 

    ![Konfiguracja riskware utrzymuje](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stronie konserwacji kliknij pozycję **Kontakty**.

    ![Riskware Konfiguracji ludzi](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Wybierz kartę **Szczegóły** i wykonaj następujące czynności:

    ![Szczegóły konfiguracji oprogramowania riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Wybierz **opcję Typ osoby,** jak pracownik.

    b. W polu tekstowym **Imię** wprowadź imię użytkownika, takiego jak **Britta**.

    d. W polu tekstowym **Nazwisko** wprowadź nazwisko użytkownika, takiego jak **Simon**.

1. Na karcie **Zabezpieczenia** wykonaj następujące czynności:

    ![Zabezpieczenia konfiguracji oprogramowania riskware](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. W sekcji **Uwierzytelnianie** wybierz tryb **uwierzytelniania,** który został skonfigurowany, taki jak konfiguracja platformy AZURE dla usługi SSO.

    b. W sekcji **Szczegóły logowania** w polu **tekstowym Identyfikator użytkownika** wprowadź `brittasimon@contoso.com`adres e-mail użytkownika w stylu .

    d. W **polach tekstowych Hasło** wprowadź hasło użytkownika.

1. Na karcie **Organizacja** wykonaj następujące czynności:

    ![Konfiguracja riskware org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Wybierz tę opcję jako organizację **Level1.**

    b. W sekcji **Podstawowe miejsce pracy osoby** w polach tekstowych **Lokalizacja** wpisz swoją lokalizację.

    d. W sekcji **Pracownik** wybierz pozycję **Stan pracownika, taki** jak Zwykły.

    d. Kliknij przycisk **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Riskware w Panelu dostępu należy automatycznie zalogować się do oprogramowania Riskware, dla którego skonfigurowano logowanie jednośmigłowe. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
