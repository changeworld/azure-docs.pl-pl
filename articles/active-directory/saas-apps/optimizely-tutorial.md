---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Optimizely | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Optimizely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: bf4f1a08c4356a68db5315b7563adb3b6c884d41
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67095503"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Optimizely

W tym samouczku dowiesz się, jak zintegrować Optimizely w usłudze Azure Active Directory (Azure AD).
Integrowanie Optimizely z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Optimizely.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Optimizely (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Optimizely, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Optimizely logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Optimizely **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-optimizely-from-the-gallery"></a>Dodawanie Optimizely z galerii

Aby skonfigurować integrację Optimizely w usłudze Azure AD, należy dodać Optimizely z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Optimizely z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Optimizely**, wybierz opcję **Optimizely** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Optimizely na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Optimizely w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Optimizely musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Optimizely, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Optimizely logowania jednokrotnego](#configure-optimizely-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Optimizely](#create-optimizely-test-user)**  — aby odpowiednikiem Britta Simon w Optimizely połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Optimizely, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Optimizely** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Optimizely domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://app.optimizely.net/<instance name>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca: `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Te wartości nie są rzeczywiste. Wartość zostanie zaktualizowana o rzeczywisty adres URL logowania i identyfikator, który zostało wyjaśnione w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja Optimizely oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę  **Edytuj** , aby otworzyć okno dialogowe  **Atrybuty użytkownika** .

    ![image](common/edit-attribute.png)

6. Ponadto powyżej Optimizely aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Name (Nazwa) | Atrybut źródłowy |
    | ---------------| --------------- |
    | email | user.mail |
    
    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie Optimizely** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-optimizely-single-sign-on"></a>Konfigurowanie Optimizely logowanie jednokrotne

1. Aby skonfigurować logowanie jednokrotne na **Optimizely** po stronie, skontaktuj się z menedżerem Optimizely i zapewnić pobrany **certyfikat (Base64)** i odpowiednie skopiowany adresów URL.

2. W odpowiedzi na wiadomości e-mail Optimizely pozwala na adres URL logowania (SSO zainicjowanego przez dostawcę usług) i wartości Identyfikator usługi dostawcy jednostki.

    a. Kopiuj **zainicjowanego przez dostawcę usług adres URL logowania jednokrotnego** podana przez Optimizely i Wklej w **na adres URL logowania** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    b. Kopiuj **identyfikator jednostki dostawcy usługi** podana przez Optimizely i Wklej w **identyfikator** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

3. W oknie innej przeglądarki logowanie jednokrotne do aplikacji Optimizely.

4. Możesz nazwa konta w prawym górnym rogu kliknij pozycję i następnie **ustawienia konta**.

    ![Usługa Azure AD logowanie jednokrotne](./media/optimizely-tutorial/tutorial_optimizely_09.png)

5. Zaznacz pole wyboru na karcie konto **włączyć logowanie Jednokrotne** w ramach rejestracji jednokrotnej w **Przegląd** sekcji.
  
    ![Usługa Azure AD logowanie jednokrotne](./media/optimizely-tutorial/tutorial_optimizely_10.png)

6. Kliknij polecenie **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Optimizely.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Optimizely**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Optimizely**.

    ![Link Optimizely na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-optimizely-test-user"></a>Tworzenie użytkownika testowego Optimizely

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Optimizely.

1. Na stronie głównej wybierz **współpracowników** kartę.

2. Aby dodać nowe współpracownika do projektu, kliknij **nowe współpracownika**.
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/optimizely-tutorial/create_aaduser_10.png)

3. Podaj adres e-mail i przypisać im rolę. Kliknij pozycję **Invite** (Zaproś).

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/optimizely-tutorial/create_aaduser_11.png)

4. Otrzyma wiadomość e-mail z zaproszeniem. Przy użyciu adresu e-mail, muszą oni zalogować się do Optimizely.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Optimizely w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Optimizely, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

