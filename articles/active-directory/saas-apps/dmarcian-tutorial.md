---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją dmarcian | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c24cbf8ad21c7dd5875a71532a5278e313774e66
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60280658"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją dmarcian

Z tego samouczka dowiesz się, jak zintegrować aplikację dmarcian z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji dmarcian z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do aplikacji dmarcian.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji dmarcian (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją dmarcian potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji dm z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja dmarcian obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług** oraz **dostawcę tożsamości**

## <a name="adding-dmarcian-from-the-gallery"></a>Dodawanie aplikacji dmarcian z galerii

Aby skonfigurować integrację aplikacji dmarcian z usługą Azure AD, musisz dodać aplikację dmarcian z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację dmarcian z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **dmarcian**, wybierz pozycję **dmarcian** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja dmarcian na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją dmarcian, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD a powiązanym użytkownikiem aplikacji dmarcian.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją dmarcian, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji dmarcian](#configure-dmarcian-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji dmarcian](#create-dmarcian-test-user)**  — aby zapewnić odpowiednik użytkownika Britta Simon w aplikacji dmarcian powiązany z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z aplikacją dmarcian, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **dmarcian** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Domena i adresy URL aplikacji dmarcian — informacje dotyczące logowania jednokrotnego](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Domena i adresy URL aplikacji dmarcian — informacje dotyczące logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca:
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizujesz te wartości, używając rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania, co zostało opisane w dalszej części tego samouczka. 

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-dmarcian-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji dmarcian

1. W innym oknie przeglądarki internetowej zaloguj się do aplikacji dmarcian jako administrator zabezpieczeń.

2. Kliknij pozycję **Profile** (Profil) w prawym górnym rogu, a następnie przejdź do obszaru **Preferences** (Preferencje).

    ![Preferencje](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

3. Przewiń w dół i kliknij sekcję **Single Sign-On** (Logowanie jednokrotne), a następnie kliknij przycisk **Configure** (Konfiguruj).

    ![Logowanie jednokrotne](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

4. Na stronie **SAML Single Sign-On** (Logowanie jednokrotne języka SAML) ustaw pozycję **Status** (Stan) na **Enabled** (Włączono) i wykonaj następujące kroki:

    ![Uwierzytelnianie](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * W sekcji **Add dmarcian to your Identity Provider** (Dodawanie aplikacji dmarcian do dostawcy tożsamości) kliknij pozycję **COPY** (Kopiuj), aby skopiować **adres URL usługi Assertion Consumer Service** dla wystąpienia i wkleić go w polu tekstowym **Adres URL odpowiedzi** w **sekcji Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    * W sekcji **Add dmarcian to your Identity Provider** (Dodawanie aplikacji dmarcian do dostawcy tożsamości) kliknij pozycję **COPY** (Kopiuj), aby skopiować **identyfikator jednostki** dla wystąpienia i wkleić go w polu tekstowym **Identyfikator** w **sekcji Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    * W sekcji **Set up Authentication** (Konfigurowanie uwierzytelniania) w polu tekstowym **Identity Provider Metadata** (Metadane dostawcy tożsamości) wklej **adres URL metadanych federacyjnych aplikacji** skopiowany z witryny Azure Portal.

    * W sekcji **Set up Authentication** (Konfigurowanie uwierzytelniania) w polu tekstowym **Attribute Statements** (Instrukcje atrybutów) wklej adres URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * W sekcji **Set up Login URL** (Konfigurowanie adresu URL logowania) skopiuj **adres URL logowania** dla wystąpienia i wklej go w polu tekstowym **Adres URL logowania** w **sekcji Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

        > [!Note]
        > Możesz zmodyfikować **adres URL logowania** zgodnie z zasadami Twojej organizacji.

    * Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji dmarcian.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **dmarcian**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **dmarcian**.

    ![Link do aplikacji dmarcian na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-dmarcian-test-user"></a>Tworzenie użytkownika testowego aplikacji dmarcian

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji dmarcian, należy ich aprowizować w tej aplikacji. W aplikacji dmarcian aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do aplikacji dmarcian jako administrator zabezpieczeń.

2. Kliknij pozycję **Profile** (Profil) w górnym prawym rogu i przejdź do obszaru **Manage Users** (Zarządzanie użytkownikami).

    ![Użytkownik](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Po prawej stronie sekcji **SSO Users** (Użytkownicy logowania jednokrotnego) kliknij pozycję **Add New User** (Dodaj nowego użytkownika).

    ![Dodawanie użytkownika](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. W wyskakującym okienku **Add New User** (Dodawanie nowego użytkownika) wykonaj następujące kroki:

    ![Nowy użytkownik](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. W **nowy adres E-mail użytkownika** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon\@contoso.com**.

    b. Jeśli chcesz nadać użytkownikowi uprawnienia administratora, zaznacz pole **Make User an Admin** (Oznacz użytkownika jako administratora).

    c. Kliknij pozycję **Add User** (Dodaj użytkownika).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka dmarcian w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji dmarcian, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

