---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Mozy Enterprise | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: a0f21165af0bcbd8bda28f0eae20d3ee837f3be9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275661"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Mozy Enterprise

W tym samouczku dowiesz się, jak zintegrować Mozy przedsiębiorstwa za pomocą usługi Azure Active Directory (Azure AD).
Integracja przedsiębiorstwa Mozy z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Mozy przedsiębiorstwa.
* Użytkownikom można automatycznie zalogowany do Mozy Enterprise (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Mozy Enterprise, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Mozy Enterprise logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Mozy Enterprise **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Dodawanie Mozy przedsiębiorstwa z galerii

Aby skonfigurować integrację Mozy przedsiębiorstwa w usłudze Azure AD, należy dodać Mozy przedsiębiorstwa z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Mozy przedsiębiorstwa z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Mozy Enterprise**, wybierz opcję **Mozy Enterprise** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Mozy Enterprise na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i test usługi Azure AD logowanie jednokrotne z usługą Mozy Enterprise w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w przedsiębiorstwie Mozy musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z usługą Mozy Enterprise, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie Mozy logowania jednokrotnego dla firm](#configure-mozy-enterprise-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego Mozy Enterprise](#create-mozy-enterprise-test-user)**  — aby odpowiednikiem Britta Simon w przedsiębiorstwie Mozy, które jest połączone z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowanie jednokrotne z usługą Mozy Enterprise, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Mozy Enterprise** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Mozy Enterprise domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej Mozy Enterprise Client](http://support.mozy.com/) można uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie Mozy Enterprise** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-mozy-enterprise-single-sign-on"></a>Konfigurowanie Mozy logowania jednokrotnego dla firm

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Mozy przedsiębiorstwa, jako administrator.

2. W **konfiguracji** kliknij **zasady uwierzytelniania**.
   
    ![Zasady uwierzytelniania](./media/mozy-enterprise-tutorial/ic777314.png "zasady uwierzytelniania")

3. Na **zasady uwierzytelniania** sekcji, wykonaj następujące czynności:
   
    ![Zasady uwierzytelniania](./media/mozy-enterprise-tutorial/ic777315.png "zasady uwierzytelniania")
   
    a. Wybierz **usługi katalogowej** jako **dostawcy**.
   
    b. Wybierz **użyć wypychania LDAP**.
   
    c. Kliknij kartę **SAML Authentication** (Uwierzytelnianie SAML).
   
    d. Wklej **adres URL logowania**, który skopiowano z witryny Azure portal do **adres URL uwierzytelniania** pola tekstowego.
   
    e. Wklej **usługi Azure AD identyfikator**, który skopiowano z witryny Azure portal do **punktem końcowym SAML** pole tekstowe.
   
    f. Otwórz pobrany certyfikat zakodowany base-64 w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej cały certyfikat do **certyfikat SAML** pola tekstowego.
   
    g. Wybierz **włączyć logowanie Jednokrotne dla grupy Administratorzy logowanie się przy użyciu swoich poświadczeń sieciowych**.
   
    h. Kliknij przycisk **Save Changes** (Zapisz zmiany).

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

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Mozy przedsiębiorstwa.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Mozy Enterprise**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Mozy Enterprise**.

    ![Link Mozy przedsiębiorstwa, na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-mozy-enterprise-test-user"></a>Tworzenie użytkownika testowego Mozy przedsiębiorstwa

Aby umożliwić użytkownikom usługi Azure AD zalogować się do przedsiębiorstwa Mozy, musi być obsługiwana w przedsiębiorstwie Mozy. W przypadku przedsiębiorstw Mozy aprowizacji to zadanie ręczne.

>[!NOTE]
>Można użyć jakichkolwiek innych przedsiębiorstw Mozy użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez przedsiębiorstwa Mozy do aprowizacji kont użytkowników usługi AAD.

**Aby aprowizować konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Mozy Enterprise** dzierżawy.

2. Kliknij przycisk **użytkowników**, a następnie kliknij przycisk **Dodaj nowego użytkownika**.
   
    ![Użytkownicy](./media/mozy-enterprise-tutorial/ic777317.png "Użytkownicy")
   
    >[!NOTE]
    >**Dodaj nowego użytkownika** tylko wtedy, gdy tylko jest wyświetlana opcja **Mozy** jest wybrany jako dostawcę, w obszarze **zasady uwierzytelniania**. Jeśli skonfigurowano uwierzytelnianie SAML, następnie użytkownicy są automatycznie dodawane podczas ich pierwszego logowania za pomocą logowania jednokrotnego na.
    
3. W oknie dialogowym Nowy użytkownik należy wykonać następujące czynności:
   
    ![Dodawanie użytkowników](./media/mozy-enterprise-tutorial/ic777318.png "Dodawanie użytkowników")
   
    a. Z **wybierz grupę** , wybierz grupę na liście.
   
    b. Z **jakiego rodzaju użytkownika** , wybierz typ na liście.
   
    c. W **Username** polu tekstowym wpisz nazwę użytkownika usługi Azure AD.
   
    d. W **E-mail** pole tekstowe, wpisz adres e-mail użytkownika usługi Azure AD.
   
    e. Wybierz **Wyślij wiadomość e-mail z instrukcją użytkownika**.
   
    f. Kliknij przycisk **Dodaj użytkowników**.

     >[!NOTE]
     > Po utworzeniu użytkownika zostanie wysłana wiadomość e-mail do użytkownika usługi Azure AD, która zawiera link do potwierdzenia konta, zanim stanie się aktywny.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Mozy przedsiębiorstwa, w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Mozy Enterprise, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

