---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Vidyard | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 94e5893caa4dc19702f45e9b42727aab4f884bcd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65865627"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Vidyard

W tym samouczku dowiesz się, jak zintegrować Vidyard w usłudze Azure Active Directory (Azure AD).
Integrowanie Vidyard z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Vidyard.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Vidyard (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Vidyard, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Vidyard logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Vidyard **SP** i **tożsamości** jednokrotne logowanie inicjowane przez

* Obsługuje Vidyard **Just In Time** aprowizacji użytkowników

## <a name="adding-vidyard-from-the-gallery"></a>Dodawanie Vidyard z galerii

Aby skonfigurować integrację Vidyard w usłudze Azure AD, należy dodać Vidyard z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Vidyard z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Vidyard**, wybierz opcję **Vidyard** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Vidyard na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Vidyard w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Vidyard musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Vidyard, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Vidyard logowania jednokrotnego](#configure-vidyard-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Vidyard](#create-vidyard-test-user)**  — aby odpowiednikiem Britta Simon w Vidyard połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Vidyard, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Vidyard** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Vidyard domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Vidyard domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Te wartości zostaną zaktualizowane o rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, które zostało wyjaśnione w dalszej części tego samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

7. Na **Konfigurowanie Vidyard** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-vidyard-single-sign-on"></a>Konfigurowanie Vidyard logowanie jednokrotne

1. W oknie przeglądarki internetowej innej Zaloguj się w witrynie Vidyard oprogramowania firmy jako administrator.

2. Na pulpicie nawigacyjnym Vidyard wybierz **grupy** > **zabezpieczeń**

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure1.png)

3. Kliknij przycisk **nowy profil** kartę.

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure2.png)

4. W **plik konfiguracji SAML** sekcji, wykonaj następujące czynności:

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure3.png)

    a. Wprowadź nazwę profilu ogólnego w **nazwa profilu** pola tekstowego.

    b. Kopiuj **strony logowania użytkownika logowania jednokrotnego** wartość i wklej go w **adres URL logowania** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    c. Kopiuj **adres URL usługi ACS** wartość i wklej go w **adres URL odpowiedzi** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    d. Kopiuj **adres URL wystawcy/metadanych** wartość i wklej go w **identyfikator** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    e. Otwórz plik certyfikatu pobrany z witryny Azure portal w programie Notatnik i wklej go do **certyfikat X.509** pola tekstowego.

    f. W **adresu URL punktu końcowego protokołu SAML** pola tekstowego, Wklej wartość **adres URL logowania** skopiowane z witryny Azure portal.

    g. Kliknij pozycję **Potwierdź**.

5. Na karcie Logowanie jednokrotne wybierz **przypisać** obok istniejącego profilu

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Po utworzeniu profilu programu rejestracji Jednokrotnej, należy ją przypisać do żadnych grup, dla której użytkownicy wymagają dostępu za pośrednictwem platformy Azure. Jeśli użytkownik nie istnieje w grupie, do którego zostały przypisane, Vidyard automatycznie utworzyć konto użytkownika i przypisywanie ich roli w czasie rzeczywistym.

6. Wybierz grupę organizacji, który jest widoczny w **grup dostępnych do przypisania**.

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure5.png)

7. Widać przypisanych grup w ramach **aktualnie przypisane grupy**. Wybierz rolę dla grupy, zgodnie z Twojej organizacji i kliknij przycisk **Potwierdź**.

    ![Konfiguracja Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Aby uzyskać więcej informacji, zobacz [tego dokumentu](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

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

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Vidyard.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Vidyard**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Vidyard**.

    ![Link Vidyard na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-vidyard-test-user"></a>Tworzenie użytkownika testowego Vidyard

W tej sekcji użytkownika o nazwie Britta Simon jest tworzony w Vidyard. Vidyard obsługuje aprowizacji użytkowników w czasie, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w Vidyard, nowy katalog jest tworzony po uwierzytelnieniu.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Vidyard](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Vidyard w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Vidyard, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

