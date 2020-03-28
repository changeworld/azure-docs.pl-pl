---
title: 'Samouczek: Integracja usługi Azure Active Directory z N2F — raporty z wydatków | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a raportami wydatków N2F.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 11f5e2f7763008c3af09c5367d90265af6a9653a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161289"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Samouczek: Integracja usługi Azure Active Directory z N2F — raporty z wydatków

W tym samouczku dowiesz się, jak zintegrować N2F — raporty wydatków z usługą Azure Active Directory (Azure AD).
Integracja N2F — raporty wydatków z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do N2F — raporty wydatków.
* Można włączyć użytkowników, aby automatycznie zalogować się do N2F — raporty wydatków (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z raportami wydatków N2F — potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* N2F — raporty wydatków z włączoną subskrypcją logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* N2F - Raporty wydatków obsługuje **SP** i **IDP** zainicjowane SSO

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Dodawanie N2F - Raporty z wydatków z galerii

Aby skonfigurować integrację N2F — Raporty wydatków do usługi Azure AD, należy dodać N2F — raporty wydatków z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać N2F - Raporty z wydatków z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **N2F - Raporty wydatków**, wybierz **N2F - Raporty wydatków** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![N2F - Raporty z wydatków na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą N2F — raporty wydatków na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w U2F — raporty wydatków.

Aby skonfigurować i przetestować usługę Azure AD logowania jednokrotnego za pomocą N2F — raporty wydatków, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie N2F — raporty wydatków Logowania jednokrotnego](#configure-n2f---expense-reports-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie N2F — raporty wydatków użytkownika testowego](#create-n2f---expense-reports-test-user)** — mieć odpowiednik Britta Simon w N2F — raporty wydatków, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą raportów n2f — raporty z wydatków, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **N2F — Raporty wydatków** wybierz opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez usługę IDP,** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![N2F — raporty o wydatkach Informacje o logowanie do domeny i adresów URL](common/preintegrated.png)

5. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    ![N2F — raporty o wydatkach Informacje o logowanie do domeny i adresów URL](common/metadata-upload-additional-signon.png)

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://www.n2f.com/app/`

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

7. W sekcji **Konfigurowanie myPolicies** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Konfigurowanie N2F — raporty wydatków Logowanie jednokrotne

1. W innym oknie przeglądarki internetowej zaloguj się do witryny N2F — raporty wydatków firmy jako administrator.

2. Kliknij **ustawienia,** a następnie wybierz pozycję **Ustawienia zaliczki** z listy rozwijanej.

    ![N2F - Konfiguracja raportów z wydatków](./media/n2f-expensereports-tutorial/configure1.png)

3. Wybierz kartę **Ustawienia konta.**

    ![N2F - Konfiguracja raportów z wydatków](./media/n2f-expensereports-tutorial/configure2.png)

4. Wybierz **pozycję Uwierzytelnianie,** a następnie wybierz pozycję + Dodaj kartę **metody uwierzytelniania.**

    ![N2F - Konfiguracja raportów z wydatków](./media/n2f-expensereports-tutorial/configure3.png)

5. Wybierz **SAML Microsoft Office 365** jako metodę uwierzytelniania.

    ![N2F - Konfiguracja raportów z wydatków](./media/n2f-expensereports-tutorial/configure4.png)

6. W sekcji **Metoda uwierzytelniania** wykonaj następujące czynności:

    ![N2F - Konfiguracja raportów z wydatków](./media/n2f-expensereports-tutorial/configure5.png)

    a. W polu tekstowym **identyfikator jednostki** wklej wartość **identyfikatora usługi Azure AD,** która została skopiowana z witryny Azure portal.

    b. W polu tekstowym **adres URL metadanych** wklej wartość **adresu URL metadanych federacji aplikacji,** która została skopiowana z witryny Azure portal.

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
  
    b. W polu **Nazwa użytkownika** wpisz **\@brittasimon yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, przyznając dostęp do N2F — raporty wydatków.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz **pozycję N2F - Raporty wydatków**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pozycję N2F - Raporty wydatków**.

    ![N2F — łącze Raporty wydatków na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-n2f---expense-reports-test-user"></a>Tworzenie N2F — raporty wydatków użytkownika testowego

Aby umożliwić użytkownikom usługi Azure AD zalogować się do N2F — raporty z wydatków, muszą być aprowizowane do N2F — raporty wydatków. W przypadku N2F — raporty wydatków inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny N2F - Raporty wydatków firmy jako administrator.

2. Kliknij **ustawienia,** a następnie wybierz pozycję **Ustawienia zaliczki** z listy rozwijanej.

    ![N2F - Użytkownik dodawania wydatków](./media/n2f-expensereports-tutorial/configure1.png)

3. Karta **Użytkownicy** z lewego panelu nawigacyjnego.

    ![N2F - Konfiguracja raportów z wydatków](./media/n2f-expensereports-tutorial/user1.png)

4. Wybierz + Nowa karta **użytkownika.**

    ![N2F - Konfiguracja raportów z wydatków](./media/n2f-expensereports-tutorial/user2.png)

5. W sekcji **Użytkownik** wykonaj następujące czynności:

    ![N2F - Konfiguracja raportów z wydatków](./media/n2f-expensereports-tutorial/user3.png)

    a. W polu **tekstowym Adres e-mail** wprowadź adres e-mail użytkownika, takiego jak **brittasimon\@contoso.com**.

    b. W polu tekstowym **Imię** wprowadź imię użytkownika, takiego jak **Britta**.

    d. W polu tekstowym **Name** (Nazwa) wprowadź nazwę użytkownika, na przykład **BrittaSimon**.

    d. Wybierz **pozycję Rola, Menedżer bezpośredni (N+1)** i **Podział** zgodnie z wymaganiami organizacji.

    e. Kliknij **przycisk Sprawdź poprawność i wyślij zaproszenie**.

    > [!NOTE]
    > Jeśli masz jakiekolwiek problemy podczas dodawania użytkownika, skontaktuj się z [zespołem pomocy technicznej N2F - Raporty wydatków](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka N2F - Raporty wydatków w Panelu dostępu należy automatycznie zalogować się do N2F - Raporty wydatków, dla których skonfigurowano logującą się logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

