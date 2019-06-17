---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Clarizen | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88f42a849d5c483feb9325072fd65769839672e4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105383"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją Clarizen

Z tego samouczka dowiesz się, jak zintegrować aplikację Clarizen z usługą Azure Active Directory (Azure AD).
Integrowanie aplikacji Clarizen z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji Clarizen.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Clarizen (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Clarizen, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Clarizen z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Clarizen obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

## <a name="adding-clarizen-from-the-gallery"></a>Dodawanie aplikacji Clarizen z galerii

Aby skonfigurować integrację aplikacji Clarizen z usługą Azure AD, musisz dodać aplikację Clarizen z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Clarizen z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Clarizen**, wybierz pozycję **Clarizen** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Clarizen na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Clarizen, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Clarizen.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Clarizen, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Clarizen](#configure-clarizen-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji Clarizen](#create-clarizen-test-user)** — aby mieć w aplikacji Clarizen odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Clarizen, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Clarizen** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Informacje o domenie i adresach URL aplikacji Clarizen na potrzeby logowania jednokrotnego](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz wartość: `Clarizen`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Należy użyć faktycznego identyfikatora i adresu URL odpowiedzi. Tutaj sugerujemy użycie unikatowej wartości ciągu jako identyfikatora. Aby uzyskać faktyczne wartości, skontaktuj się z [zespołem pomocy technicznej aplikacji Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Skonfiguruj aplikację Clarizen** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-clarizen-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Clarizen

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji Clarizen jako administrator.

1. Kliknij swoją nazwę użytkownika, a następnie kliknij pozycję **Settings** (Ustawienia).

    ![Klikanie pozycji „Settings” (Ustawienia) w obszarze „Twoja nazwa użytkownika](./media/clarizen-tutorial/tutorial_clarizen_001.png "Settings (Ustawienia)")”

1. Kliknij kartę **Global Settings** (Ustawienia globalne). Następnie obok pozycji **Federated Authentication** (Uwierzytelnianie federacyjne) kliknij pozycję **Edit** (Edytuj).

    ![Karta „Global Settings” (Ustawienia globalne)](./media/clarizen-tutorial/tutorial_clarizen_002.png "Global Settings (Ustawienia globalne)")

1. W oknie dialogowym **Federated Authentication** (Uwierzytelnianie federacyjne) wykonaj następujące kroki:

    ![Okno dialogowe „Federated Authentication” (Uwierzytelnianie federacyjne)](./media/clarizen-tutorial/tutorial_clarizen_003.png "Federated Authentication (Uwierzytelnianie federacyjne)")

    a. Wybierz pozycję **Enable Federated Authentication** (Włącz uwierzytelnianie federacyjne).

    b. Kliknij pozycję **Upload** (Przekaż), aby przekazać pobrany certyfikat.

    c. W polu tekstowym **Sign-in URL** (Adres URL logowania) wprowadź wartość**adresu URL logowania** z okna konfiguracji aplikacji usługi Azure AD.

    d. W polu tekstowym **Sign-in URL** (Adres URL wylogowywania) wprowadź wartość**adresu URL wylogowywania** z okna konfiguracji aplikacji usługi Azure AD.

    e. Wybierz pozycję **Use POST** (Użyj metody POST).

    f. Kliknij pozycję **Zapisz**.

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

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Clarizen.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Clarizen**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Clarizen**.

    ![Link aplikacji Clarizen na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-clarizen-test-user"></a>Tworzenie użytkownika testowego aplikacji Clarizen

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji Clarizen.

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

Aby umożliwić użytkownikom usługi Azure AD logowanie do aplikacji Clarizen, należy zaaprowizować konta użytkowników. W przypadku aplikacji Clarizen aprowizacja jest wykonywana ręcznie.

1. Zaloguj się do firmowej witryny aplikacji Clarizen jako administrator.

2. Kliknij kartę **People** (Osoby).

    ![Klikanie karty „People” (Osoby)](./media/clarizen-tutorial/create_aaduser_001.png "People (Osoby)")

3. Kliknij przycisk **Invite User** (Zaproś użytkownika).

    ![Przycisk „Invite User” (Zaproś użytkownika)](./media/clarizen-tutorial/create_aaduser_002.png "Zapraszanie użytkowników")

1. W oknie dialogowym **Invite people** (Zapraszanie osób) wykonaj następujące czynności:

    ![Okno dialogowe „Invite People” (Zapraszanie osób)](./media/clarizen-tutorial/create_aaduser_003.png "Invite People (Zapraszanie osób)")

    a. W polu **Email** (Adres e-mail) wpisz adres e-mail konta użytkownika Britta Simon.

    b. Kliknij pozycję **Invite** (Zaproś).

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem, którego użyje w celu potwierdzenia konta, zanim stanie się ono aktywne.


### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Clarizen w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Clarizen, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
