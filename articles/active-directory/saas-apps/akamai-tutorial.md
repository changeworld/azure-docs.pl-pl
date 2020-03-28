---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z akamai | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Akamai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042dd242285081001ca48c9f17e4d42c2294c0ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74979595"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z usługą Akamai

W tym samouczku dowiesz się, jak zintegrować program Akamai z usługą Azure Active Directory (Azure AD). Po zintegrowaniu akamai z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Akamai.
* Włącz użytkownikom automatyczne logowanie do akamai za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego Akamai.Akamai single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

- Slack obsługuje zainicjowane przez protokół SSO inicjowane przez protokół IDP

## <a name="adding-akamai-from-the-gallery"></a>Dodawanie Akamai z galerii

Aby skonfigurować integrację programu Akamai z usługą Azure AD, należy dodać akamai z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **polecenie Akamai** w polu wyszukiwania.
1. Wybierz **pozycję Akamai** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi Akamai

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą akamai przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji Akamai.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą usługi Akamai, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj Akamai SSO](#configure-akamai-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Akamai](#create-akamai-test-user)** — aby mieć odpowiednik B.Simon w Akamai, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Akamai** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<Yourapp>.login.go.akamai-access.com/sp/response`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https:// <Yourapp>.login.go.akamai-access.com/sp/response`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta Akamai,](https://www.akamai.com/us/en/contact-us/) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie Akamai** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Akamai.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Akamai**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-akamai-sso"></a>Konfigurowanie SSO Akamai

### <a name="setting-up-idp"></a>Konfigurowanie protokołu IDP

1. Signin do **konsoli Akamai Enterprise Application Access.**
1. Na **konsoli Akamai EAA** **wybierz** > **dostawców tożsamości**tożsamości .

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure01.png)

1. Kliknij **pozycję Dodaj dostawcę tożsamości**.

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure02.png)

    a. Określ **unikatową nazwę**.
    
    b. Wybierz **pozycję SAML innej firmy** i kliknij pozycję **Utwórz dostawcę tożsamości i skonfiguruj**.

### <a name="general-settings"></a>Ustawienia ogólne

1. **Przechwytywanie tożsamości** — określ nazwę (podstawowy adres URL SP — będzie używany dla konfiguracji usługi Azure AD)

    > [!NOTE]
    > Możesz wybrać własną domenę niestandardową (będzie wymagać wpisu DNS i certyfikatu). W tym przykładzie będziemy używać domeny Akamai.

1. **Akamai Cloud Zone** - Wybierz odpowiednią strefę chmury.
1. **Sprawdzanie poprawności certyfikatu** — sprawdź dokumentację Akamai (opcjonalnie)

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Konfiguracja uwierzytelniania

1. ADRES URL — określ adres URL taki sam jak przechwytywanie tożsamości (w tym miejscu użytkownicy są przekierowywani po uwierzytelnieniu).
2. Adres URL wylogowania: zaktualizuj adres URL wylogowania.
3. Podpisz żądanie SAML: domyślnie niezaznaczone.
4. W przypadku pliku metadanych IDP dodaj aplikację w konsoli usługi Azure AD.

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure04.png)

### <a name="header-based-authentication"></a>Uwierzytelnianie oparte na nagłówku

Uwierzytelnianie oparte na nagłówku Akamai

1. Wybierz **niestandardowy** formularz HTTP Kreatora dodawania aplikacji.

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure05.png)

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure06.png)

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure07.png)

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Uwierzytelnianie

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure09.png)

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Usługi

1. Kliknij pozycję Zapisz i przejdź do uwierzytelniania.

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Ustawienia zaawansowane

1. W obszarze **Nagłówki HTTP odbiorcy**określ **pozycję CustomerHeader** i **SAML Attribute**.

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure12.png)

1. Kliknij przycisk Zapisz i przejdź do przycisku **Wdrażanie.**

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>Wdrażanie aplikacji

1. Kliknij przycisk **Wdrażanie aplikacji.**

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure14.png)

1. Sprawdź, czy aplikacja została pomyślnie wdrożona.

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure15.png)

### <a name="kerberos-authentication"></a>Uwierzytelnianie Kerberos

#### <a name="remote-desktop"></a>Pulpit zdalny

1. Wybierz **pozycję RDP** z Kreatora dodawania aplikacji.

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure16.png)

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure17.png)

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure18.png)

1. Określ łącznik, który będzie obsługiwał tę obsługę.

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Uwierzytelnianie

Kliknij **pozycję Zapisz i przejdź do usługi**.

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Usługi

Kliknij **pozycję Zapisz i przejdź do pozycji Ustawienia zaawansowane**.

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Ustawienia zaawansowane

Kliknij **pozycję Zapisz i przejdź do opcji Wdrożenie**.

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure22.png)

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure23.png)

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure24.png)

### <a name="deployment"></a>wdrażania

#### <a name="ssh"></a>Protokół SSH

1. Przejdź do do dodaj aplikacje , Wybierz **SSH**.

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure25.png)

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure26.png)

1. Konfigurowanie tożsamości aplikacji.

    ![Konfigurowanie akamai](./media/header-akamai-tutorial/configure27.png)

    a. Określ nazwę / opis.

    b. Określ adres IP/FQDN serwera aplikacji i port dla protokołu SSH.

    d. Określ nazwę użytkownika / hasło SSH *Sprawdź Akamai EAA.

    d. Określ nazwę hosta zewnętrznego.

    e. Określ lokalizację łącznika i wybierz łącznik.

#### <a name="authentication"></a>Uwierzytelnianie

Kliknij **zapisz i przejdź do usługi**.

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Usługi

Kliknij **pozycję Zapisz i przejdź do pozycji Ustawienia zaawansowane**.

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Ustawienia zaawansowane

Kliknij przycisk Zapisz i przejdź do wdrożenia

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure30.png)

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>wdrażania

Kliknij pozycję **Wdrażanie aplikacji**.

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure32.png)

### <a name="kerberos-applications"></a>Aplikacje Kerberos

#### <a name="adding-directory"></a>Dodawanie katalogu

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure33.png)

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure34.png)

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure35.png)

![Konfigurowanie akamai](./media/header-akamai-tutorial/configure36.png)

### <a name="create-akamai-test-user"></a>Utwórz użytkownika testowego Akamai

W tej sekcji utworzysz użytkownika o nazwie B.Simon w Akamai. Współpracuj z [zespołem pomocy technicznej klienta Akamai,](https://www.akamai.com/us/en/contact-us/) aby dodać użytkowników na platformie Akamai. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Akamai w Panelu dostępu należy automatycznie zalogować się do Akamai, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj program Akamai z usługą Azure AD](https://aad.portal.azure.com/)
