---
title: 'Samouczek: Integracja usługi Azure Active Directory z pakietem życia SilkRoad | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i SilkRoad okres zestaw.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: b5aeb874f86c9283d1ff869c9963e109aeb88112
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59697332"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Samouczek: Integracja usługi Azure Active Directory z pakietem życia SilkRoad

W tym samouczku dowiesz się, jak zintegrować SilkRoad życia Suite z usługą Azure Active Directory (Azure AD).
Integrowanie SilkRoad życia Suite z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do zestawu życia SilkRoad.
* Użytkownikom można automatycznie zalogowany do zestawu życia SilkRoad (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pakietem życia SilkRoad, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* SilkRoad okres zestaw logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje zestaw życia SilkRoad **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Dodawanie pakietu życia SilkRoad z galerii

Aby skonfigurować integrację SilkRoad życia pakietu w usłudze Azure AD, należy dodać SilkRoad okres zestaw z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać pakiet życia SilkRoad z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SilkRoad okres zestaw**, wybierz opcję **SilkRoad okres zestaw** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SilkRoad okres zestaw na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą zestawu życia SilkRoad w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w zestawie życia SilkRoad musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą SilkRoad okres zestaw, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie SilkRoad okres zestaw logowania jednokrotnego](#configure-silkroad-life-suite-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego SilkRoad okres zestaw](#create-silkroad-life-suite-test-user)**  — aby odpowiednikiem Britta Simon w SilkRoad okres zestaw, który jest połączony z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowanie jednokrotne z pakietem życia SilkRoad, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **SilkRoad okres zestaw** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    > [!NOTE]
    > Zostanie wyświetlony **plik metadanych usługodawcy** wyjaśnione w dalszej części tego samouczka.

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![image](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![image](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych **identyfikator** i **adres URL odpowiedzi** wartości Uzyskaj automatycznie wypełnione w sekcji konfiguracji podstawowej SAML:

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > Jeśli wartości **Identyfikator** i **Adres URL odpowiedzi** nie zostaną automatycznie wypełnione, wpisz te wartości ręcznie zgodnie z wymaganiami.

    d. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.silkroad-eng.com/Authentication/`

5. Na **podstawową konfigurację protokołu SAML** sekcji, jeśli nie masz **plik metadanych usługodawcy**, wykonaj następujące czynności:

    ![SilkRoad okres zestaw domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP`|
    | `https://<subdomain>.silkroad.com/Authentication/SP`|

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/`|
    | `https://<subdomain>.silkroad.com/Authentication/`|

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej SilkRoad życia pakietu klienta](https://www.silkroad.com/locations/) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. Na **Konfigurowanie SilkRoad okres zestaw** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Konfigurowanie SilkRoad okres zestaw logowania jednokrotnego

1. Zaloguj się do witryny firmy SilkRoad jako administrator.

    > [!NOTE]
    > Aby uzyskać dostęp do aplikacji SilkRoad okres zestaw uwierzytelniania dla Konfigurowanie Federacji przy użyciu usługi Microsoft Azure AD, skontaktuj się z SilkRoad pomocy technicznej lub Twoim przedstawicielem SilkRoad usług.

1. Przejdź do **usługodawcy**, a następnie kliknij przycisk **szczegóły Federacji**.

    ![Usługa Azure AD logowanie jednokrotne](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Kliknij przycisk **pobierania metadanych Federacji**, a następnie zapisz plik metadanych na tym komputerze. Użyj pobrać metadanych Federacji jako **plik metadanych dostawcy usług** w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    ![Usługa Azure AD logowanie jednokrotne](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. W swojej **SilkRoad** aplikacji, kliknij przycisk **źródeł uwierzytelniania**.

    ![Usługa Azure AD logowanie jednokrotne](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Kliknij przycisk **Dodaj źródło uwierzytelniania**.

    ![Usługa Azure AD logowanie jednokrotne](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. W **Dodaj źródło uwierzytelniania** sekcji, wykonaj następujące czynności:

    ![Usługa Azure AD logowanie jednokrotne](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. W obszarze **opcja 2 — plik metadanych**, kliknij przycisk **Przeglądaj** można przekazać pliku metadanych pobranych z witryny Azure portal.
  
    b. Kliknij przycisk **utworzyć dostawcy tożsamości przy użyciu danych pliku**.

1. W **źródeł uwierzytelniania** kliknij **Edytuj**.

    ![Usługa Azure AD logowanie jednokrotne](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. Na **Edytuj źródło uwierzytelniania** okno dialogowe, należy wykonać następujące czynności:

    ![Usługa Azure AD logowanie jednokrotne](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Jako **włączone**, wybierz opcję **tak**.

    b. W **EntityId** pola tekstowego, Wklej wartość **usługi Azure AD identyfikator** skopiowanej w witrynie Azure portal.

    c. W **opis dostawcy tożsamości** pole tekstowe, wpisz opis dla danej konfiguracji (na przykład: *Logowania jednokrotnego usługi Azure AD*).

    d. W **plik metadanych** polu tekstowym Przekaż **metadanych** pliku, który został pobrany z witryny Azure portal.
  
    e. W **Nazwa dostawcy tożsamości** polu tekstowym wpisz nazwę, które są specyficzne dla konfiguracji (na przykład: *Azure SP*).
  
    f. W **adres URL usługi wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    g. W **adres URL logowania jednokrotnego usługi** pola tekstowego, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.

    h. Kliknij pozycję **Zapisz**.

1. Wyłącz wszystkie inne źródła uwierzytelniania.

    ![Usługa Azure AD logowanie jednokrotne](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

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

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do zestawu życia SilkRoad za pomocą platformy Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **SilkRoad okres zestaw**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **SilkRoad okres zestaw**.

    ![Link SilkRoad okres zestaw na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-silkroad-life-suite-test-user"></a>Tworzenie użytkownika testowego SilkRoad okres zestaw

W tej sekcji utworzysz użytkownika o nazwie Britta Simon SilkRoad życia pakietu. Praca z [zespołem pomocy technicznej SilkRoad życia pakietu klienta](https://www.silkroad.com/locations/) Aby dodać użytkowników na platformie SilkRoad okres zestaw. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SilkRoad okres zestaw w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Suite życia SilkRoad, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
