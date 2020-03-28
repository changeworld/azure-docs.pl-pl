---
title: 'Samouczek: Integracja usługi Azure Active Directory z administratorem dostępu prywatnego Zscaler | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a administratorem dostępu prywatnego Zscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7f0bc04bb8e7358f5bdb2e4dfc3db198e24303f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67085640"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Samouczek: Integracja usługi Azure Active Directory z administratorem dostępu prywatnego Zscaler

W tym samouczku dowiesz się, jak zintegrować administratora prywatnego dostępu Zscaler z usługą Azure Active Directory (Azure AD).
Integracja administratora programu Zscaler private access z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do administratora prywatnego dostępu Zscaler.
* Można włączyć użytkowników, aby automatycznie zalogować się do Zscaler Private Access Administrator (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z administratorem programu Zscaler Private Access, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego administratora systemu Zscaler

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Administrator dostępu prywatnego Zscaler **obsługuje** jednostkę SSO inicjowane przez sp i **idp**

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Dodawanie administratora prywatnego dostępu Zscaler z galerii

Aby skonfigurować integrację administratora prywatnego dostępu Zscaler z usługą Azure AD, należy dodać administratora prywatnego dostępu Zscaler z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać administratora programu Zscaler Private Access z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Zscaler Private Access Administrator**, wybierz **Zscaler Private Access Administrator** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Administrator prywatnego dostępu Zscaler na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Zscaler Administrator dostępu prywatnego na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Usłudze Zscaler Private Access Administrator.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą administratora dostępu prywatnego Zscaler, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne administratora dostępu prywatnego Zscaler](#configure-zscaler-private-access-administrator-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Administratora dostępu prywatnego Zscaler](#create-zscaler-private-access-administrator-test-user)** — aby mieć odpowiednik Britta Simon w Zscaler Private Access Administrator, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą administratora prywatnego dostępu Zscaler, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **administratora dostępu prywatnego Zscaler** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    ![Zscaler Private Access Administrator Domain and URL Single Sign on informacje](common/idp-relay.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<subdomain>.private.zscaler.com/auth/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.private.zscaler.com/auth/sso`

    d. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **Stan przekaźnika** wpisz adres URL: `idpadminsso`

5.  Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **sp,** wykonaj następujący krok:

    ![Zscaler Private Access Administrator Domain and URL Single Sign on informacje](common/both-signonurl.png)

    W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta prywatnego dostępu Zscaler,](https://help.zscaler.com/zpa-submit-ticket) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie administratora dostępu prywatnego Zscaler** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Konfigurowanie logowania jednokrotnego administratora programu Zscaler Private Access

1. W innym oknie przeglądarki internetowej zaloguj się do administratora prywatnego dostępu Zscaler jako administrator.

2. U góry kliknij pozycję **Administracja** i przejdź do sekcji **UWIERZYTELNIANIE,** kliknij pozycję **Konfiguracja idP**.

    ![Administrator prywatnego dostępu Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. W prawym górnym rogu kliknij pozycję **Dodaj konfigurację idp**. 

    ![Zscaler Administrator dostępu prywatnego addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Na stronie **Dodaj konfigurację IdP** wykonaj następujące czynności:
 
    ![Administrator dostępu prywatnego Zscaler idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Kliknij **pozycję Wybierz plik,** aby przekazać pobrany plik metadanych z usługi Azure AD w polu **Przekazywanie pliku metadanych IdP.**

    b. Odczytuje **metadane IdP** z usługi Azure AD i wypełnia wszystkie informacje o polach, jak pokazano poniżej.

    ![Zscaler Administrator dostępu prywatnego idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    d. Wybierz **opcję Logowanie jednokrotne** jako **administrator**.

    d. Wybierz domenę z pola **Domeny.**
    
    e. Kliknij przycisk **Zapisz**.

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

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, przyznając dostęp do Zscaler administratora dostępu prywatnego.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **Administratora dostępu prywatnego Zscaler**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Administrator prywatnego dostępu Zscaler**.

    ![Łącze Administratora dostępu prywatnego Zscaler na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Utwórz użytkownika testowego Administratora dostępu prywatnego zscalera

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do administratora prywatnego dostępu Zscaler, muszą one zostać zainicjowane przez administratora dostępu prywatnego Zscaler. W przypadku administratora prywatnego dostępu Zscaler inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Zscaler Private Access Administrator jako administrator.

2. U góry kliknij pozycję **Administracja** i przejdź do sekcji **UWIERZYTELNIANIE,** kliknij pozycję **Konfiguracja idP**.

    ![Administrator prywatnego dostępu Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Kliknij **pozycję Administratorzy** z lewej strony menu.

    ![Administrator prywatnego dostępu Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. W prawym górnym rogu kliknij pozycję **Dodaj administratora:**

    ![Administrator dostępu prywatnego Zscaler dodał administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Na stronie **Dodaj administratora** wykonaj następujące czynności:

    ![Administrator dostępu prywatnego Zscaler administratora](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. W polu tekstowym **Nazwa użytkownika** wprowadź BrittaSimon@contoso.comadres e-mail użytkownika, takiego jak .

    b. W **polach tekstowych Hasło** wpisz hasło.

    d. W polach tekstowych **Potwierdź hasło** wpisz hasło.

    d. Wybierz **pozycję Rola** jako administrator prywatnego dostępu **Zscaler**.

    e. W polu tekstowym **E-mail** wprowadź adres e-mail użytkownika, na przykład BrittaSimon@contoso.com.

    f. W polach tekstowych **Telefon** wpisz numer telefonu.

    g. W obszarze tekstowym **Strefa czasowa** wybierz strefę czasową.

    h. Kliknij przycisk **Zapisz**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Administratora dostępu prywatnego programu Zscaler w Panelu dostępu należy zalogować się automatycznie do administratora prywatnego dostępu Zscaler, dla którego skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

