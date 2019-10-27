---
title: 'Samouczek: integracja Azure Active Directory z usługą OpenAthens | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81e37e78246a8c021cfdea015a9c4fe7695f5e44
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933983"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Samouczek: integracja Azure Active Directory z usługą OpenAthens

Z tego samouczka dowiesz się, jak zintegrować usługę OpenAthens z usługą Azure Active Directory (Azure AD).
Zintegrowanie usługi OpenAthens z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi OpenAthens.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do usługi OpenAthens (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą OpenAthens, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi OpenAthens z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa OpenAthens obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

* Usługa OpenAthens obsługuje aprowizację użytkowników typu **Just In Time**

## <a name="adding-openathens-from-the-gallery"></a>Dodawanie usługi OpenAthens z galerii

Aby skonfigurować integrację usługi OpenAthens z usługą Azure AD, musisz dodać usługę OpenAthens z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać usługę OpenAthens z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **OpenAthens**, wybierz pozycję **OpenAthens** z panelu wyników i kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![OpenAthens na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z usługą OpenAthens, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem usługi OpenAthens.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z usługą OpenAthens, należy ukończyć poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w usłudze OpenAthens](#configure-openathens-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego usługi OpenAthens](#create-openathens-test-user)** — aby mieć w usłudze OpenAthens odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w usłudze OpenAthens, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **OpenAthens** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

5. W sekcji **Podstawowa konfiguracja protokołu SAML** przekaż **plik metadanych dostawcy usług**, dla którego kroki zostały wymienione w dalszej części tego samouczka.

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie metadanych usługi Openathens](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![Przeglądanie i przekazywanie metadanych usługi Openathens](common/browse-upload-metadata.png)

    d. Po pomyślnym przekazaniu pliku metadanych wartość **Identyfikator** zostanie automatycznie wypełniona w polu tekstowym sekcji **Podstawowa konfiguracja protokołu SAML**:

    ![Domena i adresy URL usługi OpenAthens — informacje o logowaniu jednokrotnym](common/idp-identifier.png)

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w usłudze OpenAthens

1. W innym oknie przeglądarki internetowej zaloguj się do swojej firmowej witryny usługi OpenAthens jako administrator.

2. Wybierz pozycję **Connections** (Połączenia) z listy na karcie **Management** (Zarządzanie). 

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. Wybierz pozycję **SAML 1.1/2.0**, a następnie wybierz przycisk **Configure** (Konfiguruj).

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. W celu dodania konfiguracji wybierz przycisk **Browse** (Przeglądaj), aby przekazać plik xml metadanych pobrany z witryny Azure Portal, a następnie wybierz przycisk **Add** (Dodaj).

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. Wykonaj poniższe kroki na karcie **Details** (Szczegóły).

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. W obszarze **Display name mapping** (Mapowanie nazwy wyświetlanej) wybierz opcję **Use attribute** (Użyj atrybutu).

    b. W polu tekstowym **Display name attribute** (Atrybut nazwy wyświetlanej) wprowadź wartość `http://schemas.microsoft.com/identity/claims/displayname`.
    
    d. W obszarze **Unique user mapping** (Mapowanie unikatowego użytkownika) wybierz opcję **Use attribute** (Użyj atrybutu).

    d. W polu tekstowym **Unique user attribute** (Atrybut unikatowego użytkownika) wprowadź wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. W obszarze **Status** (Stan) zaznacz wszystkie trzy pola wyboru.

    f. W obszarze **Create local accounts** (Tworzenie kont lokalnych) wybierz opcję **automatically** (automatycznie).

    g. Wybierz pozycję **Save changes** (Zapisz zmiany).

    h. Z karty **</> Relying Party** (</> Jednostka uzależniona) skopiuj adres **Metadata URL** (Adres URL metadanych) i otwórz go w przeglądarce, aby pobrać plik **XML metadanych dostawcy usług**. Przekaż ten plik metadanych dostawcy usług w sekcji **Podstawowa konfiguracja protokołu SAML** w usłudze Azure AD.

    ![Konfigurowanie logowania jednokrotnego](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon\@yourcompanydomain. Extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do usługi OpenAthens.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **OpenAthens**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz **OpenAthens**.

    ![Link usługi OpenAthens na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-openathens-test-user"></a>Tworzenie użytkownika testowego usługi OpenAthens

W tej sekcji w usłudze OpenAthens jest tworzony użytkownik o nazwie Britta Simon. Usługa OpenAthens obsługuje **aprowizację użytkowników just in time**, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w usłudze OpenAthens, zostanie utworzony po uwierzytelnieniu.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka OpenAthens w panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi OpenAthens, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

