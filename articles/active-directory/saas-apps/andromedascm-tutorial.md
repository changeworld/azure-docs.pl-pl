---
title: 'Samouczek: Integracja usługi Azure Active Directory z Andromedą | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a andromedą.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68fa570ecfbafe2000bfa6eb9fa159dff48219a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107085"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Samouczek: Integracja usługi Azure Active Directory z andromedą

W tym samouczku dowiesz się, jak zintegrować Andromedę z usługą Azure Active Directory (Azure AD).
Integracja Andromedy z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do Andromeda.
* Można włączyć użytkowników, aby automatycznie zalogować się do Andromeda (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z andromedą, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego Andromeda

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Andromeda obsługuje **sp i IDP** zainicjowane SSO
* Andromeda obsługuje inicjowanie obsługi administracyjnej użytkowników **just in time**

## <a name="adding-andromeda-from-the-gallery"></a>Dodawanie Andromedy z galerii

Aby skonfigurować integrację Andromedy z usługą Azure AD, należy dodać Andromedę z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Andromedę z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Andromeda**, wybierz **Andromeda** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Andromeda na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Andromeda na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Andromedie.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą andromedy, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne Andromedy](#configure-andromeda-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Andromeda](#create-andromeda-test-user)** — aby mieć odpowiednik Britta Simon w Andromeda, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą andromedy, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Andromeda** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    ![Informacje o logach i logowaniach do domeny Andromedy i adresów URL](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<tenantURL>.ngcxpress.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    ![Informacje o logach i logowaniach do domeny Andromedy i adresów URL](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Wartość zostanie zaktualizowana za pomocą rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania, który zostanie wyjaśniony w dalszej części samouczka.

6. Aplikacja Andromeda oczekuje potwierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

    > [!Important]
    > Wyczyść definicje obszaru nazw podczas konfigurowania tych definicji.

7. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności: 

    | Nazwa | Atrybut źródłowy|
    | ------ | -----------|
    | role (rola)        | Rola specyficzna dla aplikacji |
    | type        | Typ aplikacji |
    | company       | CompanyName |

    > [!NOTE]
    > Nie ma prawdziwych wartości. Te wartości są tylko w celach demonstracyjnych, użyj ról organizacji.

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij przycisk **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

9. W sekcji **Konfigurowanie Andromedy** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-andromeda-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Andromedy

1. Zaloguj się do witryny firmy Andromeda jako administrator.

2. W górnej części paska menu kliknij pozycję **Administrator** i przejdź do **pozycji Administracja**.

    ![Andromeda admin](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. Po lewej stronie paska narzędzi w sekcji **Interfejsy** kliknij pozycję **Konfiguracja SAML**.

    ![Andromeda saml (Andromeda saml)](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Na stronie **konfiguracji SAML** wykonaj następujące czynności:

    ![Andromeda config](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Sprawdź **włącz opcję SSO z SAML**.

    b. W sekcji **Informacje Andromeda** skopiuj wartość **tożsamości SP** i wklej ją do pola tekstowego **Identyfikator** podstawowej sekcji **Konfiguracji SAML.**

    d. Skopiuj wartość **adresu URL konsumenta** i wklej ją do pola tekstowego **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja SAML.**

    d. Skopiuj wartość **adresu URL logowania** i wklej ją do pola tekstowego Url **logowania** w sekcji **Podstawowa konfiguracja SAML.**

    e. W sekcji **Dostawca tożsamości SAML** wpisz nazwę dostawcy tożsamości.

    f. W **polu tekstowym Znak jednokrotny na końcu** wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    g. Otwórz pobrany **certyfikat zakodowany base64** z witryny Azure portal w notatniku, wklej go do pola tekstowego **certyfikatu X 509.**
    
    h. Zamapuj następujące atrybuty z odpowiednią wartością, aby ułatwić logowanie logowania logowania z usługi Azure AD. Atrybut **Identyfikator użytkownika** jest wymagany do logowania. W przypadku inicjowania obsługi administracyjnej wymagane są **wiadomości e-mail,** **firma,** **typ użytkownika**i **rola.** W tej sekcji definiujemy mapowanie atrybutów (nazwa i wartości), które są skorelowane z tymi zdefiniowanymi w witrynie Azure portal

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Kliknij przycisk **Zapisz**.

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

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, udzielając dostępu do Andromeda.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz **pozycję Andromeda**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **andromedę**.

    ![Łącze Andromedy na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-andromeda-test-user"></a>Tworzenie użytkownika testowego Andromedy

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Andromeda. Andromeda obsługuje just-in-time inicjowania obsługi administracyjnej użytkowników, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w Andromedie, nowy jest tworzony po uwierzytelnieniu. Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Andromeda Client](https://www.ngcsoftware.com/support/).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Andromeda w Panelu dostępu należy automatycznie zalogować się do Andromedy, dla której skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)