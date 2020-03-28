---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z serwerem Tableau | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a serwerem Tableau.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d19a7cc8d81f9e6e913f147b24c5cce03ff82027
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76986737"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z serwerem Tableau

W tym samouczku dowiesz się, jak zintegrować tableau server z usługą Azure Active Directory (Azure AD). Po zintegrowaniu tableau server z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do tableau server.
* Włącz użytkownikom automatyczne logowanie do tableau server za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego tableau server.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Serwer Tableau obsługuje sygosk SSO inicjowane przez usługę **SP**
* Po skonfigurowaniu tableau server można wymusić kontrolę sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-server-from-the-gallery"></a>Dodawanie serwera Tableau z galerii

Aby skonfigurować integrację tableau server z usługą Azure AD, należy dodać serwer Tableau z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **tableau server** w polu wyszukiwania.
1. Wybierz **pozycję Tableau Server** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla serwera Tableau

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą tableau server przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w tableau server.

Aby skonfigurować i przetestować sytua usługi Azure AD z pomocą tableau server, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne serwera Tableau](#configure-tableau-server-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego serwera Tableau](#create-tableau-server-test-user)** — aby mieć odpowiednik B.Simon w Tableau Server, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **tableau server** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://azure.<domain name>.link`

    b. W polu **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://azure.<domain name>.link`

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Powyższe wartości nie są wartościami rzeczywistymi. Zaktualizuj wartości za pomocą rzeczywistego adresu URL i identyfikatora ze strony konfiguracji serwera Tableau, która została wyjaśniona w dalszej części samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie serwera Tableau** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Tableau Server.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Tableau Server**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-tableau-server-sso"></a>Konfigurowanie sytuacy serwera tableau

1. Aby skonfigurować logowanie przy logowaniu sytuacyjne dla aplikacji, należy zalogować się do dzierżawy serwera Tableau jako administrator.

2. Na karcie **KONFIGURACJA** wybierz pozycję **Tożsamość użytkownika & access**, a następnie wybierz kartę Metoda **uwierzytelniania.**

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na stronie **KONFIGURACJA** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. W przypadku **metody uwierzytelniania**wybierz saml.

    b. Zaznacz pole wyboru **Włącz uwierzytelnianie SAML dla serwera**.

    d. Adres URL zwracany serwera Tableau — adres URL, do <http://tableau_server>który będą uzyskiwać dostęp użytkownicy serwera Tableau, na przykład . Użycie `http://localhost` nie jest zalecane. Użycie adresu URL z ukośnikiem `http://tableau_server/`(na przykład) nie jest obsługiwane. Kopiowanie **adresu URL zwrotnego serwera Tableau** i wklejanie go do skrzynki **tekstowej Logowania adresu URL** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal

    d. Identyfikator jednostki SAML — identyfikator jednostki jednoznacznie identyfikuje instalację serwera Tableau w IdP. Możesz ponownie wprowadzić adres URL serwera Tableau tutaj, jeśli chcesz, ale nie musi to być adres URL serwera Tableau. Kopiowanie **identyfikatora jednostki SAML** i wklejanie go do pola tekstowego **identyfikatora** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal

    e. Kliknij **pobierz plik metadanych XML** i otwórz go w aplikacji edytora tekstu. Zlokalizuj adres URL usługi konsumenta oświadczeń za pomocą wpisu http i indeksu 0 i skopiuj adres URL. Teraz wklej go do pola tekstowego **Odpowiedz adres URL** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal

    f. Znajdź plik metadanych federacji pobrany z witryny Azure Portal, a następnie przekaż go w **pliku metadanych SAML Idp**.

    g. Wprowadź nazwy atrybutów używanych przez IdP do przechowywania nazw użytkowników, nazw wyświetlanych i adresów e-mail.

    h. Kliknij **przycisk Zapisz**

    > [!NOTE]
    > Klient musi przekazać dowolny certyfikat w konfiguracji SSO SSO SAML serwera Tableau i zostanie on zignorowany w przepływie SSO. Jeśli potrzebujesz pomocy w konfigurowaniu SAML na tableau Server, zapoznaj się z tym artykułem [Konfigurowanie SAML](https://help.tableau.com/current/online/en-us/saml_config_azure_ad.htm).

### <a name="create-tableau-server-test-user"></a>Utwórz użytkownika testowego serwera Tableau

Celem tej sekcji jest utworzenie użytkownika o nazwie B.Simon w Tableau Server. Należy aprowizować wszystkich użytkowników na serwerze Tableau.

Ta nazwa użytkownika powinna być zgodna z wartością skonfigurowaną w niestandardowym atrybucie **nazwy użytkownika**usługi Azure AD. Przy prawidłowym mapowaniu integracja powinna działać Konfigurowanie logowania jednokrotnego usługi Azure AD.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, należy skontaktować się z administratorem serwera Tableau w organizacji.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Tableau Server w Panelu dostępu należy automatycznie zalogować się do serwera Tableau, dla którego skonfigurowano logującą się jednoślik. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj serwer tableau z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)