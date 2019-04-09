---
title: 'Samouczek: Integracja usługi Azure Active Directory z wrażenie Stany Zjednoczone (inne niż UltiPro) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i wrażenie Stany Zjednoczone (inne niż UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 4b7a9409052d8255bbad00b38217bcff030e8620
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59277021"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Samouczek: Integracja usługi Azure Active Directory z wrażenie Stany Zjednoczone (inne niż UltiPro)

W tym samouczku dowiesz się, jak zintegrować wrażenie Stany Zjednoczone (inne niż UltiPro) z usługą Azure Active Directory (Azure AD).
Integrowanie wrażenie Stany Zjednoczone (inne niż UltiPro) z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Stanów Zjednoczonych wrażenie (inne niż UltiPro).
* Aby umożliwić użytkownikom można automatycznie zalogowany do Stanów Zjednoczonych wrażenie (Non-UltiPro) (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą wrażenie Stany Zjednoczone (inne niż UltiPro), potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Wrażenie Stanów Zjednoczonych (inne niż UltiPro) logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Wrażenie Stany Zjednoczone (inne niż UltiPro) obsługuje **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Dodawanie wrażenie Stany Zjednoczone (inne niż UltiPro) z galerii

Aby skonfigurować integrację ze Stanów Zjednoczonych wrażenie (inne niż UltiPro) w usłudze Azure AD, należy dodać wrażenie Stany Zjednoczone (inne niż UltiPro) z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać wrażenie Stany Zjednoczone (inne niż UltiPro) z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **wrażenie Stany Zjednoczone (inne niż UltiPro)**, wybierz opcję **wrażenie Stany Zjednoczone (inne niż UltiPro)** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

     ![Stany Zjednoczone wrażenie (inne niż UltiPro) na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą wrażenie Stany Zjednoczone (inne niż UltiPro) w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Stanach Zjednoczonych wrażenie (inne niż UltiPro) musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą wrażenie Stany Zjednoczone (inne niż UltiPro), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie wrażenie Stany Zjednoczone (inne niż UltiPro) logowanie jednokrotne](#configure-perception-united-states-non-ultipro-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego wrażenie Stany Zjednoczone (inne niż UltiPro)](#create-perception-united-states-non-ultipro-test-user)**  — aby odpowiednikiem Britta Simon w wrażenie Stanów Zjednoczonych (inne niż UltiPro) połączoną usługę Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą wrażenie Stany Zjednoczone (inne niż UltiPro), wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **wrażenie Stany Zjednoczone (inne niż UltiPro)** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Wrażenie Stanów Zjednoczonych (inne niż UltiPro) domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL: `https://perception.kanjoya.com/sp`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. **Wrażenie Stany Zjednoczone (inne niż UltiPro)** aplikacja wymaga **usługi Azure AD identyfikator** wartość jako < entity_id >, która ma otrzymać od **ustawienia postrzeganiem pojęcia Stany Zjednoczone ( Non-UltiPro)** sekcji do zakodowania jako identyfikator URI. Aby uzyskać wartość zakodowany jako identyfikator uri, użyj następującego linku: **http://www.url-encode-decode.com/**.

    d. Po otrzymaniu identyfikatora uri zakodowaną wartość połączyć ją z **adres URL odpowiedzi** wymienionych poniżej -

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Wklej powyższą wartość w **adres URL odpowiedzi** pola tekstowego.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. Na **Ustaw się wrażenie Stany Zjednoczone (inne niż UltiPro)** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Konfigurowanie wrażenie Stanów Zjednoczonych (inne niż UltiPro) logowanie jednokrotne

1. W innym oknie przeglądarki Zaloguj się do witryny firmy Perception Stany Zjednoczone (inne niż UltiPro) jako administrator.

2. Na głównym pasku narzędzi kliknij polecenie **ustawienia konta**.

    ![Wrażenie Stanów Zjednoczonych (inne niż UltiPro) użytkownika](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Na **ustawienia konta** strony, wykonaj następujące czynności:

    ![Wrażenie Stanów Zjednoczonych (inne niż UltiPro) użytkownika](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. W **nazwa firmy** polu tekstowym wpisz nazwę **firmy**.
    
    b. W **nazwa konta** polu tekstowym wpisz nazwę **konta**.

    c. W **domyślne Odpowiedz do wiadomości E-mail** tekstu wpisz prawidłowe **E-mail**.

    d. Wybierz **logowania jednokrotnego dostawcy tożsamości** jako **SAML 2.0**.

4. Na **konfiguracji logowania jednokrotnego** strony, wykonaj następujące czynności:

    ![SSOConfig Stanów Zjednoczonych (inne niż UltiPro) wrażenie](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Wybierz **SAML NameID typu** jako **E-mail**.

    b. W **Nazwa konfiguracji logowania jednokrotnego** polu tekstowym wpisz nazwę swojej **konfiguracji**.
    
    c. W **nazwę dostawcy tożsamości** pole tekstowe, Wklej wartość **usługi Azure AD identyfikator**, które zostały skopiowane z witryny Azure portal. 

    d. W **pole tekstowe domeny SAML**, wprowadź domenę, takich jak @contoso.com.

    e. Kliknij pozycję **Przekaż ponownie** do przekazania **XML metadanych** pliku.

    f. Kliknij przycisk **Update** (Aktualizuj).

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

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Stanów Zjednoczonych wrażenie (inne niż UltiPro).

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **wrażenie Stany Zjednoczone (inne niż UltiPro)**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **wrażenie Stany Zjednoczone (inne niż UltiPro)**.

    ![Link wrażenie Stany Zjednoczone (inne niż UltiPro) na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Tworzenie użytkownika testowego wrażenie Stany Zjednoczone (inne niż UltiPro)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Stanach Zjednoczonych wrażenie (inne niż UltiPro). Praca z [zespołem pomocy technicznej wrażenie Stany Zjednoczone (inne niż UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) Aby dodać użytkowników na platformie wrażenie Stany Zjednoczone (inne niż UltiPro).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka wrażenie Stany Zjednoczone (inne niż UltiPro) w panelu dostępu, możesz powinna istnieć możliwość automatycznej rejestracji na postrzeganie Stanów Zjednoczonych (inne niż UltiPro) dla której skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

