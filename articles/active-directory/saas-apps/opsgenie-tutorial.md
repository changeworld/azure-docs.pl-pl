---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą OpsGenie | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 446ac54d84f7b2b3bf3aaf6eaf5536f0dfb804fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095757"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Samouczek: Integracja usługi Azure Active Directory z usługą OpsGenie

W tym samouczku dowiesz się, jak zintegrować OpsGenie z usługą Azure Active Directory (Azure AD).
Integracja usługi OpsGenie z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do OpsGenie.
* Można włączyć użytkowników, aby automatycznie zalogować się do OpsGenie (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą OpsGenie, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego OpsGenie

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* OpsGenie obsługuje sso inicjowane przez **SP**

## <a name="adding-opsgenie-from-the-gallery"></a>Dodawanie OpsGenie z galerii

Aby skonfigurować integrację OpsGenie z usługą Azure AD, należy dodać OpsGenie z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać OpsGenie z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **OpsGenie**, wybierz **OpsGenie** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![OpsGenie na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z OpsGenie na podstawie użytkownika testowego o nazwie **B. Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w OpsGenie.

Aby skonfigurować i przetestować usługę Azure AD jednokrotnego logowania za pomocą OpsGenie, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne OpsGenie](#configure-opsgenie-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby włączyć B. Simon do korzystania z usługi Azure AD logowania jednokrotnego.
5. **[Utwórz użytkownika testowego OpsGenie](#create-opsgenie-test-user)** — aby mieć odpowiednik B. Simon w OpsGenie, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą usługi OpsGenie, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **OpsGenie** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o logach jednokrotnych domeny i adresów URL OpsGenie](common/sp-signonurl.png)

    W polu **tekstowym "Podpisywanie adresu URL"** wpisz adres URL:`https://app.opsgenie.com/auth/login`

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

6. W sekcji **Konfigurowanie operacji OpsGenie** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-opsgenie-single-sign-on"></a>Konfigurowanie logowania jednokrotnego operacji OpsGenie

1. Otwórz inne wystąpienie przeglądarki, a następnie zaloguj się do OpsGenie jako administrator.

2. Kliknij **pozycję Ustawienia**, a następnie kliknij kartę Znak **jednokrotny na.**
   
    ![Logowanie jednokrotne OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

3. Aby włączyć funkcję SSO, wybierz pozycję **Włączone**.
   
    ![Ustawienia OperacjiGenie](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

4. W sekcji **Dostawca** kliknij kartę **Usługi Azure Active Directory.**
   
    ![Ustawienia OperacjiGenie](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

5. Na stronie okna dialogowego usługi Azure Active Directory wykonaj następujące kroki:
   
    ![Ustawienia OperacjiGenie](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. W polu tekstowym **SAML 2.0 Endpoint** wklej wartość **adresu URL logowania**skopiowaną z witryny Azure portal.
    
    b. W polu **tekstowym Adres url metadanych:** wklej wartość **adresu URL metadanych federacji aplikacji,** która została skopiowana z witryny Azure portal.
    
    d. Kliknij **pozycję Zapisz zmiany**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

Celem tej sekcji jest utworzenie użytkownika testowego w witrynie Azure portal o nazwie B. Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **B. Simon**.
  
    b. W polu **Nazwa użytkownika** wpisz **bsimon@yourcompanydomain.extension**  
    Na przykład: BSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć B. Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do OpsGenie.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz **opsGenie**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **OpsGenie**.

    ![Łącze OpsGenie na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję B. Simon** na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-opsgenie-test-user"></a>Tworzenie użytkownika testowego OpsGenie

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w OpsGenie. 

1. W oknie przeglądarki internetowej zaloguj się do dzierżawy OpsGenie jako administrator.

2. Przejdź do listy Użytkownicy, klikając pozycję **Użytkownicy** w lewym panelu.
   
    ![Ustawienia OperacjiGenie](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Kliknij **pozycję Dodaj użytkownika**.

4. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:
   
    ![Ustawienia OperacjiGenie](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
    a. W polach **tekstowych Poczta e-mail** wpisz adres e-mail B. Simon adresowany w usłudze Azure Active Directory.
   
    b. W polach **tekstowych Imię i nazwisko** wpisz **B. Simon**.
   
    d. Kliknij przycisk **Zapisz**. 

>[!NOTE]
>B. Simon otrzymuje wiadomość e-mail z instrukcjami dotyczącymi konfigurowania profilu.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka OpsGenie w Panelu dostępu należy automatycznie zalogować się do opsGenie, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

