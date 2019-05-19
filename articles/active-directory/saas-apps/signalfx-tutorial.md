---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą SignalFx | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 6f742def0441b5ae18ad9da3a8ac9d280de8f824
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65867391"
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą SignalFx

W tym samouczku dowiesz się, jak zintegrować SignalFx w usłudze Azure Active Directory (Azure AD).
Integrowanie SignalFx z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do SignalFx.
* Aby umożliwić użytkownikom można automatycznie zalogowany do SignalFx (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą SignalFx, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* SignalFx logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje SignalFx **tożsamości** jednokrotne logowanie inicjowane przez
* Obsługuje SignalFx **Just In Time** aprowizacji użytkowników

## <a name="adding-signalfx-from-the-gallery"></a>Dodawanie SignalFx z galerii

Aby skonfigurować integrację SignalFx w usłudze Azure AD, należy dodać SignalFx z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać SignalFx z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SignalFx**, wybierz opcję **SignalFx** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![SignalFx na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą SignalFx w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w SignalFx musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą SignalFx, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie SignalFx logowania jednokrotnego](#configure-signalfx-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego SignalFx](#create-signalfx-test-user)**  — aby odpowiednikiem Britta Simon w SignalFx połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z SignalFx, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **SignalFx** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![SignalFx domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `https://api.signalfx.com/v1/saml/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE]
    > Podaną wcześniej wartością nie jest rzeczywistą wartość. Wartość należy zaktualizować przy użyciu rzeczywistego adresu URL odpowiedzi, które wyjaśniono w dalszej części tego samouczka.

5. Aplikacja SignalFx oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności: 

    | Name (Nazwa) |  Atrybut źródłowy|
    | ------------------- | -------------------- |
    | User.FirstName     | user.givenname |
    | User.email          | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

8. Na **Konfigurowanie SignalFx** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-signalfx-single-sign-on"></a>Konfigurowanie SignalFx logowanie jednokrotne

1. Zaloguj się do witryny firmy SignalFx jako administrator.

1. W SignalFx po kliknięciu najważniejsze **integracje** aby otworzyć stronę integracji.

    ![SignalFx Integration](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. Kliknij pozycję **usługi Azure Active Directory** kafelka w obszarze **logowania usługi** sekcji.

    ![SignalFx saml](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. Kliknij pozycję **nową opcję integracji z** i w obszarze **zainstalować** karcie należy wykonać następujące czynności:

    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. W **nazwa** typu pole tekstowe, nazwę nowej integracji, takich jak **logowania jednokrotnego SAML OurOrgName**.

    b. Kopiuj **identyfikator integracji** wartości i Dołącz do **adres URL odpowiedzi** zamiast z `<integration ID>` w **adres URL odpowiedzi** pole tekstowe z **podstawową konfigurację protokołu SAML**  sekcji w witrynie Azure portal.

    c. Kliknij pozycję **Przekaż plik** do przekazania **certyfikatu szyfrowania Base64** pobranego z witryny Azure portal w **certyfikatu** pola tekstowego.

    d. W **adres URL wystawcy** pola tekstowego, Wklej wartość **usługi Azure AD identyfikator**, które zostały skopiowane z witryny Azure portal.

    e. W **adres URL metadanych** pola tekstowego, Wklej **adres URL logowania** skopiowanej w witrynie Azure portal.

    f. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do SignalFx.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **SignalFx**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **SignalFx**.

    ![Link SignalFx na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-signalfx-test-user"></a>Tworzenie użytkownika testowego SignalFx

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w SignalFx. SignalFx obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu SignalFx, jeśli go jeszcze nie istnieje.

Kiedy użytkownik zaloguje się SignalFx z logowania jednokrotnego SAML po raz pierwszy [zespołem pomocy technicznej SignalFx](mailto:kmazzola@signalfx.com) wysyła do nich wiadomość e-mail zawierającą łącze, które należy kliknąć za pośrednictwem uwierzytelniania. Dzieje się tak tylko po raz pierwszy loguje się użytkownik; próby kolejne logowania nie będzie wymagać weryfikacji wiadomości e-mail.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [SignalFx zespołem pomocy technicznej](mailto:kmazzola@signalfx.com)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SignalFx w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze SignalFx, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

