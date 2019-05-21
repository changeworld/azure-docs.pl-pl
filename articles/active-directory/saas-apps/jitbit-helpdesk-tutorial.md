---
title: 'Samouczek: Integracja usługi Azure Active Directory z pomocą techniczną Jitbit | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Jitbit działu pomocy technicznej.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 851b28d10bdf0b2df67e1c0782a683e790b711bc
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65898003"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Samouczek: Integracja usługi Azure Active Directory z Jitbit działu pomocy technicznej

W tym samouczku dowiesz się, jak zintegrować Jitbit działu pomocy technicznej za pomocą usługi Azure Active Directory (Azure AD).
Integrowanie pomocy Jitbit z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do pomocy technicznej Jitbit.
* Użytkownikom można automatycznie zalogowany do Jitbit działu pomocy technicznej (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pomocą techniczną Jitbit, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Pomoc techniczna Jitbit logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Pomoc techniczna Jitbit obsługuje **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Dodawanie pomocy technicznej Jitbit z galerii

Aby skonfigurować integrację Jitbit pomoc techniczna do usługi Azure AD, należy dodać Jitbit działu pomocy technicznej z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Jitbit działu pomocy technicznej z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Jitbit działu pomocy technicznej**, wybierz opcję **Jitbit działu pomocy technicznej** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Jitbit działu pomocy technicznej na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i test usługi Azure AD logowanie jednokrotne za pomocą Jitbit działu pomocy technicznej w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Jitbit działu pomocy technicznej musi zostać nawiązane.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Jitbit działu pomocy technicznej, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Jitbit pomocy logowania jednokrotnego](#configure-jitbit-helpdesk-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego pomocy Jitbit](#create-jitbit-helpdesk-test-user)**  — aby odpowiednikiem Britta Simon w pomocy Jitbit, w którym jest połączony z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Jitbit działu pomocy technicznej, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **pomocy Jitbit** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Jitbit pomocy technicznej domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta pomoc techniczna Jitbit](https://www.jitbit.com/support/) aby zyskać tę wartość.

    b. W **identyfikator jednostki** pole tekstowe, wpisz adres URL, jako pokazano poniżej: `https://www.jitbit.com/web-helpdesk/`

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. W **Konfigurowanie pomocy Jitbit** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Konfigurowanie pomocy Jitbit logowania jednokrotnego

1. W oknie przeglądarki internetowej innej Zaloguj się w witrynie Jitbit działu pomocy technicznej firmy jako administrator.

1. Na pasku narzędzi u góry kliknij **administracji**.

    ![Administracja](./media/jitbit-helpdesk-tutorial/ic777681.png "Administracja")

1. Kliknij przycisk **ustawienia ogólne**.

    ![Użytkownikom, firmom i uprawnienia](./media/jitbit-helpdesk-tutorial/ic777680.png "użytkownikom, firmom i uprawnienia")

1. W **ustawienia uwierzytelniania** konfiguracji sekcji, wykonaj następujące czynności:

    ![Ustawienia uwierzytelniania](./media/jitbit-helpdesk-tutorial/ic777683.png "ustawienia uwierzytelniania")

    a. Wybierz **Włącz SAML 2.0 pojedynczego logowania**, aby zalogować się przy użyciu pojedynczego logowania jednokrotnego (SSO) przy użyciu **OneLogin**.

    b. W **adresu URL punktu końcowego** pola tekstowego, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.

    c. Otwórz swoje **base-64** zakodowane certyfikatów w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikat X.509** textbox

    d. Kliknij przycisk **Zapisz zmiany**.

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

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do działu pomocy technicznej Jitbit za pomocą platformy Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **pomocy Jitbit**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pomocy Jitbit**.

    ![Link do pomocy technicznej Jitbit na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-jitbit-helpdesk-test-user"></a>Tworzenie użytkownika testowego Jitbit działu pomocy technicznej

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do działu pomocy technicznej Jitbit, musi być obsługiwana w Jitbit działu pomocy technicznej. W przypadku pomocy Jitbit aprowizacji to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Twojej **pomocy Jitbit** dzierżawy.

1. W menu u góry kliknij **administracji**.

    ![Administracja](./media/jitbit-helpdesk-tutorial/ic777681.png "Administracja")

1. Kliknij przycisk **użytkownikom, firmom i uprawnienia**.

    ![Użytkownikom, firmom i uprawnienia](./media/jitbit-helpdesk-tutorial/ic777682.png "użytkownikom, firmom i uprawnienia")

1. Kliknij pozycję **Dodaj użytkownika**.

    ![Dodawanie użytkownika](./media/jitbit-helpdesk-tutorial/ic777685.png "Dodawanie użytkownika")

1. W sekcji Tworzenie wpisz dane konta usługi Azure AD, który chcesz aprowizowania wykonaj następujące czynności:

    ![Tworzenie](./media/jitbit-helpdesk-tutorial/ic777686.png "tworzenie")

   a. W **Username** polu tekstowym wpisz nazwę użytkownika użytkownika, takie jak **BrittaSimon**.

   b. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak **BrittaSimon@contoso.com**.

   c. W polu tekstowym **First Name** (Imię) wpisz imię użytkownika, takie jak **Britta**.

   d. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak **Simon**.

   e. Kliknij pozycję **Utwórz**.

> [!NOTE]
> Aprowizuj konta użytkownika usługi Azure AD, można użyć innych narzędzi do tworzenia konta użytkownika Jitbit działu pomocy technicznej lub interfejsów API dostarczonych przez Jitbit działu pomocy technicznej.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Jitbit działu pomocy technicznej w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Jitbit działu pomocy technicznej, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
