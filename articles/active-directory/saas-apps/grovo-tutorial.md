---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z grovo | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a grovo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 399cecc3-aa62-4914-8b6c-5a35289820c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fb462905d1f8700214a0c4fc0c2d0bcb3e70a0f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "70163509"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-grovo"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z grovo

W tym samouczku dowiesz się, jak zintegrować Grovo z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Grovo z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Grovo.
* Włącz użytkownikom automatyczne logowanie się do Grovo za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Grovo jednokrotnego logowania (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Grovo obsługuje **sp i IDP** zainicjowane SSO
* Grovo obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time

## <a name="adding-grovo-from-the-gallery"></a>Dodawanie Grovo z galerii

Aby skonfigurować integrację Grovo z usługą Azure AD, należy dodać Grovo z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Grovo** w polu wyszukiwania.
1. Wybierz **Grovo** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-grovo"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla grovo

Konfigurowanie i testowanie usługi Azure AD SSO z Grovo przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Grovo.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą usługi Grovo, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne Grovo](#configure-grovo-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Grovo](#create-grovo-test-user)** — aby mieć odpowiednik B.Simon w Grovo, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/), na stronie integracji aplikacji **Grovo,** znajdź sekcję **Zarządzaj** i wybierz **logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    d. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polu tekstowym **Stan przekaźnika** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.grovo.com`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości za pomocą rzeczywistego identyfikatora, adresu URL odpowiedzi, adresu URL logowania i stanu przekazywania. Skontaktuj się z [zespołem pomocy technicznej Klienta Grovo,](https://www.grovo.com/contact-us) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Grovo** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Grovo.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Grovo**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-grovo-sso"></a>Konfigurowanie sytego syda w Grovo

1. W innym oknie przeglądarki internetowej zaloguj się do Grovo jako administrator.

2. Przejdź do **programu Integracje administratorów** > **Integrations**.
 
    ![Konfiguracja Grovo](./media/grovo-tutorial/tutorial_grovo_admin.png) 

3. Kliknij **pozycję USTAW w** sekcji SP **Initiated SAML 2.0.**

    ![Konfiguracja Grovo](./media/grovo-tutorial/tutorial_grovo_setup.png)

4. W oknie **podręcznym SP Initiated SAML 2.0** wykonaj następujące czynności:

    ![Konfiguracja Grovo](./media/grovo-tutorial/tutorial_grovo_saml.png)

    a. W polu tekstowym **identyfikator jednostki** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal.

    b. W polu **tekstowym usługi logowania jednokrotnego** wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    d. Wybierz **opcję Powiązanie punktu końcowego usługi logowania jednokrotnego** jako `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect`.
    
    d. Otwórz pobrany **certyfikat zakodowany w witrynie** Azure portal z witryny Azure w notatniku, wklej go do pola tekstowego **Klucz publiczny.**

    e. Kliknij przycisk **alej**.

### <a name="create-grovo-test-user"></a>Utwórz użytkownika testowego Grovo

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w Grovo. Grovo obsługuje just-in-time inicjowania obsługi administracyjnej użytkowników, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w Grovo, nowy jest tworzony po uwierzytelnieniu.

> [!Note]
> Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Grovo](https://www.grovo.com/contact-us).

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Grovo w panelu dostępu należy automatycznie zalogować się do Grovo, dla którego skonfigurowano logowanie jednośmiętne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Grovo z usługą Azure AD](https://aad.portal.azure.com/)