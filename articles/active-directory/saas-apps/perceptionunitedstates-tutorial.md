---
title: 'Samouczek: Integracja usługi Azure Active Directory z Perception United States (Non-UltiPro) | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i Perception United States (Non-UltiPro).
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
ms.openlocfilehash: e9ba42f780c93486409077383750d0635637e99b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094840"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Samouczek: Integracja usługi Azure Active Directory z Perception United States (Non-UltiPro)

W tym samouczku dowiesz się, jak zintegrować perception Stany Zjednoczone (non-UltiPro) z usługą Azure Active Directory (Azure AD).
Integracja percepcji w Stanach Zjednoczonych (non-UltiPro) z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do percepcji Stany Zjednoczone (Non-UltiPro).
* Można włączyć użytkowników do automatycznego logowania się do Percepcji Stany Zjednoczone (Non-UltiPro) (Logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z perception united states (non-UltiPro), potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego Perception United States (Non-UltiPro)

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Perception United States (Non-UltiPro) obsługuje zainicjowane **przez IDP** SSO

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Dodawanie Percepcji Stany Zjednoczone (Non-UltiPro) z galerii

Aby skonfigurować integrację percepcji Stany Zjednoczone (Non-UltiPro) do usługi Azure AD, należy dodać Percepcji Stany Zjednoczone (Non-UltiPro) z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Perception United States (Non-UltiPro) z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Perception United States (Non-UltiPro)**, wybierz **opcję Postrzeganie Stanów Zjednoczonych (Non-UltiPro)** w panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![Perception United States (Non-UltiPro) na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Percepcja Stany Zjednoczone (Non-UltiPro) na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w perception united states (non-UltiPro).

Aby skonfigurować i przetestować usługę Azure AD jednokrotnego logowania z Perception United States (Non-UltiPro), należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj samopodstawowe logowanie Perception United States (Non-UltiPro)](#configure-perception-united-states-non-ultipro-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz percepcję Stany Zjednoczone (Non-UltiPro) użytkownika testowego](#create-perception-united-states-non-ultipro-test-user)** — mieć odpowiednik Britta Simon w percepcji Stany Zjednoczone (Non-UltiPro), który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą perception united states (non-UltiPro), wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Perception United States (Non-UltiPro)** wybierz opcję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    ![Perception United States (Non-UltiPro) Informacje o domenie i adresach URL logowania jednokrotnego](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL:`https://perception.kanjoya.com/sp`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    d. Aplikacja **Perception United States (Non-UltiPro)** wymaga, aby wartość **identyfikatora usługi Azure AD** jako <entity_id>, którą można uzyskać z sekcji **Konfigurowanie postrzegania Stanów Zjednoczonych (Non-UltiPro),** która ma być zakodowana jako uri. Aby uzyskać wartość zakodowaną w identyfikatorze **http://www.url-encode-decode.com/** uri, użyj następującego łącza: .

    d. Po uzyskaniu wartości kodowanej uri połącz ją z **adresem URL odpowiedzi,** o którym mowa poniżej-

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Wklej powyższą wartość w polu tekstowym **Odpowiedz adres URL.**

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie percepcji w Stanach Zjednoczonych (non-UltiPro)** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Konfigurowanie percepcji Stany Zjednoczone (Non-UltiPro) Logowanie jednokrotne

1. W innym oknie przeglądarki zaloguj się do witryny firmy Perception United States (Non-UltiPro) jako administrator.

2. Na głównym pasku narzędzi kliknij pozycję **Ustawienia konta**.

    ![Perception Stany Zjednoczone (Non-UltiPro) użytkownik](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Na stronie **Ustawienia konta** wykonaj następujące czynności:

    ![Perception Stany Zjednoczone (Non-UltiPro) użytkownik](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. W polach tekstowych **Nazwa firmy** wpisz nazwę **Firmy**.
    
    b. W polach **tekstowych Nazwa konta** wpisz nazwę **konta**.

    d. W polu **tekstowym Domyślna wiadomość e-mail** wpisz prawidłowy **adres e-mail**.

    d. Wybierz **dostawcę tożsamości jako SAML** **2.0**.

4. Na stronie **Konfiguracja samego systemu SYC** wykonaj następujące czynności:

    ![Perception Stany Zjednoczone (Non-UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Wybierz **opcję SAML NameID Type** as **EMAIL**.

    b. W pole **tekstowym Nazwa konfiguracji wpisać** nazwę **konfiguracji**.
    
    d. W polu tekstowym **Nazwa dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal. 

    d. W **polu tekstowym Domena SAML**wprowadź domenę w stylu @contoso.com.

    e. Kliknij **przycisk Przekaż ponownie,** aby przekazać plik **XML metadanych.**

    f. Kliknij przycisk **Update** (Aktualizuj).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** brittasimon@yourcompanydomain.extensionużytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z azure logowania jednokrotnego, udzielając dostępu do percepcji Stany Zjednoczone (Non-UltiPro).

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz **opcję Postrzeganie Stanów Zjednoczonych (Non-UltiPro).**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **opcję Postrzeganie Stanów Zjednoczonych (Non-UltiPro).**

    ![Łącze Perception United States (Non-UltiPro) na liście Aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Tworzenie percepcji Stany Zjednoczone (Non-UltiPro) użytkownik testowy

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Percepcja Stany Zjednoczone (Non-UltiPro). Współpracuj z [zespołem wsparcia Perception United States (Non-UltiPro),](https://www.ultimatesoftware.com/Contact/ContactUs) aby dodać użytkowników na platformie Perception United States (Non-UltiPro).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Perception United States (Non-UltiPro) w Panelu dostępu należy automatycznie zalogować się do witryny Perception United States (Non-UltiPro), dla której skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

