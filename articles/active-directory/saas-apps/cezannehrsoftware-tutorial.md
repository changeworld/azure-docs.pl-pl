---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Cezanne HR Software | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Cezanne HR Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78d259c0354a1519fa57633a68a1dcfa5a183890
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105694"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją Cezanne HR Software

Z tego samouczka dowiesz się, jak zintegrować aplikację Cezanne HR Software z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji Cezanne HR Software z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji Cezanne HR Software.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Cezanne HR Software (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Cezanne HR Software, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Cezanne HR Software z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Cezanne HR Software obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Dodawanie aplikacji Cezanne HR Software z galerii

Aby skonfigurować integrację aplikacji Cezanne HR Software z usługą Azure AD, należy dodać aplikację Cezanne HR Software z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Cezanne HR Software z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Cezanne HR Software**, wybierz pozycję **Cezanne HR Software** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Aplikacja Cezanne HR Software na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Cezanne HR Software, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Cezanne HR Software.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Cezanne HR Software, należy ukończyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Cezanne HR Software](#configure-cezanne-hr-software-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji Cezanne HR Software](#create-cezanne-hr-software-test-user)** — aby mieć w aplikacji Cezanne HR Software odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować integrację usługi Azure AD z aplikacją Cezanne HR Software, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Cezanne HR Software** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje na temat domeny i adresów URL logowania jednokrotnego aplikacji Cezanne HR Software](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Cezanne HR Software](https://cezannehr.com/services/support/) w celu uzyskania tych wartości.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji Cezanne HR Software** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Cezanne HR Software

1. W innym oknie przeglądarki internetowej zaloguj się do dzierżawy aplikacji Cezanne HR Software jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **System Setup** (Konfiguracja systemu). Przejdź do pozycji **Security Settings**. (Ustawienia zabezpieczeń). Następnie przejdź do pozycji **Single Sign-On Configuration** (Konfiguracja logowania jednokrotnego).

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. W panelu **Allow users to log in using the following Single Sign-On (SSO) Service** (Zezwalaj użytkownikom na logowanie przy użyciu następującej usługi logowania jednokrotnego) zaznacz pole **SAML 2.0** i wybierz opcję **Advanced Configuration** (Konfiguracja zaawansowana).

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Kliknij przycisk **Add New** (Dodaj nowe).

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Wykonaj poniższe czynności w sekcji **SAML 2.0 IDENTITY PROVIDERS** (DOSTAWCY TOŻSAMOŚCI SAML 2.0).

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Wprowadź nazwę dostawcy tożsamości w polu **Display Name** (Nazwa wyświetlana).

    b. W polu tekstowym **Entity Identifier** (Identyfikator jednostki) wklej wartość pola **Identyfikator usługi Azure AD** skopiowaną z witryny Azure Portal.

    c. Zmień wartość pola **SAML Binding** (Powiązanie SAML) na „POST”.

    d. W polu tekstowym **Security Token Service Endpoint** (Punkt końcowy usługi tokenu zabezpieczającego) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    e. W polu tekstowym User ID Attribute Name (Nazwa atrybutu identyfikatora użytkownika) wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    f. Kliknij ikonę **Upload** (Przekaż), aby przekazać certyfikat pobrany z witryny Azure Portal.

    g. Kliknij przycisk **OK**.

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Cezanne HR Software.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Cezanne HR Software**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Cezanne HR Software**.

    ![Link aplikacji Cezanne HR Software na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-cezanne-hr-software-test-user"></a>Tworzenie użytkownika testowego aplikacji Cezanne HR Software

Aby umożliwić użytkownikom usługi Azure AD logowanie do aplikacji Cezanne HR Software, należy ich aprowizować w aplikacji Cezanne HR Software. Aprowizowanie w aplikacji Cezanne HR Software to zadanie wykonywane ręcznie.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do firmowej witryny aplikacji Cezanne HR Software jako administrator.

2. W okienku nawigacji po lewej stronie kliknij pozycję **System Setup** (Konfiguracja systemu). Przejdź do pozycji **Manage Users** (Zarządzaj użytkownikami). Następnie przejdź do pozycji **Add New User** (Dodaj nowego użytkownika).

    ![Nowy użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nowy użytkownik")

3. W sekcji **PERSON DETAILS** (SZCZEGÓŁY OSOBY) wykonaj następujące czynności:

    ![Nowy użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nowy użytkownik")

    a. Ustaw pozycję **Internal User** (Użytkownik wewnętrzny) na OFF (WYŁ.).

    b. W polu tekstowym **First Name** (Imię) wpisz imię użytkownika, takie jak **Britta**.  

    c. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak **Simon**.

    d. W polu tekstowym **E-mail** (Adres e-mail) wpisz adres e-mail użytkownika, np. Brittasimon@contoso.com.

4. W sekcji **Account Information** (Informacje o koncie) wykonaj następujące kroki:

    ![Nowy użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nowy użytkownik")

    a. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    b. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.

    c. Wybierz pozycję **HR Professional** (Specjalista ds. kadr) w polu **Security Role** (Rola zabezpieczeń).

    d. Kliknij przycisk **OK**.

5. Przejdź do karty **Single Sign-On** (Logowanie jednokrotne) i wybierz pozycję **Add New** (Dodaj nowy) w obszarze **SAML 2.0 Identifiers** (Identyfikatory języka SAML 2.0).

    ![Użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Użytkownik")

6. Wybierz dostawcę tożsamości w polu **Identity Provider** (Dostawca tożsamości) i w polu tekstowym **User Identifier** wprowadź adres e-mail konta użytkownika Britta Simon.

    ![Użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Użytkownik")

7. Kliknij przycisk **Zapisz** przycisku.

    ![Użytkownik](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Użytkownik")

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Cezanne HR Software w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Cezanne HR Software, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
