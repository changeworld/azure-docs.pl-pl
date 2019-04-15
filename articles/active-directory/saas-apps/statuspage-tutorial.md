---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Strona stanu | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Strona stanu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 49f77da5843bc2438ea7f82475b4faf753b66f09
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565210"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Strona stanu

W tym samouczku dowiesz się, jak zintegrować Strona stanu z usługą Azure Active Directory (Azure AD).
Integrowanie Strona stanu z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Strona stanu.
* Aby umożliwić użytkownikom można automatycznie zalogował się Strona stanu (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Strona stanu, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Strona stanu rejestracji jednokrotnej włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Strona stanu obsługuje **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-statuspage-from-the-gallery"></a>Strona dodawania stanu z galerii

Aby skonfigurować integrację Strona stanu w usłudze Azure AD, należy dodać Strona stanu z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Strona stanu z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Strona stanu**, wybierz opcję **Strona stanu** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Strona stanu na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Strona stanu, w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Strona stanu musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Strona stanu, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie Strona stanu rejestracji jednokrotnej](#configure-statuspage-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego Strona stanu](#create-statuspage-test-user)**  — aby odpowiednikiem Britta Simon w Strona stanu, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Strona stanu, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Strona stanu** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Strona stanu domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/`|
    | `https://<subdomain>.statuspage.io/`|

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume`|
    | `https://<subdomain>.statuspage.io/sso/saml/consume`|

    > [!NOTE]
    > Skontaktuj się z zespołem pomocy technicznej Strona stanu na [ SupportTeam@statuspage.io ](mailto:SupportTeam@statuspage.io)żądanie metadanych niezbędne do skonfigurowania logowania jednokrotnego. 
    >
    > a. Skopiuj wartość wystawcy z metadanych, a następnie wklej go do **identyfikator** pola tekstowego.
    >
    > b. Z metadanych, skopiuj adres URL odpowiedzi, a następnie wklej go do **adres URL odpowiedzi** pola tekstowego.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie Strona stanu** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-statuspage-single-sign-on"></a>Konfigurowanie Strona stanu rejestracji jednokrotnej

1. W innym oknie przeglądarki Zaloguj się w witrynie firmy Strona stanu jako administrator.

1. Na głównym pasku narzędzi kliknij polecenie **Zarządzaj kontem**.

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Kliknij przycisk **logowania jednokrotnego** kartę.

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Na stronie ustawień logowania jednokrotnego wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. W **logowania jednokrotnego, docelowy adres URL** pola tekstowego, Wklej wartość **adres URL logowania**, który skopiowano z witryny Azure portal.

    b. Otwórz pobranego certyfikatu w programie Notatnik, skopiuj zawartość, a następnie wklej go do **certyfikatu** pola tekstowego.

    c. Kliknij przycisk **Zapisywanie konfiguracji**.

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

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Strona stanu.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Strona stanu**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Strona stanu**.

    ![Strona stanu łącze na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-statuspage-test-user"></a>Tworzenie użytkownika testowego Strona stanu

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Strona stanu.

Strona stanu obsługę just-in-time. Została już włączona w [usługi Azure AD Konfigurowanie logowania jednokrotnego](#configure-azure-ad-single-sign-on).

**Aby utworzyć użytkownika o nazwie Britta Simon w Strona stanu, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Strona stanu jako administrator.

1. W menu u góry kliknij **Zarządzaj kontem**.

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Kliknij przycisk **członków zespołu** kartę.
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Kliknij przycisk **członek zespołu Dodaj**.
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Typ **adres E-mail**, **imię**, i **nazwisko** z prawidłowym użytkownikiem, który chcesz aprowizować do powiązanych pól tekstowych. 

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Jako **roli**, wybierz **Administrator klienta**.

1. Kliknij przycisk **Utwórz konto**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Strona stanu, w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Strona stanu, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
