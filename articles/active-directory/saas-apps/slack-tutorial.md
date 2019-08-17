---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z zapasem czasu | Microsoft Docs'
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
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26e15f704fc9604bd18a1f4848e84065fc507314
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563121"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z zapasem czasu

W tym samouczku dowiesz się, jak zintegrować zapasy czasu z Azure Active Directory (Azure AD). Podczas integrowania zapasu czasu z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do zapasu czasu.
* Zezwól użytkownikom na automatyczne logowanie do zapasu czasu przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Slack obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**
* Usługa Slack obsługuje aprowizację użytkownika typu **just in time**
* Usługa Slack obsługuje [**zautomatyzowaną** aprowizację użytkowników](https://docs.microsoft.com/en-gb/azure/active-directory/saas-apps/slack-provisioning-tutorial)

## <a name="adding-slack-from-the-gallery"></a>Dodawanie usługi Slack z galerii

Aby skonfigurować integrację usługi Slack z usługą Azure AD, należy dodać usługę Slack z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Zapasy czasu** w polu wyszukiwania.
1. Wybierz pozycję zapasowy z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla zapasu czasu

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą zapasowego użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach zapasu czasu.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą zapasu czasu, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj zapasowe logowanie](#configure-slack-sso)** jednokrotne — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego zapasowego](#create-slack-test-user)** — Aby uzyskać odpowiednik elementu B. Simon w zapasach czasu, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj logowanie](#test-sso)** jednokrotne — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją zapasową, Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie**jednokrotne.
1. Na stronie **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.slack.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://slack.com`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zaktualizuj wartość za pomocą rzeczywistego adresu URL logowania. W celu uzyskania tej wartości skontaktuj się z [zespołem pomocy technicznej klienta usługi Slack](https://slack.com/help/contact). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Usługa Slack oczekuje asercji SAML w konkretnym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** kliknij przycisk **Edytuj** , aby otworzyć okno dialogowe **atrybuty użytkownika** .

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Jeśli masz użytkowników, którym przypisano **adres e-mail** nie ma w licencji usługi Office 365 , nie będzie on wyświetlany w tokenie SAML. W takich przypadkach zalecane jest używanie ciągu **user.userprincipalname** jako wartości atrybutu **User.Email** w celu zamapowania jako atrybut **Unikatowy identyfikator**.

1. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    | Nazwa | Atrybut źródłowy |
    | --- | --- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Email | user.mail |
    | User.Username | user.userprincipalname |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij polecenie **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie zapasu czasu** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do zapasu czasu.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Slack**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-slack-sso"></a>Skonfiguruj zapasowe Logowanie jednokrotne

1. W innym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny zapasowej jako administrator.

2. Przejdź do sekcji **Microsoft Azure AD**, a następnie przejdź do sekcji **Team Settings** (Ustawienia zespołu).

     ![Skonfiguruj Logowanie jednokrotne na stronie aplikacji](./media/slack-tutorial/tutorial_slack_001.png)

3. W sekcji **Team Settings** (Ustawienia zespołu) kliknij kartę **Authentication** (Uwierzytelnianie), a następnie kliknij przycisk **Change Settings** (Zmień ustawienia).

    ![Skonfiguruj Logowanie jednokrotne na stronie aplikacji](./media/slack-tutorial/tutorial_slack_002.png)

4. W oknie dialogowym **SAML Authentication Settings** (Ustawienia uwierzytelniania SAML) wykonaj następujące czynności:

    ![Skonfiguruj Logowanie jednokrotne na stronie aplikacji](./media/slack-tutorial/tutorial_slack_003.png)

    a.  W polu tekstowym **SAML 2.0 Endpoint (HTTP)** (Punkt końcowy SAML 2.0 — HTTP) wklej wartość pola **Adres URL logowania** skopiowaną z witryny Azure Portal.

    b.  W polu tekstowym **Identity Provider Issuer** (Wystawca dostawcy tożsamości) wklej wartość pola **Identyfikator usługi Azure AD** skopiowaną z witryny Azure Portal.

    c.  Otwórz pobrany plik certyfikatu w Notatniku, skopiuj zawartość do schowka, a następnie wklej ją w polu tekstowym **Public Certificate** (Certyfikat publiczny).

    d. Skonfiguruj trzy powyższe ustawienia odpowiednio dla Twojego zespołu usługi Slack. Więcej informacji na temat ustawień możesz znaleźć w **przewodniku po konfiguracji logowania jednokrotnego w usłudze Slack** znajdującym się pod następującym adresem. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Kliknij pozycję **Save Configuration** (Zapisz konfigurację).

### <a name="create-slack-test-user"></a>Tworzenie użytkownika testowego usługi Slack

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w usłudze Slack. Usługa Slack obsługuje aprowizację typu just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby uzyskania dostępu do usługi Slack, jeśli jeszcze nie istnieje. Usługa Slack obsługuje także automatyczną aprowizację użytkowników. Więcej informacji na temat konfigurowania automatycznej aprowizacji użytkowników możesz znaleźć [tutaj](slack-provisioning-tutorial.md).

> [!NOTE]
> Jeśli konieczne jest ręczne utworzenie użytkownika, skontaktuj się z [zespołem pomocy technicznej usługi Slack](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect to narzędzie do synchronizacji, które może synchronizować lokalne tożsamości Active Directory z usługą Azure AD, a następnie te synchronizowane użytkownicy mogą również używać aplikacji jako innych użytkowników w chmurze.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Slack na panelu dostępu powinno nastąpić automatyczne zalogowanie do usługi Slack, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Azure AD](https://aad.portal.azure.com/)