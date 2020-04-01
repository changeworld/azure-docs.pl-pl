---
title: 'Samouczek: Integracja usługi Azure Active Directory z Infinite Campus | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a infinite campus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d54769c1f3265e2cee619520044313fca46855a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100371"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Samouczek: Integracja usługi Azure Active Directory z infinite campus

W tym samouczku dowiesz się, jak zintegrować Infinite Campus z usługą Azure Active Directory (Azure AD).
Integracja infinite campus z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do Nieskończonego Kampusu.
* Można włączyć użytkowników, aby automatycznie zalogować się do Infinite Campus (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z infinite campus, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja z obsługą logowania jednokrotnego Infinite Campus
* Co najmniej musisz być administratorem usługi Azure Active Directory i mieć rolę zabezpieczeń produktu kampusu "System informacji o uczniach (SIS)", aby ukończyć konfigurację.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Infinite Campus obsługuje sso inicjowane przez **SP**

## <a name="adding-infinite-campus-from-the-gallery"></a>Dodawanie Nieskończonego Kampusu z galerii

Aby skonfigurować integrację Infinite Campus z usługą Azure AD, należy dodać Infinite Campus z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Infinite Campus z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** u góry okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Infinite Campus**, wybierz **Nieskończony kampus** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

    ![Infinite Campus na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Infinite Campus na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w infinite campus.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą infinite campus, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj nieskończone logowanie jednokrotne w kampusie](#configure-infinite-campus-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Infinite Campus](#create-infinite-campus-test-user)** — aby mieć odpowiednik Britta Simon w Infinite Campus, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą programu Infinite Campus, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Infinite Campus** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji Podstawowa konfiguracja SAML wykonaj następujące kroki (pamiętaj, że domena będzie się różnić w zależności od modelu hostingu, ale wartość **w pełni kwalifikowana domena** musi być zgodna z instalacją Infinite Campus):

    a. W polu **tekstowym Logowania adres URL** wpisz adres URL przy użyciu następującego wzorca:`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Nieskończona domena kampusu i adresy URL — informacje o logach jednokrotnych](common/sp-identifier-reply.png)

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-infinite-campus-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w nieskończonym kampusie

1. W innym oknie przeglądarki internetowej zaloguj się do Infinite Campus jako administrator zabezpieczeń.

2. Po lewej stronie menu kliknij pozycję **Administracja systemem**.

    ![Administrator](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

3. Przejdź do konfiguracji**dostawcy usług SSO****zarządzania saml zarządzania** >  **użytkownikiem** > .

    ![Saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

4. Na stronie **Konfiguracja dostawcy usług SSO** wykonaj następujące czynności:

    ![Sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Wybierz **włącz logowanie jednokrotne SAML**.

    b. Edytowanie **nazwy atrybutu opcjonalnego** **w** celu

    d. W sekcji **Wybierz opcję pobierania danych serwera dostawcy tożsamości (IDP)** wybierz pozycję Adres URL **metadanych**, wklej wartość **adresu URL metadanych aplikacji,** która została skopiowana z portalu Azure w polu, a następnie kliknij przycisk **Synchronizuj**.

    d. Po kliknięciu **przycisku Synchronizuj** wartości są automatycznie wypełniane na stronie **Konfiguracja dostawcy usług jednokrotnego.** Wartości te można zweryfikować, aby dopasować wartości widoczne w kroku 4 powyżej.

    e. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** `brittasimon@yourcompanydomain.extension`użytkownika wpisz . Na przykład BrittaSimon@contoso.com.

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

> [!NOTE]
> Jeśli chcesz, aby wszyscy użytkownicy platformy Azure mieli dostęp do logowania jednokrotnego do Infinite Campus i polegali na systemie uprawnień wewnętrznych Infinite Campus, aby kontrolować dostęp, możesz ustawić właściwość wymaganą do **przypisania użytkownika** aplikacji na Nie i pominąć następujące kroki.

W tej sekcji można włączyć Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Infinite Campus.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz **nieskończony kampus**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **nieskończony kampus**.

    ![Łącze Nieskończony kampus na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-infinite-campus-test-user"></a>Utwórz użytkownika testowego Infinite Campus

Infinite Campus ma architekturę skoncentrowaną na demografii. Skontaktuj się z [zespołem pomocy technicznej Infinite Campus,](mailto:sales@infinitecampus.com) aby dodać użytkowników na platformie Infinite Campus.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Infinite Campus w Panelu dostępu należy automatycznie zalogować się do nieskończonego kampusu, dla którego skonfigurowano logującą się logującą logującą log. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
