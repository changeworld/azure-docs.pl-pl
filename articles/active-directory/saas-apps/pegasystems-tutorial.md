---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu systemów Pega | Dokumentacja firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między systemami Pega a usługi Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 013e477b66d2772698ce5c9cc61a59f8a5a04a5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094896"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu systemów Pega

W tym samouczku dowiesz się, jak integrować systemy Pega za pomocą usługi Azure Active Directory (Azure AD).

Ta integracja zapewnia następujące korzyści:

* Możesz użyć usługi Azure AD w celu kontrolowania, kto ma dostęp do systemów Pega.
* Aby umożliwić użytkownikom można automatycznie zalogowany do systemów Pega (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu systemów Pega, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz zarejestrować się w celu [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja Pega systemów, która ma logowanie jednokrotne włączone.

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka możesz skonfigurować i testowanie usługi Azure AD rejestracji jednokrotnej w środowisku testowym.

* Systemy Pega obsługuje logowanie Jednokrotne zainicjowane przez Dostawcę i inicjowane przez dostawcę tożsamości.

## <a name="add-pega-systems-from-the-gallery"></a>Dodaj Pega systemów z galerii

Aby skonfigurować integrację Pega systemów w usłudze Azure AD, należy dodać Pega systemów z galerii z listą zarządzanych aplikacji SaaS.

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** > **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **nową aplikację** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **systemów Pega**. Wybierz **systemów Pega** w wynikach wyszukiwania, a następnie wybierz **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować usługę Azure AD logowania jednokrotnego przy użyciu systemów Pega przy użyciu użytkownika testu o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiedniego użytkownika w systemach Pega.

Do konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu systemów Pega, należy wykonać następujące czynności:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  do włączenia tej funkcji dla użytkowników.
2. **[Konfigurowanie systemów Pega logowania jednokrotnego](#configure-pega-systems-single-sign-on)**  na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowania jednokrotnego.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  włączyć usługi Azure AD logowanie jednokrotne dla użytkownika.
5. **[Tworzenie użytkownika testowego systemów Pega](#create-a-pega-systems-test-user)**  połączony na reprezentację w postaci usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  można sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz usługę Azure AD logowania jednokrotnego w witrynie Azure portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z systemami Pega, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **systemów Pega** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**:

    ![Wybierz opcję logowania jednokrotnego](common/select-sso.png)

2. W **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML/WS-Fed** trybu, aby włączyć logowanie jednokrotne:

    ![Wybierz jedną metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** wybierz opcję **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okno dialogowe:

    ![Ikona edycji](common/edit-urls.png)

4. W **podstawową konfigurację protokołu SAML** okno dialogowe, jeśli chcesz skonfigurować aplikację w trybie inicjowane przez dostawcę tożsamości, wykonaj następujące czynności.

    ![Podstawowy plik konfiguracji SAML, okno dialogowe](common/idp-intiated.png)

    1. W **identyfikator** wprowadź adres URL, w tym wzorcu:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. W **adres URL odpowiedzi** wprowadź adres URL, w tym wzorcu:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Aby skonfigurować aplikację w trybie zainicjowanego przez dostawcę usług, zaznacz **Ustaw dodatkowe adresy URL** i wykonaj następujące czynności.

    ![Pega systemów domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/both-advanced-urls.png)

    1. W **adres URL logowania** Wprowadź znak na wartość adresu URL.

    1. W **tan przekaźnika** wprowadź adres URL, w tym wzorcu: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Wartości podane w tym miejscu są symbole zastępcze. Musisz rzeczywisty identyfikator, adres URL odpowiedzi, zaloguj się na adres URL i adresu URL stan przekazywania. Możesz uzyskać identyfikator i odpowiedzi z poziomu aplikacji Pega wartości adresu URL, zgodnie z opisem w dalszej części tego samouczka. Aby uzyskać wartość stanu usługi relay, skontaktuj się z pomocą [zespołu pomocy technicznej systemów Pega](https://www.pega.com/contact-us). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Aplikacja systemów Pega musi twierdzenia SAML w określonym formacie. Aby je uzyskać prawidłowy format, należy dodać mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia atrybutów domyślnych. Wybierz **Edytuj** ikonę, aby otworzyć **atrybutów użytkownika** okno dialogowe:

    ![Atrybuty użytkownika](common/edit-attribute.png)

7. Oprócz atrybutów, pokazano na poprzednim zrzucie ekranu aplikacja systemów Pega wymaga kilku więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W **oświadczenia użytkownika** części **atrybutów użytkownika** okna dialogowego pole, wykonaj następujące kroki, aby dodać te atrybuty tokenu języka SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Te wartości są specyficzne dla Twojej organizacji. Podaj odpowiednie wartości.

    1. Wybierz **Dodaj nowe oświadczenie** otworzyć **Zarządzanie oświadczenia użytkownika** okno dialogowe:

    ![Wybierz opcję Dodaj nowe oświadczenie.](common/new-save-attribute.png)

    ![Okno dialogowe Zarządzanie oświadczeniami użytkownika](common/new-attribute-details.png)

    1. W polu tekstowym **Nazwa** podaj nazwę atrybutu pokazanego dla tego wiersza.

    1. Pozostaw **Namespace** puste pole.

    1. Aby uzyskać **źródła**, wybierz opcję **atrybutu**.

    1. W **atrybut źródłowy** wybierz wartość atrybutu wyświetlanego dla tego wiersza.

    1. Wybierz przycisk **OK**.

    1. Wybierz pozycję **Zapisz**.

8. Na **Ustaw się logowania jednokrotnego przy użyciu protokołu SAML** stronie **certyfikat podpisywania SAML** zaznacz **Pobierz** łącze obok **XML metadanych Federacji** , zgodnie z wymaganiami, a następnie Zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/metadataxml.png)

9. W **systemy Pega** sekcji, skopiuj odpowiednie adresy URL, w zależności od wymagań.

    ![Skopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Usługa Azure AD identyfikator**.

    1. **Adres URL wylogowania**.

### <a name="configure-pega-systems-single-sign-on"></a>Konfigurowanie systemów Pega logowania jednokrotnego

1. Aby skonfigurować logowanie jednokrotne na **systemów Pega** po stronie, zaloguj się do portalu Pega przy użyciu konta administratora w innym oknie przeglądarki.

2. Wybierz **tworzenie** > **SysAdmin** > **usługi uwierzytelniania**:

    ![Wybierz usługę uwierzytelniania](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Wykonaj następujące czynności na **Utwórz usługę uwierzytelniania** ekranu.

    ![Tworzenie ekranu usługi uwierzytelniania](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. W **typu** listy wybierz **SAML 2.0**.

    1. W **nazwa** wprowadź dowolną nazwę (na przykład **logowania jednokrotnego usługi Azure AD**).

    1. W **krótki opis** wprowadź opis.  

    1. Wybierz **Utwórz i Otwórz**.
    
4. W **informacji o dostawcy tożsamości (IdP)** zaznacz **metadanych tożsamości importu** i przejdź do pliku metadanych, który został pobrany z witryny Azure portal. Kliknij przycisk **przesyłania** można załadować metadanych:

    ![Sekcja informacji o dostawcy tożsamości](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Importowanie spowoduje wypełnienie danych dostawcy tożsamości, jak pokazano poniżej:

    ![Zaimportowane dane dostawcy tożsamości](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Wykonaj następujące kroki w **ustawienia usługi dostawcy (SP)** sekcji.

    ![Ustawienia dostawcy usług](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Kopiuj **identyfikacji jednostki** wartość i wklej go w **identyfikator** pole w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    1. Kopiuj **lokalizacji potwierdzenie konsumenta Service (ACS)** wartość i wklej go w **adres URL odpowiedzi** pole w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    1. Wybierz **wyłączenie podpisywanie**.

7. Wybierz pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testu o nazwie Britta Simon w witrynie Azure portal.

1. W witrynie Azure portal wybierz **usługi Azure Active Directory** w okienku po lewej stronie wybierz **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**:

    ![Wybierz opcję Wszyscy użytkownicy](common/users.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu:

    ![Wybierz nowego użytkownika](common/new-user.png)

3. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W polu **Nazwa** wpisz **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wprowadź **brittasimon @\<domenatwojejfirmy >.\< Rozszerzenia >** . (Na przykład BrittaSimon@contoso.com.)

    c. Wybierz **hasło Show**i zanotuj wartość, która znajduje się w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Britta Simon do użycia platformy Azure logowania jednokrotnego przez udostępnienie jej Pega systemów.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz pozycję **systemów Pega**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **systemów Pega**.

    ![Lista aplikacji](common/all-applications.png)

3. W okienku po lewej stronie wybierz **użytkowników i grup**:

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** listy użytkowników, a następnie kliknij **wybierz** znajdujący się u dołu ekranu.

6. Jeśli oczekujesz wartość roli dla asercji SAML **wybierz rolę** okna dialogowego wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-pega-systems-test-user"></a>Tworzenie użytkownika testowego Pega systemów

Następnie należy utworzyć użytkownika o nazwie Britta Simon w systemach Pega. Praca z [zespołu pomocy technicznej systemów Pega](https://www.pega.com/contact-us) do tworzenia użytkowników.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz należy przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego za pomocą panelu dostępu.

Po wybraniu kafelka Pega systemów w panelu dostępu użytkownik powinien automatyczne logowanie do wystąpienia Pega systemów, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji, zobacz [dostępu i użycia aplikacji w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczków dotyczących integrowania aplikacji SaaS przy użyciu usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)