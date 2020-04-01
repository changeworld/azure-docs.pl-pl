---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z kartą Kanbanize | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a kartą Kanbanize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c5a3a096c5a44f681d23587837ae31fd1af33b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373225"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z kartą Kanbanize

W tym samouczku dowiesz się, jak zintegrować kanbanize z usługą Azure Active Directory (Azure AD). Po zintegrowaniu karty Kanbanize z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do kanbanize.
* Włącz użytkownikom automatyczne logowanie do kanbanize za pomocą swoich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) firmy Kanbanize.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Kanbanize obsługuje jednostki SSO inicjowane przez **sp i protokół IDP**
* Kanbanize obsługuje **inicjowanie** obsługi administracyjnej użytkowników just in time

## <a name="adding-kanbanize-from-the-gallery"></a>Dodawanie karty Kanbanize z galerii

Aby skonfigurować integrację kart Kanbanize z usługą Azure AD, należy dodać kanbanize z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Kanbanize** w polu wyszukiwania.
1. Wybierz **pozycję Kanbanize** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla karty Kanbanize

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą karty Kanbanize przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w kanbanize.

Aby skonfigurować i przetestować sytua usługi Azure AD z kartą Kanbanize, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne kanbanize](#configure-kanbanize-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Kanbanize](#create-kanbanize-test-user)** — aby mieć odpowiednik B.Simon w Kanbanize, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Kanbanize** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

     a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<subdomain>.kanbanize.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.kanbanize.com/saml/acs`

    d. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    d. W polach tekstowych **Stan przekazywania** wpisz adres URL:`/ctrl_login/saml_login`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Kanbanize,](mailto:support@ms.kanbanize.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Kanbanize oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut nameidentifier jest mapowany na atrybut **user.userprincipalname**. Aplikacja Kanbanize oczekuje, że identyfikator nazw zostanie zamapowany na **user.mail,** więc musisz edytować mapowanie atrybutów, klikając ikonę Edytuj i zmienić mapowanie atrybutów.

    ![image](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie kanbanize** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Kanbanize.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Kanbanize**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-kanbanize-sso"></a>Konfigurowanie numeru SYK kanbanize

1. Aby zautomatyzować konfigurację w ramach karty Kanbanize, należy zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **Przycisk Konfigurowanie Kanbanize** spowoduje skierowanie cię do aplikacji Kanbanize. W tym miejscu podaj poświadczenia administratora, aby zalogować się do kanbanize. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować kanbanize, otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmy Kanbanize jako administrator i wykonaj następujące czynności:

4. Przejdź w prawym górnym rogu strony, kliknij logo **Ustawienia.**

    ![Ustawienia kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. Na stronie panelu Administracja po lewej stronie menu kliknij pozycję **Integracje,** a następnie włącz **opcję Logowanie jednokrotne**.

    ![Integracje kart Kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. W sekcji Integracje kliknij przycisk **KONFIGURUJ,** aby otworzyć stronę **integracji logowania jednokrotnego.**

    ![Konfiga Kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. Na stronie **Integracja logowania jednokrotnego w** obszarze **Konfiguracje**wykonaj następujące czynności:

    ![Integracje kart Kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. W polu tekstowym **Identyfikator jednostki Idp** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal.

    b. W polu **tekstowym Idp Login Endpoint** wklej wartość **adresu URL logowania,** który został skopiowany z witryny Azure portal.

    d. W polu tekstowym **Idp Logout Endpoint** wklej wartość **adresu URL wylogowania,** który został skopiowany z witryny Azure portal.

    d. W **polu Nazwa atrybutu pola tekstowego wiadomości e-mail** wprowadź tę wartość`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. W **polu Nazwa atrybutu dla** pola tekstowego Imię wprowadź tę wartość`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. W **polu Tekstowym Nazwa atrybutu** wprowadź tę wartość`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > Te wartości można uzyskać, łącząc wartości obszaru nazw i nazw odpowiedniego atrybutu z sekcji Atrybuty użytkownika w witrynie Azure portal.

    g. W Notatniku otwórz certyfikat zakodowany base-64 pobrany z witryny Azure Portal, skopiuj jego zawartość (bez znaczników początkowych i końcowych), a następnie wklej go do pola **CertyfikatU Idp X.509.**

    h. Sprawdź **Włącz logowanie za pomocą logowania jedno i drugie i Kanbanize**.

    i. Kliknij **pozycję Zapisz ustawienia**.

### <a name="create-kanbanize-test-user"></a>Tworzenie użytkownika testowego Kanbanize

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w Kanbanize. Kanbanize obsługuje just-in-time inicjowania obsługi administracyjnej użytkowników, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w kanbanize, nowy jest tworzony po uwierzytelnieniu. Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta Kanbanize](mailto:support@ms.kanbanize.com).

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Kanbanize w Panelu dostępu należy automatycznie zalogować się do karty Kanbanize, dla której skonfigurowano logowanie jednośmiębowe. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj kanbanize z usługą Azure AD](https://aad.portal.azure.com/)

