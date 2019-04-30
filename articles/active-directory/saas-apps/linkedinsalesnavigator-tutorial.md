---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą narzędzia LinkedIn Sales Navigator | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i narzędzia LinkedIn Sales Navigator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2153d4a1e6c6c24c8177e0be1481eaa4e27d2e6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128774"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą narzędzia LinkedIn Sales Navigator

W tym samouczku dowiesz się, jak zintegrować narzędzia LinkedIn Sales Navigator w usłudze Azure Active Directory (Azure AD).
Integrowanie narzędzia LinkedIn Sales Navigator z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do narzędzia LinkedIn Sales Navigator.
* Użytkownikom można automatycznie zalogowany do narzędzia LinkedIn Sales Navigator (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą narzędzia LinkedIn Sales Navigator, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Narzędzia LinkedIn Sales Navigator logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje narzędzia LinkedIn Sales Navigator **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez

* Obsługuje narzędzia LinkedIn Sales Navigator **Just In Time** aprowizacji użytkowników

* Obsługuje narzędzia LinkedIn Sales Navigator [ **automatyczne** aprowizacji użytkowników](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Dodawania narzędzia LinkedIn Sales Navigator z galerii

Aby skonfigurować integrację z narzędzia LinkedIn Sales Navigator w usłudze Azure AD, należy dodać narzędzia LinkedIn Sales Navigator z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać narzędzia LinkedIn Sales Navigator z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **narzędzia LinkedIn Sales Navigator**, wybierz opcję **narzędzia LinkedIn Sales Navigator** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Narzędzia LinkedIn Sales Navigator na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą narzędzia LinkedIn Sales Navigator w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w serwisie LinkedIn Sales Navigator musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą narzędzia LinkedIn Sales Navigator, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie usługi LinkedIn Sales Navigator logowania jednokrotnego](#configure-linkedin-sales-navigator-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego narzędzia LinkedIn Sales Navigator](#create-linkedin-sales-navigator-test-user)**  — aby odpowiednikiem Britta Simon w narzędzia LinkedIn Sales Navigator połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z narzędzia LinkedIn Sales Navigator, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **narzędzia LinkedIn Sales Navigator** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![LinkedIn Sales Navigator domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W **identyfikator** tekstu wprowadź **identyfikator jednostki** wartość będzie skopiuj wartość Identyfikatora jednostki z portalu usługi Linkedin wyjaśnione w dalszej części tego samouczka.

    b. W **adres URL odpowiedzi** tekstu wprowadź **adresu Url asercji klienta dostępu (ACS)** wartości, zostanie skopiowany adres Url asercji klienta dostępu (ACS) wartość z portalu usługi Linkedin wyjaśnione w dalszej części tego samouczka.

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![LinkedIn Sales Navigator domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

6. Narzędzia LinkedIn Sales Navigator aplikacja oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Narzędzia LinkedIn Sales Navigator aplikacja oczekuje nameidentifier mają być mapowane z **user.mail**, więc trzeba edytować mapowanie atrybutów, klikając ikonę edycji i zmienić mapowanie atrybutu.

    ![image](common/edit-attribute.png)

7. Ponadto powyżej narzędzia LinkedIn Sales Navigator aplikacja oczekuje kilka więcej atrybutów, które mają być przekazywane w odpowiedzi SAML. W sekcji oświadczenia użytkowników na **atrybutów użytkownika** okna dialogowego, wykonaj następujące kroki, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Name (Nazwa) | Atrybut źródłowy|
    | --- | --- |
    | email| user.mail |
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

9. Na **Konfigurowanie narzędzia LinkedIn Sales Navigator** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-linkedin-sales-navigator-single-sign-on"></a>Konfigurowanie usługi LinkedIn Sales Navigator logowania jednokrotnego

1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **narzędzia LinkedIn Sales Navigator** witryny sieci Web jako administrator.

1. W **Centrum kont** kliknij przycisk **Ustawienia globalne** w obszarze **Ustawienia**. Zaznacz również **Sales Navigator** z listy rozwijanej.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Kliknij pozycję **lub kliknij tutaj, aby załadować i skopiuj poszczególne pola w formularzu** i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Kopiuj **identyfikator jednostki** i wklej go w **identyfikator** polu tekstowym **podstawową konfigurację protokołu SAML** w witrynie Azure portal.

    b. Kopiuj **adresu Url asercji klienta dostępu (ACS)** i wklej go w **adres URL odpowiedzi** polu tekstowym **podstawową konfigurację protokołu SAML** w witrynie Azure portal.

1. Przejdź do sekcji **LinkedIn Admin Settings** (Ustawienia administratora LinkedIn). Przekaż plik XML, który został pobrany z witryny Azure portal, klikając **pliku XML Przekaż** opcji.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Kliknij przycisk **On** (Włącz), aby włączyć funkcję logowania jednokrotnego. Stan funkcji logowania jednokrotnego zmieni się z **Not Connected** (Niepołączona) na **Connected** (Połączona)

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

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

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do narzędzia LinkedIn Sales Navigator.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **narzędzia LinkedIn Sales Navigator**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **narzędzia LinkedIn Sales Navigator**.

    ![Link narzędzia LinkedIn Sales Navigator na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-linkedin-sales-navigator-test-user"></a>Tworzenie użytkownika testowego narzędzia LinkedIn Sales Navigator

Połączone Sales Navigator aplikacji obsługuje tylko w aprowizacji użytkowników czas (JIT) i uwierzytelnianie użytkowników są tworzone automatycznie w aplikacji. Aktywuj **automatycznie przypisywać licencje** Aby przypisać licencję do użytkownika.

   ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka narzędzia LinkedIn Sales Navigator w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze narzędzia LinkedIn Sales Navigator, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

