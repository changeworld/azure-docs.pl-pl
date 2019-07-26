---
title: 'Samouczek: Azure Active Directory integrację z usługą DocuSign | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 5c7d6116ed2925e57f094a67f27a11f9e2d61831
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499258"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Samouczek: Integracja Azure Active Directory z usługą DocuSign

W tym samouczku dowiesz się, jak zintegrować usługę DocuSign z usługą Azure Active Directory (Azure AD).
Integracja DocuSign z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do usługi DocuSign.
* Możesz pozwolić użytkownikom na automatyczne logowanie do DocuSign (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą DocuSign, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączonym logowaniem jednokrotnym w DocuSign

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* DocuSign obsługuje logowanie jednokrotne w usłudze **SP**

* DocuSign obsługuje [Automatyczne Inicjowanie obsługi użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)

## <a name="adding-docusign-from-the-gallery"></a>Dodawanie DocuSign z galerii

Aby skonfigurować integrację programu DocuSign z usługą Azure AD, musisz dodać DocuSign z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać DocuSign z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Docusign**, wybierz pozycję **Docusign** from panel wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![DocuSign na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą DocuSign na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w DocuSign.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi DocuSign, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Skonfiguruj logowanie](#configure-docusign-single-sign-on)** jednokrotne w usłudze Docusign, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Utwórz użytkownika testowego Docusign](#create-docusign-test-user)** , aby uzyskać odpowiednik Britta Simon w Docusign, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą DocuSign, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Docusign** wybierz pozycję **Logowanie**jednokrotne.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![DocuSign domenę i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania i identyfikatora, który został wyjaśniony w dalszej części sekcji **punkty końcowe protokołu SAML 2,0** w samouczku.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie Docusign** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-docusign-single-sign-on"></a>Konfigurowanie logowania jednokrotnego DocuSign

1. W innym oknie przeglądarki sieci Web Zaloguj się do **portalu administratora Docusign** jako administrator.

2. W prawym górnym rogu strony kliknij pozycję **logo** profilu, a następnie kliknij pozycję **Przejdź do administratora**.
  
    ![Konfigurowanie logowania jednokrotnego][51]

3. Na stronie rozwiązania domeny kliknij pozycję **domeny** .

    ![Konfigurowanie logowania jednokrotnego][50]

4. W sekcji **domeny** kliknij pozycję **Przejmij domenę**.

    ![Konfigurowanie logowania jednokrotnego][52]

5. W oknie dialogowym Zażądaj **domeny** w polu tekstowym **nazwa domeny** wpisz domenę firmy, a następnie kliknij pozycję **Przejmij**. Upewnij się, że zweryfikowano domenę i że stan jest aktywny.

    ![Konfigurowanie logowania jednokrotnego][53]

6. Na stronie rozwiązania domeny kliknij pozycję **dostawcy tożsamości**.
  
    ![Konfigurowanie logowania jednokrotnego][54]

7. W sekcji **dostawcy tożsamości** kliknij pozycję **Dodaj dostawcę tożsamości**. 

    ![Konfigurowanie logowania jednokrotnego][55]

8. Na stronie **Ustawienia dostawcy tożsamości** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego][56]

    a. W polu tekstowym **Nazwa** wpisz unikatową nazwę konfiguracji. Nie używaj spacji.

    b. W polu **tekstowym wystawca dostawcy tożsamości**wklej wartość identyfikatora usługi **Azure AD**, która została skopiowana z Azure Portal.

    c. W polu tekstowym **adres URL logowania dostawcy tożsamości** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    d. W polu tekstowym **adres URL wylogowania dostawcy tożsamości** wklej wartość **adresu URL wylogowania**, która została skopiowana z Azure Portal.

    e. Wybierz pozycję **Podpisz żądanie AuthN**.

    f. Jako **Wyślij żądanie AuthN przez**, wybierz pozycję **post**.

    g. Jako **Wyślij żądanie wylogowania przez**, wybierz pozycję **Pobierz**.

    h. W sekcji **Mapowanie atrybutów niestandardowych** kliknij pozycję **Dodaj nowe mapowanie**.

    ![Konfigurowanie logowania jednokrotnego][62]

    i. Wybierz pole, które chcesz zmapować z zastrzeżeniem usługi Azure AD. W tym przykładzie **EmailAddress** jest mapowany na wartość **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . Jest to domyślna nazwa dla usługi Azure AD na potrzeby żądania poczty e-mail, a następnie kliknij przycisk **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego][57]

    > [!NOTE]
    > Użyj odpowiedniego **identyfikatora użytkownika** , aby zamapować użytkownika z usługi Azure AD do Docusign mapowania użytkownika. Wybierz odpowiednie pole i wprowadź odpowiednią wartość na podstawie ustawień organizacji.

    j. W sekcji **Certyfikaty dostawcy tożsamości** kliknij pozycję **Dodaj certyfikat**, a następnie Przekaż certyfikat pobrany z portalu usługi Azure AD, a następnie kliknij przycisk **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego][58]

    k. W sekcji **dostawcy tożsamości** kliknij pozycję **Akcje**, a następnie kliknij pozycję **punkty końcowe**.

    ![Konfigurowanie logowania jednokrotnego][59]

    l. W sekcji **Zobacz punkty końcowe protokołu SAML 2,0** w **portalu administracyjnym Docusign**wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego][60]

    * Skopiuj **adres URL wystawcy dostawcy usługi**, a następnie wklej go do pola tekstowego **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    * Skopiuj **adres URL logowania dostawcy usługi**, a następnie wklej go do pola tekstowego **Zaloguj się w adresie URL** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    * Kliknij przycisk **Zamknij** .

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz brittasimon@yourcompanydomain.extension. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi DocuSign.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **Docusign**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Docusign**.

    ![Link DocuSign na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-docusign-test-user"></a>Utwórz użytkownika testowego DocuSign

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w DocuSign. DocuSign obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze DocuSign, zostanie utworzony nowy po uwierzytelnieniu.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Docusign](https://support.docusign.com/).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka DocuSign w panelu dostępu należy automatycznie zalogować się do DocuSign, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
