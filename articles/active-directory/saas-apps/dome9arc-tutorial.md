---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Dome9 Arc | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86112c6d1c720787af80a9846b5c94ec59895ecb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65862147"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją Dome9 Arc

Z tego samouczka dowiesz się, jak zintegrować aplikację Dome9 Arc z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Dome9 Arc z usługą Azure AD oferuje następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji Dome9 Arc.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Dome9 Arc (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Dome9 Arc, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Dome9 Arc z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Dome9 Arc obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług** oraz **dostawcę tożsamości**

## <a name="adding-dome9-arc-from-the-gallery"></a>Dodawanie aplikacji Dome9 Arc z galerii

Aby skonfigurować integrację aplikacji Dome9 Arc z usługą Azure AD, musisz dodać aplikację Dome9 Arc z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Dome9 Arc z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Dome9 Arc**, wybierz pozycję **Dome9 Arc** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Dome9 Arc na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Dome9 Arc, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Dome9 Arc.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Dome9 Arc, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Dome9 Arc](#configure-dome9-arc-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji Dome9 Arc](#create-dome9-arc-test-user)** — aby mieć w aplikacji Dome9 Arc odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Dome9 Arc, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Dome9 Arc** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Informacje o domenie i adresach URL aplikacji Dome9 Arc na potrzeby logowania jednokrotnego](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://secure.dome9.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Wartość nazwy firmy wybierzesz w portalu administracyjnym aplikacji Dome9, co objaśniono w dalszej części tego samouczka.

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Informacje o domenie i adresach URL aplikacji Dome9 Arc na potrzeby logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta aplikacji Dome9 Arc](mailto:support@dome9.com) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Aplikacja Dome9 Arc oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

7. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności: 

    | Name (Nazwa) |  Atrybut źródłowy|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

9. W sekcji **Konfigurowanie aplikacji Dome9 Arc** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-dome9-arc-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Dome9 Arc

1. W innym oknie przeglądarki internetowej zaloguj się do swojej firmowej witryny aplikacji Dome9 Arc jako administrator.

2. Kliknij pozycję **Profile Settings** (Ustawienia profilu) w prawym górnym rogu, a następnie kliknij przycisk **Account Settings** (Ustawienia konta). 

    ![Konfiguracja aplikacji Dome9 Arc](./media/dome9arc-tutorial/configure1.png)

3. Przejdź na kartę **SSO** (Logowanie jednokrotne), a następnie kliknij przycisk **ENABLE** (WŁĄCZ).

    ![Konfiguracja aplikacji Dome9 Arc](./media/dome9arc-tutorial/configure2.png)

4. W sekcji SSO Configuration (Konfiguracja logowania jednokrotnego) wykonaj następujące czynności:

    ![Konfiguracja aplikacji Dome9 Arc](./media/dome9arc-tutorial/configure3.png)

    a. Wprowadź nazwę firmy w polu tekstowym **Account ID** (Identyfikator konta). Ta wartość jest używana w adresie URL odpowiedzi, o którym wspomniano w sekcji Adres URL witryny Azure Portal.

    b. W polu tekstowym **Issuer** (Wystawca) wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    c. W polu tekstowym **Idp endpoint url** (Adres URL punktu końcowego dostawcy tożsamości) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. Otwórz w Notatniku pobrany certyfikat zakodowany w formacie Base64, skopiuj zawartość do schowka, a następnie wklej ją w polu tekstowym **SAML X.509 certificate** (Certyfikat X.509 SAML).

    e. Kliknij pozycję **Zapisz**.

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Dome9 Arc.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Dome9 Arc**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Dome9 Arc**.

    ![Link aplikacji Dome9 Arc na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-dome9-arc-test-user"></a>Tworzenie użytkownika testowego aplikacji Dome9 Arc

Aby umożliwić użytkownikom usługi Azure AD logowanie do aplikacji Dome9 Arc, należy aprowizować ich w aplikacji Dome9 Arc. Aplikacja Dome9 Arc obsługuje aprowizowanie typu just-in-time, ale aby działało ono poprawnie, użytkownik musi wybrać określoną **rolę** i przypisać tę samą użytkownikowi.

   >[!Note]
   >W celu utworzenia **roli** i uzyskania szczegółowych informacji skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Dome9 Arc](https://dome9.com/about/contact-us/).

**Aby ręcznie aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do swojej firmowej witryny aplikacji Dome9 Arc jako administrator.

2. Kliknij pozycję **Users & Roles** (Użytkownicy i role), a następnie kliknij pozycję **Users** (Użytkownicy).

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user1.png)

3. Kliknij przycisk **ADD USER** (DODAJ UŻYTKOWNIKA).

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user2.png)

4. W sekcji **Create User** (Tworzenie użytkownika) wykonaj następujące kroki:

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user3.png)

    a. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    b. W polu tekstowym **First Name** (Imię) wpisz imię użytkownika, takie jak Britta.

    c. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak Simon.

    d. W polu **SSO User** (Użytkownik logowania jednokrotnego) wybierz ustawienie **On** (Włączone).

    e. Kliknij przycisk **UTWÓRZ**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka aplikacji Dome9 Arc w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Dome9 Arc, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

