---
title: 'Samouczek: Integracja usługi Azure Active Directory z pomocą techniczną Jitbit | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a pomocą techniczną Jitbit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 29addcd62afd193af83196b2d942e9778ff3f031
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099405"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Samouczek: Integracja usługi Azure Active Directory z pomocą techniczną Jitbit

W tym samouczku dowiesz się, jak zintegrować jitbit helpdesk z usługi Azure Active Directory (Azure AD).
Integracja jitbit helpdesk z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do jitbit helpdesk.
* Można włączyć użytkowników, aby automatycznie zalogować się do Jitbit Helpdesk (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pomocą techniczną Jitbit, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego Jitbit Helpdesk

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Jitbit Helpdesk obsługuje sytuowane sytuaszowe zainicjowane przez **sp**

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Dodawanie jitbit helpdesk z galerii

Aby skonfigurować integrację Jitbit Helpdesk z usługą Azure AD, należy dodać jitbit helpdesk z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać jitbit Helpdesk z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Jitbit Helpdesk**, wybierz **Jitbit Helpdesk** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Jitbit Helpdesk na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą Jitbit Helpdesk na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w pomocy technicznej Jitbit.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą pomocy Jitbit Helpdesk, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne jitbit helpdesk](#configure-jitbit-helpdesk-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Jitbit Helpdesk](#create-jitbit-helpdesk-test-user)** — aby mieć odpowiednik Britta Simon w Jitbit Helpdesk, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą pomocy technicznej Jitbit, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Jitbit Helpdesk** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Jitbit Helpdesk Domain and URL Single sign-on information Jitbit Helpdesk Domain and URL single sign-on information Jitbit Helpdesk Domain and URL](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: 
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej Jitbit Helpdesk,](https://www.jitbit.com/support/) aby uzyskać tę wartość.

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL w następujący sposób:`https://www.jitbit.com/web-helpdesk/`

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie pomocy Jitbit** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w umojówek pomocy Jitbit

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Jitbit Helpdesk jako administrator.

1. Na pasku narzędzi u góry kliknij pozycję **Administracja**.

    ![Administracja](./media/jitbit-helpdesk-tutorial/ic777681.png "Administracja")

1. Kliknij **pozycję Ustawienia ogólne**.

    ![Użytkownicy, firmy i uprawnienia](./media/jitbit-helpdesk-tutorial/ic777680.png "Użytkownicy, firmy i uprawnienia")

1. W sekcji **Konfiguracja ustawień uwierzytelniania** wykonaj następujące czynności:

    ![Ustawienia uwierzytelniania](./media/jitbit-helpdesk-tutorial/ic777683.png "Ustawienia uwierzytelniania")

    a. Wybierz **włącz jednokrotne logowanie SAML 2.0**, aby zalogować się przy użyciu logowania jednokrotnego za pomocą **funkcji OneLogin**.

    b. W polu tekstowym **URL programu EndPoint** wklej wartość **adresu URL logowania** skopiowanego z witryny Azure portal.

    d. Otwórz certyfikat **zakodowany base-64** w notatniku, skopiuj jego zawartość do schowka, a następnie wklej go do pola **tekstowego certyfikatu X.509**

    d. Kliknij **pozycję Zapisz zmiany**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, udzielając dostępu do Jitbit Helpdesk.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz pozycję **Jitbit Helpdesk**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Jitbit Helpdesk**.

    ![Łącze Jitbit Helpdesk na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-jitbit-helpdesk-test-user"></a>Tworzenie użytkownika testowego Jitbit Helpdesk

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do jitbit helpdesk, muszą one być aprowizować w Jitbit Helpdesk. W przypadku Jitbit Helpdesk inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy **pomocy technicznej Jitbit.**

1. W menu u góry kliknij pozycję **Administracja**.

    ![Administracja](./media/jitbit-helpdesk-tutorial/ic777681.png "Administracja")

1. Kliknij **pozycję Użytkownicy, firmy i uprawnienia**.

    ![Użytkownicy, firmy i uprawnienia](./media/jitbit-helpdesk-tutorial/ic777682.png "Użytkownicy, firmy i uprawnienia")

1. Kliknij **pozycję Dodaj użytkownika**.

    ![Dodaj użytkownika](./media/jitbit-helpdesk-tutorial/ic777685.png "Dodawanie użytkownika")

1. W sekcji Tworzenie wpisz dane konta usługi Azure AD, które chcesz aprowizować w następujący sposób:

    ![Utwórz](./media/jitbit-helpdesk-tutorial/ic777686.png "Utwórz")

   a. W polu tekstowym **Nazwa użytkownika** wpisz nazwę użytkownika, taką jak **BrittaSimon**.

   b. W polu tekstowym **Wiadomość e-mail** wpisz wiadomość e-mail użytkownika w stylu **BrittaSimon@contoso.com**.

   d. W polu tekstowym **Imię** wpisz imię użytkownika, takiego jak **Britta**.

   d. W polu **tekstowym Nazwisko** wpisz nazwisko użytkownika, takiego jak **Simon**.

   e. Kliknij przycisk **Utwórz**.

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure AD można użyć innych narzędzi do tworzenia kont użytkowników Jitbit Helpdesk lub interfejsów API dostarczonych przez pomoc techniczną Jitbit.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Jitbit Helpdesk w Panelu dostępu należy automatycznie zalogować się do pomocy Jitbit, dla której skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
