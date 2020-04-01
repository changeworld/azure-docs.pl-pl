---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z slackiem | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c80963976783321d05fc6f32bb24daed36fa105
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76985559"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z slackiem

W tym samouczku dowiesz się, jak zintegrować Slack z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Slack z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Slack.
* Włącz użytkownikom automatyczne logowanie do slacka za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Slack rejestracji jednokrotnej (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Usługa Slack obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**
* Usługa Slack obsługuje aprowizację użytkownika typu **just in time**
* Usługa Slack obsługuje [**zautomatyzowaną** aprowizację użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-provisioning-tutorial)
* Po skonfigurowaniu slacka można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-slack-from-the-gallery"></a>Dodawanie usługi Slack z galerii

Aby skonfigurować integrację usługi Slack z usługą Azure AD, należy dodać usługę Slack z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Slack** w polu wyszukiwania.
1. Wybierz **pozycję Zapas z** panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla slacka

Konfigurowanie i testowanie usługi Azure AD SSO z Slack przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w slacku.

Aby skonfigurować i przetestować sytuasz usługi Azure AD z pomocą slacka, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego slack](#configure-slack-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz użytkownika testowego Slack](#create-slack-test-user)** — aby mieć odpowiednik B.Simon w slack, który jest połączony z reprezentacji usługi Azure AD użytkownika.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Slack** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.slack.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://slack.com`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zaktualizuj wartość za pomocą rzeczywistego adresu URL logowania. W celu uzyskania tej wartości skontaktuj się z [zespołem pomocy technicznej klienta usługi Slack](https://slack.com/help/contact). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Slack aplikacja oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodawania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image](common/edit-attribute.png)

1. Oprócz powyższej aplikacji Slack oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML, które są pokazane poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami. Jeśli użytkownicy nie mają adresu e-mail, a następnie **mapuj adres e-mail** do **user.userprincipalname**.

    | Nazwa | Atrybut źródłowy |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | | |

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie zapasów zapasów** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Slack.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Slack**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-slack-sso"></a>Konfigurowanie aplikacji sytłej slack

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Slack jako administrator.

2. Przejdź do sekcji **Microsoft Azure AD**, a następnie przejdź do sekcji **Team Settings** (Ustawienia zespołu).

     ![Konfigurowanie strony aplikacji logowania jednokrotnego](./media/slack-tutorial/tutorial_slack_001.png)

3. W sekcji **Team Settings** (Ustawienia zespołu) kliknij kartę **Authentication** (Uwierzytelnianie), a następnie kliknij przycisk **Change Settings** (Zmień ustawienia).

    ![Konfigurowanie strony aplikacji logowania jednokrotnego](./media/slack-tutorial/tutorial_slack_002.png)

4. W oknie dialogowym **SAML Authentication Settings** (Ustawienia uwierzytelniania SAML) wykonaj następujące czynności:

    ![Konfigurowanie strony aplikacji logowania jednokrotnego](./media/slack-tutorial/tutorial_slack_003.png)

    a.  W polu tekstowym **SAML 2.0 Endpoint (HTTP)** (Punkt końcowy SAML 2.0 — HTTP) wklej wartość pola **Adres URL logowania** skopiowaną z witryny Azure Portal.

    b.  W polu tekstowym **Identity Provider Issuer** (Wystawca dostawcy tożsamości) wklej wartość pola **Identyfikator usługi Azure AD** skopiowaną z witryny Azure Portal.

    d.  Otwórz pobrany plik certyfikatu w Notatniku, skopiuj zawartość do schowka, a następnie wklej ją w polu tekstowym **Public Certificate** (Certyfikat publiczny).

    d. Skonfiguruj trzy powyższe ustawienia odpowiednio dla Twojego zespołu usługi Slack. Więcej informacji na temat ustawień możesz znaleźć w **przewodniku po konfiguracji logowania jednokrotnego w usłudze Slack** znajdującym się pod następującym adresem. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Kliknij pozycję **Save Configuration** (Zapisz konfigurację).

### <a name="create-slack-test-user"></a>Tworzenie użytkownika testowego usługi Slack

Celem tej sekcji jest utworzenie użytkownika o nazwie B.Simon w Slack. Usługa Slack obsługuje aprowizację typu just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby uzyskania dostępu do usługi Slack, jeśli jeszcze nie istnieje. Usługa Slack obsługuje także automatyczną aprowizację użytkowników. Więcej informacji na temat konfigurowania automatycznej aprowizacji użytkowników możesz znaleźć [tutaj](slack-provisioning-tutorial.md).

> [!NOTE]
> Jeśli konieczne jest ręczne utworzenie użytkownika, skontaktuj się z [zespołem pomocy technicznej usługi Slack](https://slack.com/help/contact).

> [!NOTE]
> Usługa Azure AD Connect to narzędzie do synchronizacji, które można synchronizować na lokalnych tożsamościach usługi Active Directory z usługą Azure AD, a następnie ci zsynchronizowani użytkownicy mogą również używać aplikacji, tak jak inni użytkownicy chmury.

## <a name="test-sso"></a>Test SSO

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Slack na panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi Slack, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj slack z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)