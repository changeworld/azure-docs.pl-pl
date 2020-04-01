---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Kantega SSO dla Bitbucket | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a sygosk typu Proso firmy Kantega dla bitbucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: b04b44c907e3210f3cc3975b36639f4fe275eef9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099207"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Kantega SSO dla Bitbucket

W tym samouczku dowiesz się, jak zintegrować usługę SSO kantega dla bitbucket z usługą Azure Active Directory (Azure AD).
Integracja usługi SSO firmy Kantega dla bitbucket z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do usługi Kantega SSO dla Bitbucket.
* Można włączyć użytkowników, aby automatycznie zalogować się do logowania jednokrotnego Kantega dla Bitbucket (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Kantega SSO dla bitbucket, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Kantega SSO dla subskrypcji z obsługą logowania jednokrotnego Bitbucket

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Kantega SSO dla Bitbucket obsługuje **SP i IDP** zainicjowane SSO

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Dodawanie SSO Kantega dla Bitbucket z galerii

Aby skonfigurować integrację usługi SSO kantega dla bitbucket do usługi Azure AD, należy dodać anso firmy Kantega dla Bitbucket z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać funkcję SSO Kantega dla Bitbucket z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Kantega SSO dla Bitbucket**, wybierz **Kantega SSO dla Bitbucket** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Kantega SSO dla Bitbucket na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Kantega SSO dla Bitbucket na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie AnSO firmy Kantega dla bitbucket.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego kantega dla bitbucket, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne Kantega dla logowania jednokrotnego Bitbucket](#configure-kantega-sso-for-bitbucket-single-sign-on)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz usługę SSO Kantega dla użytkownika testowego Bitbucket](#create-kantega-sso-for-bitbucket-test-user)** — aby mieć odpowiednik Britta Simon w Kantega SSO dla Bitbucket, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego kantega dla bitbucket, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/), na stronie rejestracji **logowania jednokrotnego kantega dla aplikacji Bitbucket** wybierz opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** Jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    ![Kantega SSO dla domeny Bitbucket i adresów URL informacje o logowaniach jednokrotnych](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    ![Kantega SSO dla domeny Bitbucket i adresów URL informacje o logowaniach jednokrotnych](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Wartości te są odbierane podczas konfiguracji wtyczki Bitbucket, która jest wyjaśniona w dalszej części samouczka.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie sso kantega dla Bitbucket** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-kantega-sso-for-bitbucket-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Kantega dla logowania jednokrotnego Bitbucket

1. W innym oknie przeglądarki internetowej zaloguj się do portalu administracyjnego Bitbucket jako administrator.

1. Kliknij trybikiem i kliknij **przycisk Znajdź nowe dodatki**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Szukaj **Kantega SSO dla Bitbucket SAML & Kerberos** i kliknij przycisk **Zainstaluj,** aby zainstalować nową wtyczkę SAML.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. Rozpocznie się instalacja wtyczki.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. Po zakończeniu instalacji. Kliknij przycisk **Zamknij**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon33.png)

1. Kliknij pozycję **Zarządzaj**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon34.png)

1. Kliknij pozycję **Configure** (Konfiguruj), aby skonfigurować nową wtyczkę.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. W sekcji **SAML.** Wybierz **usługę Azure Active Directory (Azure AD)** z listy rozwijanej Dodaj **dostawcę tożsamości.**

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Wybierz poziom subskrypcji jako **podstawowy**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. W sekcji **Właściwości aplikacji** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Skopiuj wartość **identyfikatora URI aplikacji** i użyj jej jako **identyfikatora, adresu URL odpowiedzi i adresu URL logowania w** sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    b. Kliknij przycisk **alej**.

1. W sekcji **Importowanie metadanych** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Wybierz **plik metadanych na moim komputerze**i przekaż plik metadanych, który został pobrany z witryny Azure portal.

    b. Kliknij przycisk **alej**.

1. W sekcji **Lokalizacja nazwy i syłowienia należy** wykonać następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Dodaj nazwę dostawcy tożsamości w pola tekstowym **nazwy dostawcy tożsamości** (np. usługi Azure AD).

    b. Kliknij przycisk **alej**.

1. Sprawdź certyfikat podpisywania i kliknij przycisk **Dalej**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. W sekcji **Konta użytkowników Bitbucket** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Wybierz **pozycję Utwórz użytkowników w katalogu wewnętrznym Bitbucket w razie potrzeby** i wprowadź odpowiednią nazwę grupy dla użytkowników (może to być wiele nie. grup oddzielonych przecinkiem).

    b. Kliknij przycisk **alej**.

1. Kliknij przycisk **Zakończ**.

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. W sekcji **Znane domeny dla usługi Azure AD** wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Wybierz **pozycję Znane domeny** z lewego panelu strony.

    b. Wprowadź nazwę domeny w polach **tekstowych Znane domeny.**

    d. Kliknij przycisk **Zapisz**.

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

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, udzielając dostępu do logowania jednokrotnego Kantega dla Bitbucket.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz pozycję **Kantega SSO dla Bitbucket**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pozycję Kantega SSO dla Bitbucket**.

    ![Link SSO Kantega dla Bitbucket na liście Aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Tworzenie usługi SSO Kantega dla użytkownika testowego Bitbucket

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Bitbucket, muszą one być aprowizowana do Bitbucket. W przypadku usługi SSO kantega dla Bitbucket inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Bitbucket jako administrator.

1. Kliknij ikonę ustawień.

    ![Dodawanie pracownika](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. W sekcji **Karta Administracja** kliknij pozycję **Użytkownicy**.

    ![Dodawanie pracownika](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Kliknij **pozycję Utwórz użytkownika**.

    ![Dodawanie pracownika](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. Na stronie okna dialogowego **Tworzenie użytkownika** wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. W polu tekstowym **Username** (Nazwa użytkownika) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    b. W polu tekstowym **Full Name** (Imię i nazwisko) wpisz imię i nazwisko użytkownika, np. Britta Simon.

    d. W polu tekstowym **Email address** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład Brittasimon@contoso.com.

    d. W polu tekstowym **Password** (Hasło) wpisz hasło użytkownika.

    e. W polach tekstowych **Potwierdź hasło** ponownie wejdź ponownie pod hasło użytkownika.

    f. Kliknij **pozycję Utwórz użytkownika**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SSO Kantega dla Bitbucket w panelu dostępu, powinieneś być automatycznie zalogowany do sytuatora SSO Kantega dla Bitbucket, dla którego skonfigurowano logującą się do rejestru jednoślikowego. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

