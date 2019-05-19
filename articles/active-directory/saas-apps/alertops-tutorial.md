---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją AlertOps | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją AlertOps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7914bc3775631f3cc5d6ae68fed10c6d5fecb853
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65864283"
---
# <a name="tutorial-azure-active-directory-integration-with-alertops"></a>Samouczek: integracja usługi Azure Active Directory z aplikacją AlertOps

Z tego samouczka dowiesz się, jak zintegrować aplikację AlertOps z usługą Azure Active Directory (Azure AD).
Integracja aplikacji AlertOps z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji AlertOps.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji AlertOps (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją AlertOps, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji AlertOps z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja AlertOps obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług i dostawcę tożsamości**

## <a name="adding-alertops-from-the-gallery"></a>Dodawanie aplikacji AlertOps z galerii

Aby skonfigurować integrację aplikacji AlertOps z usługą Azure AD, należy z poziomu galerii dodać to rozwiązanie do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację AlertOps z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz nazwę **AlertOps**, wybierz pozycję **AlertOps** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Aplikacja AlertOps na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją AlertOps, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji AlertOps.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji AlertOps, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji AlertOps](#configure-alertops-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji AlertOps](#create-alertops-test-user)** — aby mieć w aplikacji AlertOps odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji AlertOps, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **AlertOps** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Domena i adresy URL aplikacji AlertOps — informacje dotyczące logowania jednokrotnego](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.alertops.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.alertops.com/login.aspx`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Domena i adresy URL aplikacji AlertOps — informacje dotyczące logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta aplikacji AlertOps](mailto:support@alertops.com) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfigurowanie aplikacji AlertOps** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-alertops-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji AlertOps

1. W innym oknie przeglądarki zaloguj się do firmowej witryny aplikacji AlertOps jako administrator.

2. Kliknij pozycję **Account settings** (Ustawienia konta) w panelu nawigacji po lewej stronie.

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/configure1.png)

3. Na stronie **Subscription Settings** (Ustawienia subskrypcji) wybierz pozycję **SSO** (Logowanie jednokrotne), a następnie wykonaj następujące kroki:

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/configure2.png)

    a. Zaznacz pole wyboru **Użyj logowania jednokrotnego Sign-On (SSO)**.

    b. Z listy rozwijanej **SSO Provider** (Dostawca logowania jednokrotnego) wybierz pozycję **Azure Active Directory**.

    c. W polu tekstowym **Issuer URL** (Adres URL wystawcy) użyj wartości identyfikatora wybranego w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    d. W polu tekstowym **SAML endpoint URL** (Adres URL logowania) wklej wartość pola **Adres URL logowania** skopiowaną z witryny Azure Portal.

    e. W polu tekstowym **SLO endpoint URL** (Adres URL punktu końcowego logowania jednokrotnego) wklej wartość pola **Adres URL logowania** skopiowaną z witryny Azure Portal.

    f. Z listy rozwijanej **SAML Signature Algorithm** (Algorytm podpisu SAML) wybierz pozycję **SHA256**.

    g. Otwórz pobrany plik Certificate(Base64) w Notatniku. Skopiuj jego zawartość do schowka, a następnie wklej go w polu tekstowym X.509 Certificate (Certyfikat X.509).

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

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji AlertOps.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **AlertOps**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **AlertOps**.

    ![Link do aplikacji AlertOps na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-alertops-test-user"></a>Tworzenie użytkownika testowego aplikacji AlertOps

1. W innym oknie przeglądarki zaloguj się do firmowej witryny aplikacji AlertOps jako administrator.

2. Kliknij pozycję **Users** (Użytkownicy) na panelu nawigacyjnym po lewej stronie.

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/user1.png)

3. Wybierz pozycję **Add User** (Dodaj użytkownika).

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/user2.png)

4. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/user3.png)

    a. W polu tekstowym **Login User Name** (Nazwa użytkownika logowania) wprowadź nazwę użytkownika, na przykład **Brittasimon**.

    b. W **oficjalne wiadomości E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **Brittasimon\@contoso.com**.

    c. W polu tekstowym **First Name** (Imię) wpisz imię użytkownika, na przykład **Britta**.

    d. W polu tekstowym **Last Name** (Nazwisko) wprowadź nazwisko użytkownika, na przykład **Simon**.

    e. Wybierz wartość z listy rozwijanej **Type** (Typ) zgodnie z wymaganiami w Twojej organizacji.

    f. Wybierz wartość z listy rozwijanej **Role** (Rola) zgodnie z wymaganiami w Twojej organizacji.

    g. Wybierz pozycję **Dodaj**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka AlertOps w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji AlertOps, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
