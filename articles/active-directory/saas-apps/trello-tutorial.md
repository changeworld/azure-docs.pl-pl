---
title: 'Samouczek: integracja usługi Azure Active Directory z aplikacją Trello | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Trello.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 453827b42d12333fd4f27761e7f73484fb749532
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65865790"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Samouczek: integracja usługi Azure Active Directory z aplikacją Trello

Z tego samouczka dowiesz się, jak zintegrować aplikację Trello z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Trello z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji Trello.
* Aby umożliwić użytkownikom można automatycznie zalogowany Trello (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji: witryna Azure portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Trello potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja pojedynczego logowania na obsługujących usługi Trello.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje usługi Trello, i dostawcy tożsamości — zainicjowanego przez dostawcę usług rejestracji Jednokrotnej

* Trello obsługę Just In Time użytkownika

## <a name="add-trello-from-the-gallery"></a>Dodaj usługi Trello z galerii

Aby skonfigurować integrację usługi Trello w usłudze Azure AD, najpierw dodać Trello z galerii z listą zarządzanych aplikacji SaaS.

Aby dodać usługi Trello z galerii, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

2. Wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa](common/enterprise-applications.png)

3. Aby dodać nową aplikację, wybierz **nową aplikację** znajdujący się u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **Trello**, a następnie wybierz pozycję **Trello** w okienku wyników.

5. Wybierz **Dodaj** przycisk, aby dodać aplikację.

     ![Aplikacja Trello na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu usługi Trello w oparciu o użytkownika testu o nazwie **Britta Simon**.

Dla logowania jednokrotnego do pracy należy ustanowić łącze między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze Trello.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu usługi Trello, należy wykonać poniższe bloki konstrukcyjne:

1. [Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on) — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. [Konfigurowanie usługi Trello logowania jednokrotnego](#configure-trello-single-sign-on) do konfigurowania pojedynczego ustawień logowania jednokrotnego na stronie aplikacji.
3. [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. [Tworzenie użytkownika testowego usługi Trello](#create-a-trello-test-user) odpowiednikiem Britta Simon znajdują się w aplikacji Trello, połączonego z usługi Azure AD reprezentacja użytkownika.
6. [Przetestuj logowanie jednokrotne](#test-single-sign-on), aby sprawdzić działanie konfiguracji.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

> [!NOTE]
> Dostawca aplikacji Trello powinien dostarczyć Ci element slug **\<enterprise\>**. Jeśli nie masz wartość informacji o pracy, skontaktuj się z [zespołem pomocy technicznej usługi Trello](mailto:support@trello.com) można pobrać informacji o pracy w przedsiębiorstwie.

Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu usługi Trello, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Trello** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** wybierz opcję **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okno dialogowe.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W **podstawową konfigurację protokołu SAML** sekcji, aby skonfigurować aplikację w trybie inicjowane przez dostawcę tożsamości, wykonaj następujące czynności:

    ![Domena usługi Trello i adresy URL pojedynczy informacje logowania jednokrotnego](common/idp-intiated.png)

    a. W **identyfikator** wprowadź adres URL przy użyciu następującego wzorca: `https://trello.com/auth/saml/metadata`

    b. W **adres URL odpowiedzi** wprowadź adres URL przy użyciu następującego wzorca: `https://trello.com/auth/saml/consume/<enterprise>`

5. Wybierz **Ustaw dodatkowe adresy URL**, a następnie, jeśli chcesz skonfigurować aplikację w trybie zainicjowanego przez dostawcę usług, wykonaj następujące czynności:

    ![Domena usługi Trello i adresy URL pojedynczy informacje logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W **adres URL logowania** wprowadź adres URL przy użyciu następującego wzorca:  `https://trello.com/auth/saml/login/<enterprise>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizować te wartości z identyfikatorem rzeczywisty adres URL odpowiedzi i adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta usługi Trello](mailto:support@trello.com) do uzyskania tych wartości. Może również odnosić się do wzorców w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

6. Aplikacja usługi Trello oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na **Ustaw się logowanie jednokrotne z SAML** wybierz opcję **Edytuj** przycisk, aby otworzyć **atrybutów użytkownika** okno dialogowe.

    ![Okno dialogowe Atrybuty użytkownika](common/edit-attribute.png)

7. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na poprzedniej ilustracji. Następnie wykonaj następujące kroki:

    | Name (Nazwa) |  Atrybut źródłowy|
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. Wybierz pozycję **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Okno dialogowe oświadczenia użytkownika](common/new-save-attribute.png)

    ![Zarządzanie oświadczeniami użytkownika](common/new-attribute-details.png)

    b. W **nazwa** wprowadź nazwę atrybutu, która jest wyświetlana dla tego wiersza.

    c. Pozostaw **Namespace** puste.

    d. Aby uzyskać **źródła**, wybierz opcję **atrybutu**.

    e. W **atrybut źródłowy** listy, wprowadź wartość atrybutu, który jest wyświetlany dla tego wiersza.

    f. Wybierz przycisk **OK**.

    g. Wybierz pozycję **Zapisz**.

8. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** zaznacz **Pobierz** można pobrać **certyfikat (Base64)**  z danymi opcjami zgodnie z wymaganiami. Następnie zapisz go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

9. Na **Konfigurowanie usługi Trello** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-trello-single-sign-on"></a>Konfigurowanie usługi Trello logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne, po stronie usługi Trello, najpierw wysyłać pobrany **certyfikat (Base64)** i skopiować adresy URL w witrynie Azure portal do [zespołem pomocy technicznej usługi Trello](mailto:support@trello.com). Pozwalają zagwarantować, że połączenia logowania jednokrotnego SAML jest prawidłowo po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. W **użytkownika** okna dialogowego pole, wykonaj następujące kroki.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wprowadź "brittasimon@yourcompanydomain.extension". Na przykład, w tym przypadku możesz wprowadzić "BrittaSimon@contoso.com".

    c. Wybierz **hasło Show** pole wyboru, a następnie zanotuj tę wartość, która jest wyświetlana w **hasło** pole.

    d. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Trello.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz pozycję **Trello**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Trello**.

    ![Link usługi Trello, na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

4. Wybierz **Dodaj użytkownika** przycisku. Następnie w **Dodaj przydziału** okno dialogowe, wybierz opcję **użytkowników i grup**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy. Następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. Jeśli oczekiwane wartości roli w potwierdzenie SAML, a następnie, w **wybierz rolę** okna dialogowego wybierz odpowiednią rolę dla użytkownika z listy. Następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

7. W **Dodaj przydziału** okno dialogowe, wybierz opcję **przypisać** przycisku.

### <a name="create-a-trello-test-user"></a>Tworzenie użytkownika testowego usługi Trello

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze Trello. Trello obsługuje Just in aprowizacji użytkowników czas, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Trello, zostanie utworzony po uwierzytelnieniu.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej usługi Trello](mailto:support@trello.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji możesz przetestować konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu portalu MyApps.

Po wybraniu kafelka usługi Trello w portalu MyApps powinny być automatycznie zarejestrowaniu w usłudze Trello. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [co to jest MyApps portal?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

