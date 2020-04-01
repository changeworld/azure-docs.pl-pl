---
title: 'Samouczek: Integracja usługi Azure Active Directory z peakonem | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: b093a26848701254ad674081037c266f1fb012b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094722"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Samouczek: Integracja usługi Azure Active Directory z peakonem

W tym samouczku dowiesz się, jak zintegrować Peakon z usługą Azure Active Directory (Azure AD).
Integracja Peakon z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do Peakon.
* Można włączyć użytkowników, aby automatycznie zalogować się do Peakon (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Peakon, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego Peakon

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Peakon obsługuje **sp** i **IDP** zainicjowane SSO

## <a name="adding-peakon-from-the-gallery"></a>Dodawanie Peakon z galerii

Aby skonfigurować integrację Peakon z usługą Azure AD, należy dodać Peakon z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Peakon z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Peakon**, wybierz **Peakon** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Peakon na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Peakon na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Peakon.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą Peakon, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne Peakon](#configure-peakon-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Peakon](#create-peakon-test-user)** — aby mieć odpowiednik Britta Simon w Peakon, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą peakonu, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Peakon** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    ![Informacje o logach jednokrotnych w domenie Peakon i adresach URL](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://app.peakon.com/saml/<companyid>/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://app.peakon.com/saml/<companyid>/assert`

5. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    ![Informacje o logach jednokrotnych w domenie Peakon i adresach URL](common/metadata-upload-additional-signon.png)

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://app.peakon.com/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości za pomocą rzeczywistego adresu URL identyfikatora i odpowiedzi, który został wyjaśniony w dalszej części samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (nieprzetworzony)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

7. W sekcji **Konfigurowanie Peakon** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-peakon-single-sign-on"></a>Konfigurowanie rejestracji jednokrotnej Peakon

1. W innym oknie przeglądarki internetowej zaloguj się do Peakon jako administrator.

2. Na pasku menu po lewej stronie kliknij pozycję **Konfiguracja,** a następnie przejdź do **pozycji Integracje**.

    ![The Config](./media/peakon-tutorial/tutorial_peakon_config.png)

3. Na stronie **Integracje** kliknij **opcję Logowanie jednokrotne**.

    ![Singiel](./media/peakon-tutorial/tutorial_peakon_single.png)

4. W sekcji **Logowanie jednokrotne** kliknij pozycję **Włącz**.

    ![Włączenie](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. W sekcji **Logowanie jednokrotne dla pracowników korzystających z saml** należy wykonać następujące kroki:

    ![Saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. In the **SSO Login URL** textbox, paste the value of **Login URL**, which you have copied from the Azure portal.

    b. W polu tekstowym **adresu URL logowania** logowania logowania wklej wartość adresu URL **wylogowania,** który został skopiowany z witryny Azure portal.

    d. Kliknij **pozycję Wybierz plik,** aby przekazać certyfikat pobrany z witryny Azure portal do pola Certyfikat.

    d. Kliknij **ikonę,** aby skopiować **identyfikator jednostki** i wkleić w obszarze tekstowym **identyfikator** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    e. Kliknij **ikonę,** aby skopiować **adres URL odpowiedzi (ACS)** i wkleić w skrzynce tekstowej **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    f. Kliknij **przycisk Zapisz**

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, przyznając dostęp do Peakon.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz **pozycję Peakon**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **peakon**.

    ![Łącze Peakon na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-peakon-test-user"></a>Utwórz użytkownika testowego Peakon

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Peakon, muszą one być aprowidywne w Peakon.  
W przypadku Peakon inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Peakon jako administrator.

2. Na pasku menu po lewej stronie strony kliknij pozycję **Konfiguracja,** a następnie przejdź do **pozycji Pracownicy**.

    ![Pracownik](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. W prawym górnym rogu strony kliknij pozycję **Dodaj pracownika**.

      ![Dodaj pracownika](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Na stronie Okna dialogowego **Nowy pracownik** wykonaj następujące czynności:

     ![Nowy pracownik](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. W polach **tekstowych Nazwa** wpisz imię jako **Britta** i nazwisko jako **simon**.

    b. W polu **tekstowym Poczta e-mail** wpisz adres e-mail, taki jak **Brittasimon\@contoso.com**.

    d. Kliknij **pozycję Utwórz pracownika**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Peakon w Panelu dostępu należy automatycznie zalogować się do Peakon, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

