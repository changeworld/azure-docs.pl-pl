---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z konsolą administratora netskope | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a konsolą administratora sieci Netskope.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e286adec-8d19-4d41-9afa-a2e39d7a5983
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c38900d4ded3d2ee08245674bda90d96226c1eb
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396572"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z konsolą administratora netskope

W tym samouczku dowiesz się, jak zintegrować konsolę administratora netskope z usługą Azure Active Directory (Azure AD). Po zintegrowaniu konsoli administratora netskope z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do konsoli administratora netskope.
* Włącz automatyczne logowanie użytkowników do konsoli administratora netskope za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną obsługą logowania jednokrotnego konsoli usługi Netskope.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Netskope Administrator Console obsługuje **sp i idp** zainicjowane SSO

## <a name="adding-netskope-administrator-console-from-the-gallery"></a>Dodawanie konsoli administratora netskope z galerii

Aby skonfigurować integrację konsoli administratora netskope z usługą Azure AD, należy dodać konsolę administratora netskope z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Netskope Administrator Console** w polu wyszukiwania.
1. Wybierz **pozycję Netskope Administrator Console** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-administrator-console"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla konsoli administratora usługi Netskope

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą konsoli administratora netskope przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w konsoli administratora usługi Netskope.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą konsoli administratora usługi Netskope, wykonaj następujące elementy konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne konsoli administratora netskope](#configure-netskope-administrator-console-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego konsoli administratora netskope](#create-netskope-administrator-console-test-user)** — aby mieć odpowiednik B.Simon w konsoli administratora Netskope, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Konsoli administratora netskope** znajdź sekcję **Zarządzaj** i wybierz **opcję Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`<OrgKey>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Otrzymasz te wartości wyjaśnione w dalszej części samouczka.

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Wartości adresu URL logowania nie są prawdziwe. Zaktualizuj wartość adresu URL logowania za pomocą rzeczywistego adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta konsoli administratora netskope,](mailto:support@netskope.com) aby uzyskać wartość adresu URL logowania. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Netskope Administrator Console oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/default-attributes.png)

1. Oprócz powyższej, Aplikacja Netskope Administrator Console oczekuje kilku więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa |  Atrybut źródłowy|
    | ---------| --------- |
    | rola administratora | user.assignedroles |

    > [!NOTE]
    > Kliknij [tutaj,](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) aby dowiedzieć się, jak utworzyć role w usłudze Azure AD.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie konsoli administratora sieci Netskope** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do konsoli administratora Netskope.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Netskope Administrator Console**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-netskope-administrator-console-sso"></a>Konfigurowanie rejestracji rejestracji sytej konsoli administratora w sieci Netskope

1. Otwórz nową kartę w przeglądarce i zaloguj się do witryny firmy Netskope Administrator Console jako administrator.

1. Kliknij kartę **Ustawienia** z lewego okienka nawigacji.

    ![Konfiguracja konsoli administratora netskope](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Kliknij kartę **Administracja.**

    ![Konfiguracja konsoli administratora netskope](./media/netskope-cloud-security-tutorial/config-administration.png)

1. Kliknij kartę **Jedno przysłowiowe.**

    ![Konfiguracja konsoli administratora netskope](./media/netskope-cloud-security-tutorial/config-sso.png)

1. W sekcji **Ustawienia sieciowe** wykonaj następujące czynności:
    
    ![Konfiguracja konsoli administratora netskope](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. Skopiuj wartość **adresu URL usługi konsumenta oświadczeń** i wklej ją do pola tekstowego Adres URL **odpowiedzi** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    b. Kopiuj wartość **identyfikatora jednostki dostawcy usług** i wklej ją do pola tekstowego **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w portalu Azure.

1. Kliknij sekcję **EDYTUJ USTAWIENIA** w sekcji **Ustawienia logowania/SLO.**

    ![Konfiguracja konsoli administratora netskope](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. W oknie podręcznym **Ustawienia** wykonaj następujące czynności;

    ![Konfiguracja konsoli administratora netskope](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Wybierz **włącz opcję SSO**.

    b. W polu tekstowym **adresu URL usługi IDP** wklej wartość **adresu URL logowania,** która została skopiowana z witryny Azure portal.

    d. W polu tekstowym **identyfikatora jednostki IDP** wklej wartość **identyfikatora usługi Azure AD,** która została skopiowana z witryny Azure portal.

    d. Otwórz pobrany certyfikat zakodowany base64 w notatniku, skopiuj jego zawartość do schowka, a następnie wklej go do pola tekstowego **CERTYFIKAT IDP.**

    e. Wybierz **włącz opcję SSO**.

    f. W polu **tekstowym adresu URL usługi IDP SLO** wklej wartość **adresu URL wylogowania,** która została skopiowana z witryny Azure portal.

    g. Kliknij przycisk **PRZEŚLIJ**.

### <a name="create-netskope-administrator-console-test-user"></a>Utwórz użytkownika testowego konsoli administratora netskope

1. Otwórz nową kartę w przeglądarce i zaloguj się do witryny firmy Netskope Administrator Console jako administrator.

1. Kliknij kartę **Ustawienia** z lewego okienka nawigacji.

    ![Tworzenie użytkownika konsoli administratora netskope](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Kliknij kartę **Aktywna platforma.**

    ![Tworzenie użytkownika konsoli administratora netskope](./media/netskope-cloud-security-tutorial/user1.png)

1. Kliknij kartę **Użytkownicy.**

    ![Tworzenie użytkownika konsoli administratora netskope](./media/netskope-cloud-security-tutorial/add-user.png)

1. Kliknij **pozycję DODAJ UŻYTKOWNIKÓW**.

    ![Tworzenie użytkownika konsoli administratora netskope](./media/netskope-cloud-security-tutorial/user-add.png)

1. Wprowadź adres e-mail użytkownika, którego chcesz dodać, a następnie kliknij przycisk **ADD**.

    ![Tworzenie użytkownika konsoli administratora netskope](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Konsoli administratora netskope w Panelu dostępu należy automatycznie zalogować się do konsoli administratora netskope, dla której skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj konsolę administratora netskope z usługą Azure AD](https://aad.portal.azure.com/)
