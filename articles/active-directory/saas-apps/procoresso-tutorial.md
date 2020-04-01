---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Procore SSO | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a logiem jednokrotnym procore.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6863a6b02e867afd732ce1662136051b8afec8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093659"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Procore SSO

W tym samouczku dowiesz się, jak zintegrować sygnowanie sygnowanie usługi Procore z usługą Azure Active Directory (Azure AD).
Integracja wpisu SSO procore z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do procore SSO.
* Można włączyć użytkowników do automatycznego logowania do Procore SSO (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Procore SSO, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego Procore

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Procore SSO obsługuje zainicjowane **przez IDP** SSO

## <a name="adding-procore-sso-from-the-gallery"></a>Dodawanie dodawania sytego sydaj proso z galerii

Aby skonfigurować integrację aplikacji SSO Procore z usługą Azure AD, należy dodać wpisowy naliczanie tożsamości Procore z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać proso sytą w galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Procore SSO**, wybierz **Procore SSO** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Procore SSO na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą procore logowania jednokrotnego na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji logowania jednokrotnego procore.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego procore, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne Procore](#configure-procore-sso-single-sign-on)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego SSO Procore](#create-procore-sso-test-user)** — aby mieć odpowiednik Britta Simon w Procore SSO, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą logowania jednokrotnego procore, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **logowania jednokrotnego Procore** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![Procore — informacje o logach jednokrotnych i adresach URL](common/preintegrated.png)

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie aplikacji SSO Procore** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-procore-sso-single-sign-on"></a>Konfigurowanie logowania jednokrotnego procore

1. Aby skonfigurować logowanie jednokrotne po stronie **logowania jednokrotnego Procore,** zaloguj się do witryny firmy procore jako administrator.

2. Z listy rozwijanej przybornika kliknij pozycję **Administrator,** aby otworzyć stronę ustawień logowania jedno i przesuwu.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/procore_tool_admin.png)

3. Wklej wartości w polach opisanych poniżej-

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. W polu **tekstowym adres URL pojedynczego logowania wystawcy** wklej wartość **identyfikatora usługi Azure AD** skopiowanego z witryny Azure portal.

    b. W polu **Docelowy adres URL logowania SAML** wklej wartość **adresu URL logowania** skopiowany z witryny Azure portal.

    d. Teraz otwórz **kod XML metadanych federacji** pobrany powyżej z witryny Azure portal i skopiuj certyfikat w tagu o nazwie **X509Certificate**. Wklej skopiowaną wartość do pola **Jednokrotne podpisanie na x509 Certyfikat.**

4. Kliknij przycisk **Zapisz zmiany**.

5. Po tych ustawieniach musisz wysłać **nazwę domeny** (np. **contoso.com**), za pośrednictwem której logujesz się do Procore do zespołu pomocy [technicznej Procore,](https://support.procore.com/) a oni aktywują federacyjne logowanie logowania do logowania dla tej domeny.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** `brittasimon@yourcompanydomain.extension`użytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do procore logowania jednokrotnego.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **Procore SSO**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **procore SSO**.

    ![Łącze SSO Procore na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-procore-sso-test-user"></a>Utwórz użytkownika testowego SSO Procore

Wykonaj poniższe kroki, aby utworzyć użytkownika testowego Procore po stronie SSO Procore.

1. Zaloguj się do witryny firmy procore jako administrator.    

2. Z listy rozwijanej przybornika kliknij pozycję **Katalog,** aby otworzyć stronę katalogu firmy.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Kliknij opcję **Dodaj osobę,** aby otworzyć formularz i wprowadzić następujące opcje -

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_user_add.png)

    a. W polu tekstowym **Imię** wpisz imię użytkownika, takie jak **Britta**.

    b. W polu **tekstowym Nazwisko** wpisz nazwisko użytkownika, takie jak **Simon**.

    d. W polu **tekstowym Adres e-mail** wpisz BrittaSimon@contoso.comadres e-mail użytkownika w stylu .

    d. Wybierz **pozycję Szablon uprawnień** jako **zastosuj szablon uprawnień później**.

    e. Kliknij przycisk **Utwórz**.

4. Sprawdź i zaktualizuj szczegóły nowo dodanego kontaktu.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_user_check.png)

5. Kliknij **zapisz i wyślij zaproszenie** (jeśli wymagane jest zaproszenie za pośrednictwem poczty) lub **Zapisz** (Zapisz bezpośrednio), aby zakończyć rejestrację użytkownika.
    
    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Logaj firmy Procore w Panelu dostępu należy automatycznie zalogować się do logującego się do proore, dla którego skonfigurowano logującą jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

