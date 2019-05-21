---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą aplikacji DocuSign | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i DocuSign.
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
ms.openlocfilehash: 72505cfc0a86c00882de37c35dff61a12a9c3fbe
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65899590"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą aplikacji DocuSign

W tym samouczku dowiesz się, jak zintegrować DocuSign w usłudze Azure Active Directory (Azure AD).
Integrowanie aplikacji DocuSign z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do aplikacji DocuSign.
* Aby umożliwić użytkownikom można automatycznie zalogowany do DocuSign (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD aplikacji docusign, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* DocuSign logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje DocuSign **SP** jednokrotne logowanie inicjowane przez

* Obsługuje DocuSign **Just In Time** aprowizacji użytkowników

## <a name="adding-docusign-from-the-gallery"></a>Dodawanie aplikacji DocuSign z galerii

Aby skonfigurować integrację DocuSign w usłudze Azure AD, należy dodać DocuSign z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać DocuSign w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **DocuSign**, wybierz opcję **DocuSign** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![DocuSign na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą aplikacji DocuSign w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze DocuSign musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego aplikacji docusign, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie aplikacji DocuSign logowania jednokrotnego](#configure-docusign-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego DocuSign](#create-docusign-test-user)**  — aby odpowiednikiem Britta Simon w aplikacji DocuSign, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowania jednokrotnego aplikacji docusign, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **DocuSign** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena aplikacji DocuSign i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator, który zostało wyjaśnione w dalszej części **Wyświetl SAML 2.0 punkty końcowe** sekcji, w tym samouczku.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie DocuSign** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-docusign-single-sign-on"></a>Konfigurowanie aplikacji DocuSign logowanie jednokrotne

1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **portalu administracyjnego DocuSign** jako administrator.

2. W górnym prawym rogu strony kliknij profil **logo** a następnie kliknij polecenie **przejdź do administratora**.
  
    ![Konfigurowanie logowania jednokrotnego][51]

3. Na stronie rozwiązania domeny, kliknij na **domen**

    ![Konfigurowanie logowania jednokrotnego][50]

4. W obszarze **domen** kliknij **oświadczenia domeny**.

    ![Konfigurowanie logowania jednokrotnego][52]

5. Na **oświadczenia domeny** okna dialogowego w **nazwy domeny** polu tekstowym wpisz domenę firmy, a następnie kliknij przycisk **oświadczenia**. Upewnij się, że zweryfikować domenę, a stan jest aktywny.

    ![Konfigurowanie logowania jednokrotnego][53]

6. Na stronie rozwiązania domeny, kliknij **dostawców tożsamości**.
  
    ![Konfigurowanie logowania jednokrotnego][54]

7. W obszarze **dostawców tożsamości** kliknij **Dodawanie dostawcy tożsamości**. 

    ![Konfigurowanie logowania jednokrotnego][55]

8. Na **ustawień dostawcy tożsamości** strony, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego][56]

    a. W **nazwa** pole tekstowe, wpisz unikatową nazwę dla danej konfiguracji. Nie należy używać miejsc do magazynowania.

    b. W **wystawca dostawcy tożsamości w polu tekstowym**, Wklej wartość **usługi Azure AD identyfikator**, które zostały skopiowane z witryny Azure portal.

    c. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL logowania**, które zostały skopiowane z witryny Azure portal.

    d. W **adres URL wylogowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    e. Wybierz **żądania uwierzytelniania logowania**.

    f. Jako **żądania wysyłania AuthN**, wybierz opcję **WPIS**.

    g. Jako **wysyłania żądania wylogowania**, wybierz opcję **UZYSKAĆ**.

    h. W **mapowanie atrybutu niestandardowego** sekcji, kliknij pozycję **Dodaj nowe MAPOWANIE**.

    ![Konfigurowanie logowania jednokrotnego][62]

    i. Wybierz pole, które ma być mapowany na podstawie usługi Azure AD oświadczenia. W tym przykładzie **emailaddress** oświadczeń jest zamapowana z wartością **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Jest domyślna nazwa oświadczeń z usługi Azure AD oświadczenie adresu e-mail, a następnie kliknij przycisk **ZAPISZ**.

    ![Konfigurowanie logowania jednokrotnego][57]

    > [!NOTE]
    > To zrobić w odpowiednim **identyfikator użytkownika** do mapowania użytkowników z usługi Azure AD do mapowania użytkowników DocuSign. Zaznacz odpowiednie pola, a następnie wprowadź odpowiednią wartość na podstawie własnych ustawień organizacji.

    j. W **certyfikatów dostawcy tożsamości** , kliknij przycisk **Dodaj certyfikat**, a następnie przekaż certyfikat został pobrany z portalu usługi Azure AD i kliknij przycisk **ZAPISZ**.

    ![Konfigurowanie logowania jednokrotnego][58]

    k. W **dostawców tożsamości** kliknij **akcje**, a następnie kliknij przycisk **punktów końcowych**.

    ![Konfigurowanie logowania jednokrotnego][59]

    l. W **Wyświetl SAML 2.0 punkty końcowe** sekcji na **portalu administracyjnego DocuSign**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego][60]

    * Kopiowanie **adres URL wystawcy dostawcy usługi**, a następnie wklej go do **identyfikator** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    * Kopiuj **adres URL logowania dostawcy usługi**, a następnie wklej go do **na adres URL logowania** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    * Kliknij przycisk **Zamknij**

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola brittasimon@yourcompanydomain.extension. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do aplikacji DocuSign.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **DocuSign**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **DocuSign**.

    ![Link aplikacji DocuSign na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-docusign-test-user"></a>Tworzenie użytkownika testowego DocuSign

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w usłudze DocuSign. DocuSign obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w usłudze DocuSign, nowy katalog jest tworzony po uwierzytelnieniu.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej DocuSign](https://support.docusign.com/).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka DocuSign w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze DocuSign, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

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
