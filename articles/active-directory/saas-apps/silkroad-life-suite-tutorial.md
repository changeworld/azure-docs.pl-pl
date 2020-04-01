---
title: 'Samouczek: Integracja usługi Azure Active Directory z pakietem SilkRoad Life Suite | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a pakietem SilkRoad Life Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 63165da69815c77afb8692e1e68c1710beb8df8c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090820"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Samouczek: Integracja usługi Azure Active Directory z pakietem SilkRoad Life Suite

W tym samouczku dowiesz się, jak zintegrować pakiet SilkRoad Life Suite z usługą Azure Active Directory (Azure AD).
Integracja pakietu SilkRoad Life Suite z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do pakietu SilkRoad Life Suite.
* Możesz włączyć automatyczne logowanie użytkowników do pakietu SilkRoad Life Suite (logowanie jednokrotne) za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem SilkRoad Life Suite, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z funkcją logowania jednokrotnego SilkRoad Life Suite

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SilkRoad Life Suite obsługuje sso inicjowane przez **SP**

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Dodawanie pakietu SilkRoad Life Suite z galerii

Aby skonfigurować integrację pakietu SilkRoad Life Suite z usługą Azure AD, należy dodać pakiet SilkRoad Life Suite z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać pakiet SilkRoad Life Suite z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SilkRoad Life Suite**, wybierz **SilkRoad Life Suite** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![SilkRoad Life Suite na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z SilkRoad Life Suite na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze SilkRoad Life Suite.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą pakietu SilkRoad Life Suite, należy wykonać następujące elementy konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne silkroad life suite](#configure-silkroad-life-suite-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego SilkRoad Life Suite](#create-silkroad-life-suite-test-user)** — aby mieć odpowiednik Britta Simon w usłudze SilkRoad Life Suite, która jest połączona z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą pakietu SilkRoad Life Suite, wykonaj następujące czynności:

1. W [portalu Azure](https://portal.azure.com/)na stronie integracji aplikacji **SilkRoad Life Suite** wybierz opcję **Logowanie jednokrotne.**

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    > [!NOTE]
    > Otrzymasz **plik metadanych dostawcy usług** wyjaśnione w dalszej części tego samouczka.

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![image](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![image](common/browse-upload-metadata.png)

    d. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatora** i **adresu URL odpowiedzi** są automatycznie wypełniane w sekcji Podstawowa konfiguracja SAML:

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > Jeśli wartości **Identyfikator** i **Adres URL odpowiedzi** nie zostaną automatycznie wypełnione, wpisz te wartości ręcznie zgodnie z wymaganiami.

    d. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://<subdomain>.silkroad-eng.com/Authentication/`

5. W sekcji **Podstawowa konfiguracja SAML,** jeśli nie masz **pliku metadanych dostawcy usług,** wykonaj następujące kroki:

    ![Informacje o domenie i adresach URL SilkRoad Life Suite](common/sp-identifier-reply.png)

    a. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://<subdomain>.silkroad-eng.com/Authentication/`

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP`|
    | `https://<subdomain>.silkroad.com/Authentication/SP`|

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/`|
    | `https://<subdomain>.silkroad.com/Authentication/`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta SilkRoad Life Suite,](https://www.silkroad.com/locations/) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Set up SilkRoad Life Suite** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Konfigurowanie logowania jednokrotnego pakietu SilkRoad Life Suite

1. Zaloguj się do witryny firmy SilkRoad jako administrator.

    > [!NOTE]
    > Aby uzyskać dostęp do aplikacji SilkRoad Life Suite Authentication do konfigurowania federacji za pomocą usługi Microsoft Azure AD, skontaktuj się z pomocą techniczną SilkRoad lub przedstawicielem silkroad services.

1. Przejdź do **witryny Usługodawca**, a następnie kliknij pozycję **Szczegóły federacji**.

    ![Logowanie jednokrotne usługi Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Kliknij **pozycję Pobierz metadane federacji,** a następnie zapisz plik metadanych na komputerze. Użyj pobranych metadanych federacji jako **pliku metadanych dostawcy usług** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    ![Logowanie jednokrotne usługi Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. W aplikacji **SilkRoad** kliknij pozycję **Źródła uwierzytelniania**.

    ![Logowanie jednokrotne usługi Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Kliknij **pozycję Dodaj źródło uwierzytelniania**.

    ![Logowanie jednokrotne usługi Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. W sekcji **Dodaj źródło uwierzytelniania** wykonaj następujące czynności:

    ![Logowanie jednokrotne usługi Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. W obszarze **Opcja 2 — plik metadanych**kliknij przycisk **Przeglądaj,** aby przekazać pobrany plik metadanych z witryny Azure portal.
  
    b. Kliknij **pozycję Utwórz dostawcę tożsamości przy użyciu danych pliku**.

1. W sekcji **Źródła uwierzytelniania** kliknij pozycję **Edytuj**.

    ![Logowanie jednokrotne usługi Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. W oknie dialogowym **Edytowanie źródła uwierzytelniania** wykonaj następujące czynności:

    ![Logowanie jednokrotne usługi Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Jako **włączone**, wybierz **tak**.

    b. W polu tekstowym **EntityId** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal.

    d. W polu **tekstowym Opis idp** wpisz opis konfiguracji (na przykład: *Azure AD SSO*).

    d. W polu tekstowym **Plik metadanych** przekaż plik **metadanych** pobrany z witryny Azure portal.
  
    e. W polach tekstowych **Nazwa dostawcy idp** wpisz nazwę specyficzną dla twojej konfiguracji (na przykład: *Azure SP*).
  
    f. W polu tekstowym **adresu URL usługi wylogowania** wklej wartość **adresu URL wylogowania** skopiowanego z witryny Azure portal.

    g. W polu **tekstowym adresu URL usługi logowania** wklej wartość adresu URL **logowania** skopiowanego z witryny Azure portal.

    h. Kliknij przycisk **Zapisz**.

1. Wyłącz wszystkie inne źródła uwierzytelniania.

    ![Logowanie jednokrotne usługi Azure AD](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

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

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do SilkRoad Life Suite.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz pakiet **SilkRoad Life Suite**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **silkroad life suite**.

    ![Link SilkRoad Life Suite na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-silkroad-life-suite-test-user"></a>Utwórz użytkownika testowego SilkRoad Life Suite

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w SilkRoad Life Suite. Współpracuj z [zespołem wsparcia klienta SilkRoad Life Suite,](https://www.silkroad.com/locations/) aby dodać użytkowników na platformie SilkRoad Life Suite. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SilkRoad Life Suite w Panelu dostępu należy automatycznie zalogować się do pakietu SilkRoad Life Suite, dla którego skonfigurowano logującą się do rejestru jednośmiękowego. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
