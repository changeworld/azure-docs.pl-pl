---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu schematu organizacyjnego teraz | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i teraz schematu organizacyjnego.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: cc2bbd0c1220a37de640bde6294eb096b25e5398
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65870515"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu schematu organizacyjnego teraz

W tym samouczku dowiesz się, jak zintegrować teraz schematu organizacyjnego przy użyciu usługi Azure Active Directory (Azure AD).
Integrowanie schematu organizacyjnego teraz z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do chwili schematu organizacyjnego.
* Użytkownikom można automatycznie zalogowany do chwili schemat organizacyjny (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu schematu organizacyjnego teraz, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Schemat organizacyjny teraz logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Schemat organizacyjny teraz obsługuje **SP** i **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-orgchart-now-from-the-gallery"></a>Dodawanie schematu organizacyjnego teraz z galerii

Aby skonfigurować integrację teraz schematu organizacyjnego w usłudze Azure AD, należy dodać teraz schematu organizacyjnego z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać teraz schematu organizacyjnego z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **teraz schematu organizacyjnego**, wybierz opcję **teraz schemat organizacyjny** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Schemat organizacyjny teraz na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i test usługi Azure AD logowania jednokrotnego przy użyciu schematu organizacyjnego teraz w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika teraz schematu organizacyjnego musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu schematu organizacyjnego teraz, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie schematu organizacyjnego teraz logowanie jednokrotne](#configure-orgchart-now-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego teraz schematu organizacyjnego](#create-orgchart-now-test-user)**  — aby odpowiednikiem Britta Simon teraz schematu organizacyjnego, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z teraz schematu organizacyjnego, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **teraz schematu organizacyjnego** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następującą czynność:

    ![Schemat organizacyjny teraz domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-identifier.png)

    W polu tekstowym **Identyfikator** wpisz adres URL: `https://sso2.orgchartnow.com`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![image](common/both-preintegrated-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` jest **usługi Azure AD identyfikator** skopiowane z **Skonfiguruj teraz schematu organizacyjnego** sekcji opisany w dalszej części samouczka.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

7. Na **Skonfiguruj teraz schematu organizacyjnego** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-orgchart-now-single-sign-on"></a>Skonfiguruj teraz schematu organizacyjnego logowania jednokrotnego

Do konfigurowania logowania jednokrotnego na **teraz schematu organizacyjnego** stronie, musisz wysłać pobrany **XML metadanych Federacji** i odpowiednie skopiowany adresy URL z portalu Azure, aby [zespołem pomocy technicznej teraz schematu organizacyjnego ](mailto:ocnsupport@officeworksoftware.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

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

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do schematu organizacyjnego teraz.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **teraz schematu organizacyjnego**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **teraz schematu organizacyjnego**.

    ![Schemat organizacyjny teraz link na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-orgchart-now-test-user"></a>Tworzenie użytkownika testowego teraz schematu organizacyjnego

Aby umożliwić użytkownikom usługi Azure AD zalogować się do chwili schematu organizacyjnego, musi być obsługiwana w chwili schematu organizacyjnego. 

1. Schemat organizacyjny teraz obsługę just-in-time, który jest domyślnie włączona. Nowy użytkownik jest tworzony podczas próby dostępu teraz schematu organizacyjnego, jeśli go jeszcze nie istnieje. Tylko będzie utworzyć użytkownika w czasie inicjowania obsługi funkcji **tylko do odczytu** użytkownika, gdy żądanie logowania jednokrotnego pochodzi z rozpoznanym dostawcy tożsamości i wiadomości e-mail w potwierdzenie SAML nie znajduje się na liście użytkowników. Automatycznie inicjowania obsługi funkcji, musisz utworzyć grupę dostępu, pod tytułem **ogólne** teraz schematu organizacyjnego. Wykonaj poniższe kroki, aby utworzyć grupę dostępu:

    a. Przejdź do **Zarządzaj grupami** opcja po kliknięciu przycisku **koło zębate** w prawym górnym rogu interfejsu użytkownika.

    ![Schemat organizacyjny teraz grup](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Wybierz **Dodaj** ikonę i nazwy grupy **ogólne** kliknięcie **OK**. 

    ![Dodaj teraz schematu organizacyjnego](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Wybierz foldery, które mają użytkownicy ogólne lub tylko do odczytu, aby można było uzyskać dostęp do:

    ![Schemat organizacyjny teraz folderów](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Zablokuj** foldery tak, aby można je modyfikować tylko administratorom. Następnie naciśnij klawisz **OK**.

    ![Schemat organizacyjny teraz blokują](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Aby utworzyć **administratora** użytkowników i **odczytu/zapisu** użytkowników, należy ręcznie utworzyć użytkownika w celu uzyskania dostępu do ich poziomu uprawnień przy użyciu logowania jednokrotnego. Aby aprowizować konto użytkownika, wykonaj następujące czynności:

    a. Zaloguj się do schematu organizacyjnego teraz jako Administrator zabezpieczeń.

    b.  Kliknij pozycję **ustawienia** w prawym górnym rogu, a następnie przejdź do **Zarządzanie użytkownikami**.

    ![Schemat organizacyjny teraz ustawienia](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Kliknij pozycję **Dodaj** i wykonaj następujące czynności:

    ![Zarządzanie teraz schematu organizacyjnego](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * W **identyfikator użytkownika** polu tekstowym wprowadź identyfikator użytkownika, takich jak **brittasimon\@contoso.com**.

    * W **adres E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak **brittasimon\@contoso.com**.

    * Kliknij pozycję **Add** (Dodaj).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka teraz schematu organizacyjnego w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze teraz schematu organizacyjnego, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

