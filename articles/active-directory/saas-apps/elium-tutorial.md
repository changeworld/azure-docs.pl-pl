---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Elium | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b714983f1401ba60357a8642e0a56422d01be74e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103422"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją Elium

Z tego samouczka dowiesz się, jak zintegrować aplikację Elium z usługą Azure Active Directory (Azure AD).
Integracja aplikacji Elium z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do aplikacji Elium.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Elium (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z aplikacją Elium potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji Elium z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Elium obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług** oraz **dostawcę tożsamości**

* Aplikacja Elium obsługuje aprowizowanie użytkowników typu **just in time**

## <a name="adding-elium-from-the-gallery"></a>Dodawanie aplikacji Elium z galerii

Aby skonfigurować integrację aplikacji Elium z usługą Azure AD, musisz dodać aplikację Elium z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację Elium z galerii, wykonaj następujące kroki:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Elium**, wybierz pozycję **Elium** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja Elium na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją Elium, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Elium.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacją Elium, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji Elium](#configure-elium-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego aplikacji Elium](#create-elium-test-user)** — aby mieć w aplikacji Elium odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji Elium, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Elium** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Informacje o domenie i adresach URL aplikacji Elium na potrzeby logowania jednokrotnego](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<platform-domain>.elium.com/login/saml2/acs`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Informacje o domenie i adresach URL aplikacji Elium na potrzeby logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Uzyskasz te wartości z **pliku metadanych dostawcy tożsamości**, dostępnego do pobrania na stronie `https://<platform-domain>.elium.com/login/saml2/metadata`, co objaśniono w dalszej części tego samouczka.

6. Aplikacja Elium oczekuje asercji SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image](common/edit-attribute.png)

7. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności: 

    | Name (Nazwa) | Atrybut źródłowy|
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > To są oświadczenia domyślne. **Tylko oświadczenie e-mail jest wymagane**. W przypadku aprowizowania JIT również tylko oświadczenie e-mail jest obowiązkowe. Inne oświadczenia niestandardowe mogą się różnić w zależności od platformy klienta.

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

    ![Link pobierania certyfikatu](common/metadataxml.png)

9. W sekcji **Konfigurowanie aplikacji Elium** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-elium-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w aplikacji Elium

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji Elium jako administrator.

2. Kliknij pozycję **User profile** (Profil użytkownika) w prawym górnym rogu, a następnie wybierz pozycję **Administration** (Administracja).

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user1.png)

3. Wybierz kartę **Security** (Zabezpieczenia).

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user2.png)

4. Przewiń w dół do sekcji **Single sign-on (SSO)** (Logowanie jednokrotne) i wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user3.png)

    a. Skopiuj wartość pola **Verify that SAML2 authentication works for your account** (Sprawdź, czy uwierzytelnianie SAML2 działa dla Twojego konta) i wklej ją w polu tekstowym **Adres URL logowania** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    > [!NOTE]
    > Po skonfigurowaniu logowania jednokrotnego zawsze możesz uzyskać dostęp do domyślnej strony logowania zdalnego pod następującym adresem URL: `https://<platform_domain>/login/regular/login` 

    b. Zaznacz pole wyboru **Enable SAML2 federation** (Włącz federację SAML2).

    c. Zaznacz pole wyboru **JIT Provisioning** (Aprowizowanie JIT).

    d. Otwórz **metadane dostawcy usług**, klikając przycisk **Download** (Pobierz).

    e. Wyszukaj element **entityID** w pliku **metadanych dostawcy usług**, skopiuj wartość elementu **entityID**, a następnie wklej ją w polu tekstowym **Identyfikator** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal. 

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user4.png)

    f. Wyszukaj element **AssertionConsumerService** w pliku **metadanych dostawcy usług**, skopiuj wartość elementu **Location**, a następnie wklej ją w polu tekstowym **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user5.png)

    g. Otwórz w Notatniku plik metadanych pobrany z witryny Azure Portal, skopiuj jego zawartość i wklej ją w polu tekstowym **IdP Metadata** (Metadane dostawcy tożsamości).

    h. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji Elium.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, pozycję **Wszystkie aplikacje**, a następnie pozycję **Elium**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Elium**.

    ![Link aplikacji Elium na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-elium-test-user"></a>Tworzenie użytkownika testowego aplikacji Elium

W tej sekcji w aplikacji Elium jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Elium obsługuje **aprowizowanie typu just-in-time**, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Elium, zostanie utworzony podczas próby uzyskania dostępu do aplikacji Elium.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej aplikacji Elium](mailto:support@elium.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Elium w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Elium, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

