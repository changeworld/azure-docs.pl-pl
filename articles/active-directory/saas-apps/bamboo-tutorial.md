---
title: 'Samouczek: Integracja usługi Azure Active Directory z samol SSO dla bamboo by resolution GmbH | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a samol SSO dla bamboo przez resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 044a2f9a9ecd0ccceb99ce7999af7e2c8578950d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67106531"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Samouczek: Integracja usługi Azure Active Directory z samol SSO dla bamboo by resolution GmbH

W tym samouczku dowiesz się, jak zintegrować saml SSO dla bamboo przez rozdzielczość GmbH z usługi Azure Active Directory (Azure AD).
Integracja saml SSO dla bamboo by resolution GmbH z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do saml SSO dla bamboo przez resolution GmbH.
* Można włączyć automatyczne logowanie użytkowników do logowania jednokrotnego SAML dla firmy Bamboo przez resolution GmbH (logowanie jednokrotne) za pomocą ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z saml SSO dla bamboo by resolution GmbH, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* SAML SSO dla Bamboo by resolution GmbH subskrypcja z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SAML SSO dla Bamboo by resolution GmbH obsługuje **sp i IDP** zainicjowane SSO
* SAML SSO dla Bamboo by resolution GmbH obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Dodawanie SAML SSO dla Bamboo przez rozdzielczość GmbH z galerii

Aby skonfigurować integrację saml SSO dla bamboo przez rozdzielczość GmbH do usługi Azure AD, musisz dodać SAML SSO dla Bamboo przez rozdzielczość GmbH z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać SAML SSO dla bamboo by resolution GmbH z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SAML SSO dla Bamboo by resolution GmbH**, wybierz **SAML SSO dla Bamboo by resolution GmbH** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![SAML SSO dla Bamboo by resolution GmbH na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą SAML SSO dla bamboo przez rozdzielczość GmbH na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji SAML SSO dla bamboo by resolution GmbH.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego SAML dla firmy Bamboo według rozdzielczości GmbH, należy wykonać następujące elementy konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj samol SSO dla bamboo by resolution GmbH Single Sign-On](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz SAML SSO dla bamboo by resolution GmbH test użytkownika](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** - mieć odpowiednik Britta Simon w SAML SSO dla Bamboo przez rozdzielczość GmbH, który jest połączony z reprezentacji Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego SAML dla firmy Bamboo przez resolution GmbH, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie **SSO SAML dla bamboo by resolution GmbH** wybierz opcję **Logowanie jednokrotne.**

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    ![SAML SSO dla Bamboo przez rozdzielczość GmbH Domain i adresy URL informacje o logowanie jednokrotne](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<server-base-url>/plugins/servlet/samlsso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

5. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    ![SAML SSO dla Bamboo przez rozdzielczość GmbH Domain i adresy URL informacje o logowanie jednokrotne](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [saml sso dla bamboo by resolution GmbH Client support team,](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie samouszeńców SAML dla bamboo by resolution GmbH** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>Konfigurowanie logowania sytograficznego SAML dla firmy Bamboo w rozdzielczości GmbH z loganiem jednokrotnym

1. Zarejestruj się jako administrator sso SAML dla firmy Bamboo by resolution GmbH.

1. Po prawej stronie głównego paska narzędzi kliknij pozycję **Dodatki ustawień** > **.**

    ![Ustawienia](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Przejdź do sekcji BEZPIECZEŃSTWO, kliknij **SAML SingleSignOn** na pasku menu.

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Na stronie **konfiguracji wtyczki SIngleSignOn języka SAML** kliknij pozycję **Dodaj dostawcę tożsamości**.

    ![Dodawanie dostawcy tożsamości](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Na stronie **wybierania dostawcy tożsamości języka SAML** wykonaj następujące kroki:

    ![Dostawca tożsamości](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. W obszarze **Typ dostawcy tożsamości** ustaw wartość **AZURE AD**.

    b. W polu tekstowym **Nazwa** wpisz nazwę.

    d. W polu tekstowym **Opis** wpisz opis.

    d. Kliknij przycisk **alej**.

1. Na stronie **konfiguracji dostawcy tożsamości** kliknij przycisk **Dalej**.

    ![Konfiguracja tożsamości](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Na stronie **importowania metadanych dostawcy tożsamości języka SAML** kliknij pozycję **Załaduj plik**, aby przekazać plik **METADATA XML** pobrany z witryny Azure Portal.

    ![Idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Kliknij przycisk **alej**.

1. Kliknij pozycję **Zapisz ustawienia**.

    ![Zapisywanie](./media/bamboo-tutorial/tutorial_bamboo_save.png)

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

W tej sekcji możesz włączyć Britta Simon do korzystania z azure logowania jednokrotnego, udzielając dostępu do SAML SSO dla bamboo przez rozdzielczość GmbH.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz **saml SSO dla bamboo według rozdzielczości GmbH**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **SAML SSO for Bamboo by resolution GmbH**.

    ![Łącze SAML SSO for Bamboo by resolution GmbH na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Tworzenie saml SSO dla bamboo przez rozdzielczość GmbH test użytkownika

Celem tej sekcji jest stworzenie użytkownika o nazwie Britta Simon w SAML SSO dla Bamboo przez resolution GmbH. SAML SSO dla Bamboo by resolution GmbH obsługuje aprowizacji just-in-time, a także użytkownicy mogą być tworzone ręcznie, skontaktuj się z [SAML SSO dla Bamboo przez rozdzielczość GmbH Zespół wsparcia klienta](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) zgodnie z wymaganiami.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SAML SSO dla bamboo by resolution GmbH w Panelu dostępu należy automatycznie zalogować się do SSO SAML dla Bamboo w rozdzielczości GmbH, dla której skonfigurowano logi SSO. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
