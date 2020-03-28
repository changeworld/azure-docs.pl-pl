---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z elium | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0900f730c287586725722f0b8baaeb0c22f850c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72791222"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Samouczek: Integracja logowania jednokrotnego usługi Azure Active Directory z elium

W tym samouczku dowiesz się, jak zintegrować Elium z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Elium z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Elium.
* Włącz użytkownikom automatyczne logowanie się do elium za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego Elium.Elium single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Aplikacja Elium obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług oraz dostawcę tożsamości**
* Aplikacja Elium obsługuje aprowizowanie użytkowników typu **just in time**

## <a name="adding-elium-from-the-gallery"></a>Dodawanie aplikacji Elium z galerii

Aby skonfigurować integrację aplikacji Elium z usługą Azure AD, musisz dodać aplikację Elium z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Elium** w polu wyszukiwania.
1. Wybierz **pozycję Elium** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-elium"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla elium

Konfigurowanie i testowanie usługi Azure AD SSO z Elium przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Elium.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą elium, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj elium SSO](#configure-elium-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Elium](#create-elium-test-user)** — aby mieć odpowiednik B.Simon w Elium, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Elium** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<platform-domain>.elium.com/login/saml2/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Uzyskasz te wartości z **pliku metadanych dostawcy tożsamości**, dostępnego do pobrania na stronie `https://<platform-domain>.elium.com/login/saml2/metadata`, co objaśniono w dalszej części tego samouczka.

1. Aplikacja Elium oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej, Aplikacja Elium oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > To są oświadczenia domyślne. **Tylko oświadczenie e-mail jest wymagane**. W przypadku aprowizowania JIT również tylko oświadczenie e-mail jest obowiązkowe. Inne oświadczenia niestandardowe mogą się różnić w zależności od platformy klienta.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie Elium** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Elium.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Elium**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-elium-sso"></a>Konfigurowanie sytua dla elium

1. Aby zautomatyzować konfigurację w Elium, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij **Set up Elium** przekieruje Cię do aplikacji Elium. Stamtąd podaj poświadczenia administratora, aby zalogować się do Elium. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-6.

    ![Konfiguracja instalacji](common/setup-sso.png)

1. Jeśli chcesz skonfigurować Elium ręcznie, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Elium jako administrator i wykonaj następujące czynności:

1. Kliknij pozycję **User profile** (Profil użytkownika) w prawym górnym rogu, a następnie wybierz pozycję **Administration** (Administracja).

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user1.png)

1. Wybierz kartę **Security** (Zabezpieczenia).

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user2.png)

1. Przewiń w dół do sekcji **Single sign-on (SSO)** (Logowanie jednokrotne) i wykonaj następujące kroki:

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user3.png)

    a. Skopiuj wartość pola **Verify that SAML2 authentication works for your account** (Sprawdź, czy uwierzytelnianie SAML2 działa dla Twojego konta) i wklej ją w polu tekstowym **Adres URL logowania** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    > [!NOTE]
    > Po skonfigurowaniu logowania jednokrotnego zawsze możesz uzyskać dostęp do domyślnej strony logowania zdalnego pod następującym adresem URL: `https://<platform_domain>/login/regular/login` 

    b. Zaznacz pole wyboru **Enable SAML2 federation** (Włącz federację SAML2).

    d. Zaznacz pole wyboru **JIT Provisioning** (Aprowizowanie JIT).

    d. Otwórz **metadane dostawcy usług**, klikając przycisk **Download** (Pobierz).

    e. Wyszukaj element **entityID** w pliku **metadanych dostawcy usług**, skopiuj wartość elementu **entityID**, a następnie wklej ją w polu tekstowym **Identyfikator** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal. 

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user4.png)

    f. Wyszukaj element **AssertionConsumerService** w pliku **metadanych dostawcy usług**, skopiuj wartość elementu **Location**, a następnie wklej ją w polu tekstowym **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    ![Konfigurowanie logowania jednokrotnego](./media/elium-tutorial/user5.png)

    g. Otwórz w Notatniku plik metadanych pobrany z witryny Azure Portal, skopiuj jego zawartość i wklej ją w polu tekstowym **IdP Metadata** (Metadane dostawcy tożsamości).

    h. Kliknij przycisk **Zapisz**.

### <a name="create-elium-test-user"></a>Tworzenie użytkownika testowego aplikacji Elium

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w Elium. Aplikacja Elium obsługuje **aprowizowanie typu just-in-time**, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Elium, zostanie utworzony podczas próby uzyskania dostępu do aplikacji Elium.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej aplikacji Elium](mailto:support@elium.com).

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Elium w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Elium, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj Elium z usługą Azure AD](https://aad.portal.azure.com/)