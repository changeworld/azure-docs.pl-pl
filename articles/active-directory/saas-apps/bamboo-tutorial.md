---
title: 'Samouczek: Azure integracji usługi Active Directory za pomocą logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH.
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
ms.openlocfilehash: 97c13469c9c70aec31314048b8971c66bae5cd49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65858794"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Samouczek: Usługa Azure integracji usługi Active Directory za pomocą logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH

W tym samouczku dowiesz się, jak zintegrować logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH w usłudze Azure Active Directory (Azure AD).
Integracja logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do logowania jednokrotnego SAML Bambus przez rozwiązania GmbH.
* Aby umożliwić użytkownikom można automatycznie zalogowany do logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integracji z usługą Azure AD za pomocą logowania jednokrotnego SAML dla Bambus przy rozdzielczości GmbH, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Logowania jednokrotnego SAML dla Bambus przez rozwiązania obsługuje GmbH **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez
* Logowania jednokrotnego SAML dla Bambus przez rozwiązania obsługuje GmbH **Just In Time** aprowizacji użytkowników

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Dodawanie logowania jednokrotnego SAML dla Bambus przy rozdzielczości GmbH z galerii

Aby skonfigurować integrację logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH w usłudze Azure AD, należy dodać logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać logowania jednokrotnego SAML dla Bambus za rozdzielczość GmbH z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH**, wybierz opcję **logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![Logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego SAML Bambus przez rozwiązania GmbH zależą od użytkownika testu o nazwie **Britta Simon**.
Do logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkownika w logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego SAML dla Bambus przy rozdzielczości GmbH logowania jednokrotnego](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Utwórz logowania jednokrotnego SAML dla Bambus przez użytkownika testowego GmbH rozpoznawania](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)**  — aby odpowiednikiem Britta Simon w logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Adresy URL i logowania jednokrotnego SAML dla Bambus przez rozpoznawanie domeny GmbH pojedynczy informacje logowania jednokrotnego](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Adresy URL i logowania jednokrotnego SAML dla Bambus przez rozpoznawanie domeny GmbH pojedynczy informacje logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołu pomocy technicznej logowania jednokrotnego SAML dla Bambus przez rozwiązania klienta GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

7. Na **Konfigurowanie logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>Konfigurowanie logowania jednokrotnego SAML dla Bambus przy rozdzielczości GmbH logowania jednokrotnego

1. Zaloguj się do usługi logowania jednokrotnego SAML dla Bambus przez witrynę firmy GmbH rozpoznawania jako administrator.

1. Po prawej stronie głównego paska narzędzi, kliknij przycisk **ustawienia** > **dodatki**.

    ![Ustawienia](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Przejdź do sekcji Zabezpieczenia, kliknij pozycję **SAML SingleSignOn** na pasku menu.

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Na stronie **konfiguracji wtyczki SIngleSignOn języka SAML** kliknij pozycję **Dodaj dostawcę tożsamości**.

    ![Dodawanie dostawcy tożsamości](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Na stronie **wybierania dostawcy tożsamości języka SAML** wykonaj następujące kroki:

    ![Dostawca tożsamości](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. W obszarze **Typ dostawcy tożsamości** ustaw wartość **AZURE AD**.

    b. W polu tekstowym **Nazwa** wpisz nazwę.

    c. W polu tekstowym **Opis** wpisz opis.

    d. Kliknij przycisk **Dalej**.

1. Na **konfiguracji dostawcy tożsamości** kliknij **dalej**.

    ![Konfiguracja tożsamości](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Na stronie **importowania metadanych dostawcy tożsamości języka SAML** kliknij pozycję **Załaduj plik**, aby przekazać plik **METADATA XML** pobrany z witryny Azure Portal.

    ![Idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Kliknij przycisk **Dalej**.

1. Kliknij pozycję **Zapisz ustawienia**.

    ![Zapisywanie](./media/bamboo-tutorial/tutorial_bamboo_save.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **logowania jednokrotnego SAML dla Bambus przez rozwiązania GmbH**.

    ![Logowania jednokrotnego SAML dla Bambus przez łącze GmbH rozwiązania na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Utwórz logowania jednokrotnego SAML dla Bambus przez użytkownika testowego GmbH rozwiązania

Celem tej sekcji jest utworzenie użytkownika wymagane Britta Simon w logowania jednokrotnego SAML Bambus przez rozwiązania GmbH. Logowania jednokrotnego SAML Bambus przez rozwiązania GmbH obsługę just-in-time, a użytkownicy mogą być tworzone ręcznie, skontaktuj się z pomocą [zespołu pomocy technicznej logowania jednokrotnego SAML dla Bambus przez rozwiązania klienta GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) zgodnie z wymaganiami.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Kliknięcie logowania jednokrotnego SAML dla Bambus rozpoznawania GmbH kafelka w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze logowania jednokrotnego SAML dla Bambus przez GmbH rozpoznawania, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
