---
title: 'Samouczek: Integracja usługi Azure Active Directory z rozwiązaniem BlueJeans | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i rozwiązaniem BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2830e95f042d5c83d91d6c026d2fa2e70eb3e3b2
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65601645"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Samouczek: integracja usługi Azure Active Directory z rozwiązaniem BlueJeans

Z tego samouczka dowiesz się, jak zintegrować rozwiązanie BlueJeans z usługą Azure Active Directory (Azure AD).
Integracja rozwiązania BlueJeans z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować za pomocą usługi Azure AD, kto ma dostęp do rozwiązania BlueJeans.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do rozwiązania BlueJeans (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z rozwiązaniem BlueJeans, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja rozwiązania BlueJeans z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązanie BlueJeans obsługuje logowanie jednokrotne inicjowane przez **SP**

* Rozwiązanie BlueJeans obsługuje [**automatyczną** aprowizację użytkowników](bluejeans-provisioning-tutorial.md)

## <a name="adding-bluejeans-from-the-gallery"></a>Dodawanie rozwiązania BlueJeans z galerii

Aby skonfigurować integrację rozwiązania BlueJeans z usługą Azure AD, musisz dodać rozwiązanie BlueJeans z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać rozwiązanie BlueJeans z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **BlueJeans**, wybierz pozycję **BlueJeans** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Rozwiązanie BlueJeans na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z rozwiązaniem BlueJeans, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem rozwiązania BlueJeans.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z rozwiązaniem BlueJeans, należy ukończyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w rozwiązaniu BlueJeans](#configure-bluejeans-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego rozwiązania BlueJeans](#create-bluejeans-test-user)** — aby mieć w rozwiązaniu BlueJeans odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w rozwiązaniu BlueJeans, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **BlueJeans** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](media/bluejeans-tutorial/edit-urls-bluejeans.png)

4. W **podstawową konfigurację protokołu SAML** okno dialogowe, wprowadź następujące wartości:

    ![Informacje o domenie i adresach URL logowania jednokrotnego rozwiązania BlueJeans](media/bluejeans-tutorial/tutorial_bluejeans-basic-configuration.png)

   - W polu tekstowym **Identyfikator** wpisz następujący tekst: `http://samlsp.bluejeans.com`
    
   - W **adres URL logowania** pole tekstowe, wpisz adres URL strony docelowej, które zostały przekazane użytkownikowi przez BlueJeans (Aby uzyskać tę wartość, możesz skontaktować się ze [zespołem pomocy technicznej klienta BlueJeans](https://support.bluejeans.com/contact)): `https://<companyname>.bluejeans.com`
    
   - Kliknij pozycję **Zapisz**.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Skonfiguruj aplikację BlueJeans** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-bluejeans-single-sign-on"></a>Konfigurowanie logowania jednokrotnego rozwiązania BlueJeans

1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **BlueJeans** witryny firmy jako administrator.

2. Wybierz pozycję **ADMIN (ADMINISTRATOR) \> GROUP SETTINGS (USTAWIENIA GRUPY) \> SECURITY (ZABEZPIECZENIA)**.

    ![Administrator](./media/bluejeans-tutorial/ic785868.png "Administrator")

3. W sekcji **SECURITY** (ZABEZPIECZENIA) wykonaj następujące czynności:

    ![Logowanie jednokrotne SAML](./media/bluejeans-tutorial/ic785869.png "Logowanie jednokrotne SAML")

    a. Wybierz pozycję **SAML Single Sign On** (Logowanie jednokrotne SAML).

    b. Wybierz pozycję **Enable automatic provisioning** (Włącz automatyczną aprowizację).

4. Przejdź dalej, wykonując następujące czynności:

    ![Ścieżka certyfikatu](./media/bluejeans-tutorial/ic785870.png "Ścieżka certyfikatu")

    a. Kliknij pozycję **Choose File** (Wybierz plik), aby przekazać certyfikat zakodowany w formacie base-64, pobrany wcześniej z witryny Azure Portal.

    b. W polu tekstowym **Adres URL logowania** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    c. W polu tekstowym **Password Change URL** (Adres URL zmiany hasła) wklej wartość **adresu URL zmiany hasła** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

5. Przejdź dalej, wykonując następujące czynności:

    ![Zapisywanie zmian](./media/bluejeans-tutorial/ic785874.png "Zapisywanie zmian")

    a. W **identyfikator użytkownika** polu tekstowym wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. W polu tekstowym **Email** (Adres e-mail) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Kliknij przycisk **SAVE CHANGES** (ZAPISZ ZMIANY).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon\@yourcompanydomain.extension`. Na przykład BrittaSimon@contoso.com.

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do rozwiązania BlueJeans.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **BlueJeans**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **BlueJeans**.

    ![Link rozwiązania BlueJeans na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-bluejeans-test-user"></a>Tworzenie użytkownika testowego rozwiązania BlueJeans

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w rozwiązaniu BlueJeans. Rozwiązanie BlueJeans obsługuje automatyczną aprowizację użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznej aprowizacji użytkowników można znaleźć [tutaj](bluejeans-provisioning-tutorial.md).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **BlueJeans** witryny firmy jako administrator.

2. Wybierz pozycję **ADMIN (ADMINISTRATOR) \> MANAGE USERS (ZARZĄDZAJ UŻYTKOWNIKAMI) \> ADD USER (DODAJ UŻYTKOWNIKA)**.

    ![Administrator](./media/bluejeans-tutorial/ic785877.png "Administrator")

    > [!IMPORTANT]
    > Karta **ADD USER** (DODAJ UŻYTKOWNIKA) jest dostępna tylko wtedy, gdy na **karcie SECUTIRY** (ZABEZPIECZENIA) nie zaznaczono pola wyboru **Enable automatic provisioning** (Włącz automatyczną aprowizację).

3. W sekcji **ADD USER** (DODAJ UŻYTKOWNIKA) wykonaj następujące czynności:

    ![Dodaj użytkownika](./media/bluejeans-tutorial/ic785886.png "Dodaj użytkownika")

    a. W polu tekstowym **First Name** (Imię) wprowadź imię użytkownika, na przykład **Britta**.

    b. W polu tekstowym **Last name** (Nazwisko) wprowadź nazwisko użytkownika, na przykład **Simon**.

    c. W polu tekstowym **Pick a BlueJeans Username** (Wybierz nazwę użytkownika BlueJeans) wprowadź nazwę użytkownika, na przykład **Brittasimon**

    d. W polu tekstowym **Create a Password** (Utwórz hasło) wprowadź hasło.

    e. W polu tekstowym **Company** (Firma) wprowadź nazwę Twojej firmy.

    f. W **adres E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak `brittasimon\@contoso.com`.

    g. W polu tekstowym **Create a BlueJeans Meeting I.D** (Utwórz identyfikator spotkania BlueJeans) wprowadź swój identyfikator spotkania.

    h. W polu tekstowym **Pick a Moderator Passcode** (Wybierz kod dostępu moderatora) wprowadź kod dostępu.

    i. Kliknij pozycję **CONTINUE** (KONTYNUUJ).

    ![Dodawanie użytkownika](./media/bluejeans-tutorial/ic785887.png "Dodawanie użytkownika")

    J. Kliknij przycisk **ADD USER** (DODAJ UŻYTKOWNIKA).

> [!NOTE]
> Aby aprowizować konta użytkowników usługi Azure AD, można użyć jakichkolwiek innych narzędzi tworzenia konta użytkownika lub interfejsów API dostarczonych przez firmę BlueJeans.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka BlueJeans w panelu dostępu powinno nastąpić automatyczne zalogowanie do rozwiązania BlueJeans, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
