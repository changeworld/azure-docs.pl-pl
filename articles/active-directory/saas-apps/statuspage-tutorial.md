---
title: 'Samouczek: Integracja usługi Azure Active Directory ze statuspage | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a statuspage.
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
ms.openlocfilehash: d947f610e6a753ce2ed349917640b07a55bbb735
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089872"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Samouczek: Integracja usługi Azure Active Directory ze statuspage

W tym samouczku dowiesz się, jak zintegrować statuspage z usługą Azure Active Directory (Azure AD).
Integracja statuspage z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do statuspage.
* Można włączyć użytkowników, aby automatycznie zalogować się do StatusPage (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD ze statuspage, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną rejestracją jednokrotną StatusPage

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* StatusPage obsługuje zainicjowane przez protokół SSO inicjowane przez **protokół** IDP

## <a name="adding-statuspage-from-the-gallery"></a>Dodawanie strony statusowej z galerii

Aby skonfigurować integrację statuspage do usługi Azure AD, należy dodać StatusPage z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać stronę statusu z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **StatusPage**, wybierz **opcję StatusPage** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![StatusPage na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą StatusPage na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w statuspage.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą strony statusowej, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj jednokrotne logowanie statuspage](#configure-statuspage-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego StatusPage](#create-statuspage-test-user)** — aby mieć odpowiednik Britta Simon w StatusPage, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą strony statusowej, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **StatusPage** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![StatusPage Domeny i adresy URL — informacje o logowaniem jednokrotnym](common/idp-intiated.png)

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
    > Skontaktuj się z zespołem pomocy technicznej [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)StatusPage, aby zażądać metadanych niezbędnych do skonfigurowania logowania jednokrotnego. 
    >
    > a. Z metadanych skopiuj wartość wystawcy, a następnie wklej ją do pola tekstowego **Identyfikator.**
    >
    > b. Z metadanych skopiuj adres URL odpowiedzi, a następnie wklej go do pola tekstowego **Odpowiedz adres URL.**

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie strony stanu** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-statuspage-single-sign-on"></a>Konfigurowanie logowania jednokrotnego strony statusu

1. W innym oknie przeglądarki zaloguj się do witryny firmy StatusPage jako administrator.

1. Na głównym pasku narzędzi kliknij pozycję **Zarządzaj kontem**.

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Kliknij kartę **Logowanie jednokrotne.**

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Na stronie Ustawienia samego systemu syłk wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. W polu tekstowym **docelowy identyfikatora SSO** wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    b. Otwórz pobrany certyfikat w Notatniku, skopiuj zawartość, a następnie wklej go do pola tekstowego **Certyfikat.**

    d. Kliknij **pozycję ZAPISZ KONFIGURACJĘ**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension`  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do StatusPage.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **StatusPage**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **StatusPage**.

    ![Łącze StatusPage na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-statuspage-test-user"></a>Utwórz użytkownika testowego StatusPage

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w StatusPage.

StatusPage obsługuje just-in-time inicjowania obsługi administracyjnej. Włączono go już w [konfigurowaniu logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on).

**Aby utworzyć użytkownika o nazwie Britta Simon w StatusPage, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy StatusPage jako administrator.

1. W menu u góry kliknij pozycję **Zarządzaj kontem**.

    ![Konfigurowanie logowania jednokrotnego](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Kliknij kartę **Członkowie zespołu.**
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Kliknij **pozycję DODAJ CZŁONKA ZESPOŁU**.
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Wpisz **adres e-mail**, **imię**i **nazwisko** prawidłowego użytkownika, którego chcesz udostępnić w powiązanych polach tekstowych. 

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Jako **rola**wybierz **administratora klienta**.

1. Kliknij **pozycję UTWÓRZ KONTO**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka StatusPage w Panelu dostępu należy automatycznie zalogować się do strony stanu, dla której skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
