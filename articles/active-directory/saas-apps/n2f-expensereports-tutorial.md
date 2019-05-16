---
title: 'Samouczek: Raporty wydatków Integracja usługi Azure Active Directory za pomocą N2F - | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i N2F — raporty wydatków.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7380cf6727817d99edbedf5552c8bea42f177074
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65744402"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Samouczek: Raporty wydatków Integracja usługi Azure Active Directory za pomocą N2F-

W tym samouczku dowiesz się, jak zintegrować N2F — raporty wydatków w usłudze Azure Active Directory (Azure AD).
Zintegrowanie N2F — raporty wydatków w usłudze Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do N2F — raporty wydatków.
* Aby umożliwić użytkownikom można automatycznie zalogowany N2F — raporty wydatków (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z N2F — raporty wydatków, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* N2F — raporty wydatków pojedynczego logowania jednokrotnego włączonych subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* N2F — obsługuje raporty wydatków **SP** i **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Dodawanie N2F — raporty wydatków z galerii

Aby skonfigurować integrację N2F - raportów wydatków w usłudze Azure AD, musisz dodać N2F - raportów z galerii z listą zarządzanych aplikacji SaaS wydatków.

**Aby dodać N2F — raporty wydatków z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **N2F — raporty wydatków**, wybierz opcję **N2F — raporty wydatków** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![N2F — raporty wydatków na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz oraz test usługi Azure AD logowanie jednokrotne za pomocą N2F — raporty wydatków użytkownika testowego w oparciu o nazwie **Britta Simon**.
Powinien być określony dla logowania jednokrotnego do pracy, relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w N2F — raporty wydatków.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą N2F — raporty wydatków, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie N2F — raporty wydatków logowania jednokrotnego](#configure-n2f---expense-reports-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Utwórz N2F — raporty wydatków testowe użytkownika](#create-n2f---expense-reports-test-user)**  — aby odpowiednikiem Britta Simon w N2F — raporty wydatków, które jest połączone z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD w logowanie jednokrotne za pomocą N2F — raporty wydatków, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **N2F — raporty wydatków** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb, użytkownik nie musi wykonywać żadnych czynności, ponieważ aplikacja jest już wstępnie zintegrowane z usługą Azure.

    ![N2F — raporty wydatków, domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/preintegrated.png)

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![N2F — raporty wydatków, domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://www.n2f.com/app/`

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/copy-metadataurl.png)

7. Na **Konfigurowanie myPolicies** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Konfigurowanie N2F — raporty wydatków logowania jednokrotnego

1. W oknie przeglądarki internetowej innej Zaloguj się do Twojej N2F - witryny firmy raportów wydatków jako administrator.

2. Kliknij pozycję **ustawienia** , a następnie wybierz **ustawienia zaawansowane** z listy rozwijanej.

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/configure1.png)

3. Wybierz **ustawienia konta** kartę.

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/configure2.png)

4. Wybierz **uwierzytelniania** , a następnie wybierz **+ Dodaj metodę uwierzytelniania** kartę.

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/configure3.png)

5. Wybierz **SAML Microsoft Office 365** jako metodę uwierzytelniania.

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/configure4.png)

6. Na **metodę uwierzytelniania** sekcji, wykonaj następujące czynności:

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/configure5.png)

    a. W **identyfikator jednostki** pola tekstowego, Wklej **usługi Azure AD identyfikator** wartości, które zostały skopiowane z witryny Azure portal.

    b. W **adres URL metadanych** pola tekstowego, Wklej **adres Url metadanych Federacji aplikacji** wartości, które zostały skopiowane z witryny Azure portal.

    c. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do N2F — raporty wydatków.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **N2F — raporty wydatków**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **N2F — raporty wydatków**.

    ![Raporty wydatków N2F - łącze na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-n2f---expense-reports-test-user"></a>Utwórz N2F — raporty wydatków testowe użytkownika

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do N2F — raporty wydatków one musi być obsługiwana w N2F — raporty wydatków. W przypadku N2F — raporty wydatków aprowizacji to zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Twojej N2F - witryny firmy raportów wydatków jako administrator.

2. Kliknij pozycję **ustawienia** , a następnie wybierz **ustawienia zaawansowane** z listy rozwijanej.

    ![N2F - wydatków Dodaj użytkownika](./media/n2f-expensereports-tutorial/configure1.png)

3. Wybierz **użytkowników** kartę z panelu nawigacyjnym po lewej stronie.

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/user1.png)

4. Wybierz **+ nowy użytkownik** kartę.

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/user2.png)

5. Na **użytkownika** sekcji, wykonaj następujące czynności:

    ![Raporty wydatków N2F — Konfiguracja](./media/n2f-expensereports-tutorial/user3.png)

    a. W **adres E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon\@contoso.com**.

    b. W polu tekstowym **First name (Imię)** wprowadź imię użytkownika, na przykład **Britta**.

    c. W polu tekstowym **Name** (Nazwa) wprowadź nazwę użytkownika, na przykład **BrittaSimon**.

    d. Wybierz **roli, Menedżer bezpośrednich (N + 1)**, i **dzielenia** zgodnie z wymaganiami organizacji.

    e. Kliknij przycisk **sprawdzania poprawności i wysłać zaproszenie**.

    > [!NOTE]
    > Jeśli podczas dodawania użytkownika występują wszelkich problemów, skontaktuj się z pomocą [N2F — raporty wydatków zespołu pomocy technicznej](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu N2F — raporty wydatków kafelka w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze N2F — raporty wydatków, dla których skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

