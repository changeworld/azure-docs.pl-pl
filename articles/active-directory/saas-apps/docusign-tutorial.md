---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z docusign | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a docusign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01f969c3bc6f546025b3bbe5826181efdfa69be0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983661"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z docusign

W tym samouczku dowiesz się, jak zintegrować DocuSign z usługą Microsoft Azure Active Directory (Azure AD). Po zintegrowaniu DocuSign z usługą Azure AD można:

* Użyj usługi Azure AD, aby kontrolować, kto ma dostęp do DocuSign.
* Włącz automatyczne logowanie do docusign dla użytkowników za pośrednictwem ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: w witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji z usługą Azure AD z aplikacjami typu oprogramowanie jako usługa SaaS, zobacz [Logowanie jednokrotne w aplikacjach usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja DocuSign, która jest włączona logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz samouszeńcę usługi Azure AD w środowisku testowym, aby to sprawdzić:

* DocuSign obsługuje zainicjowane przez dostawcę usług (SP) samoso.

* DocuSign obsługuje *just-in-time inicjowania* obsługi administracyjnej użytkowników.

* DocuSign obsługuje [automatyczne inicjowanie obsługi administracyjnej użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial).
* Po skonfigurowaniu docusign można wymusić kontrolę sesji, które chronią eksfiltracji i infiltracji poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-docusign-from-the-gallery"></a>Dodawanie docusign z galerii

Aby skonfigurować integrację docusign z usługą Azure AD, należy dodać DocuSign z galerii do listy zarządzanych aplikacji SaaS:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub szkolnego lub przy użyciu osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji dla przedsiębiorstw,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **DocuSign** w polu wyszukiwania.
1. Wybierz **docusign** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla docusign

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą docuSign przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik sygnatora sygnowania dokumentów sygnowania, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a odpowiednim użytkownikiem w docusign.

Aby skonfigurować i przetestować sygnalizowania usługi Azure AD za pomocą docuSign, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj samouszeńcę usługi Azure AD,](#configure-azure-ad-sso) aby użytkownicy mogli korzystać z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user) aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. [Skonfiguruj logowanie jednokrotne docusign,](#configure-docusign-sso) aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. [Utwórz użytkownika testowego DocuSign,](#create-docusign-test-user) aby wygenerować odpowiednik B.Simon w DocuSign, który jest połączony z reprezentacją usługi Azure AD użytkownika.
1. [Przetestuj sytą próbę sycącą,](#test-sso) aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Aby włączyć usługę Azure AD SSO w witrynie Azure portal, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **DocuSign** znajdź sekcję **Zarządzaj,** a następnie wybierz opcję **logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wykonaj następujące kroki:

    a. W polu **Zaloguj się na adres URL** wprowadź adres URL, używając następującego wzorca:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL, używając następującego wzorca:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Te wartości w nawiasach są symbolami zastępczymi. Zastąp je wartościami w rzeczywistym adresie URL logowania i identyfikatorze. Te szczegóły są wyjaśnione w sekcji "Zobacz PUNKTY KOŃCOWE SAML 2.0" w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą SAML** w sekcji Saml Signing Certificate (Certyfikat **podpisywania SAML)** znajdź **pozycję Certyfikat (Base64).** Wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie docusign** skopiuj odpowiedni adres URL (lub adresy URL) na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B.Simon w witrynie Azure portal.

1. W lewym okienku witryny Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. U góry ekranu wybierz pozycję **Nowy użytkownik**.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź **b.simon**.  
   1. W polu **Nazwa** użytkownika `<username>@<companydomain>.<extension>`wprowadź . Na przykład: `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło,** a następnie zanotuj wartość wyświetlaną w polu **Hasło.**
   1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji udzielisz B.Simon dostępu do DocuSign, dzięki czemu ten użytkownik może używać logowania jednokrotnego platformy Azure.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **DocuSign**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przydziału** wybierz pozycję **Użytkownicy i grupy**.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy **Użytkownicy,** a następnie naciśnij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie naciśnij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** wybierz przycisk **Przypisz.**

## <a name="configure-docusign-sso"></a>Konfigurowanie docusign sygnowania sygn.

1. Aby zautomatyzować konfigurację w docusign, należy zainstalować rozszerzenie przeglądarki My Apps Secure Sign-in, wybierając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki wybierz pozycję **Setup DocuSign**. Zostaniesz przekierowany do aplikacji DocuSign. W tym miejscu podaj poświadczenia administratora, aby zalogować się do DocuSign. Rozszerzenie przeglądarki automatycznie konfiguruje aplikację i automatyzuje kroki od 3 do 5.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz skonfigurować DocuSign ręcznie, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy DocuSign jako administrator.

4. W prawym górnym rogu strony wybierz logo profilu, a następnie wybierz pozycję **Przejdź do administratora**.
  
    ![Przejdź do administratora w obszarze Profil][51]

5. Na stronie rozwiązania domeny wybierz pozycję **Domeny**.

    ![Rozwiązania/domeny domen][50]

6. W sekcji **Domeny** wybierz pozycję **DOMENA OŚWIADCZEŃ**.

    ![Opcja Domena oświadczeń][52]

7. W oknie **dialogowym Zgłaskanie domeny** w polu **Nazwa domeny** wpisz domenę firmową, a następnie wybierz pozycję **RUJ**. Upewnij się, że domena jest aktywna i jej stan jest aktywny.

    ![Odbierz okno dialogowe Nazwa domeny/domeny][53]

8. Na stronie rozwiązania domeny wybierz pozycję **Dostawcy tożsamości**.
  
    ![Opcja Dostawcy tożsamości][54]

9. W sekcji **Dostawcy tożsamości** wybierz pozycję **DODAJ DOSTAWCĘ TOŻSAMOŚCI**.

    ![Opcja Dodaj dostawcę tożsamości][55]

10. Na stronie **Ustawienia dostawcy tożsamości** wykonaj następujące czynności:

    ![Pola Ustawienia dostawcy tożsamości][56]

    a. W polu **Nazwa** wpisz unikatową nazwę konfiguracji. Nie używaj spacji.

    b. W **polu Wystawca dostawcy tożsamości**wklej wartość **identyfikatora usługi Azure AD,** która została skopiowana z witryny Azure portal.

    d. W polu **Adres URL logowania dostawcy tożsamości** wklej wartość adresu URL **logowania,** która została skopiowana z witryny Azure portal.

    d. W polu **Adres URL wylogowania dostawcy tożsamości** wklej wartość adresu URL **wylogowania,** który został skopiowany z witryny Azure portal.

    e. Wybierz **pozycję Podpisz żądanie AuthN**.

    f. Dla **wyślij żądanie AuthN przez**, wybierz **POST**.

    g. Dla **wyślij żądanie wylogowania przez**, wybierz **GET**.

    h. W sekcji **Niestandardowe mapowanie atrybutów** wybierz pozycję **DODAJ NOWE MAPOWANIE**.

       ![Interfejs użytkownika mapowania atrybutów niestandardowych][62]

    i. Wybierz pole, które chcesz zamapować do oświadczenia usługi Azure AD. W tym przykładzie oświadczenie **adresu e-mail** jest `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`mapowane z wartością . To domyślna nazwa oświadczenia z usługi Azure AD dla oświadczenia e-mail. Wybierz **zapisz**.

       ![Pola niestandardowego mapowania atrybutów][57]

       > [!NOTE]
       > Użyj odpowiedniego **identyfikatora użytkownika,** aby zamapować użytkownika z usługi Azure AD do mapowania użytkownika DocuSign. Wybierz odpowiednie pole i wprowadź odpowiednią wartość na podstawie ustawień organizacji.

    j. W sekcji **Certyfikaty dostawcy tożsamości** wybierz pozycję **DODAJ CERTYFIKAT**, przekaż certyfikat pobrany z portalu usługi Azure AD i wybierz pozycję **ZAPISZ**.

       ![Certyfikaty dostawcy tożsamości/dodawanie certyfikatu][58]

    k. W sekcji **Dostawcy tożsamości** wybierz pozycję **AKCJE**, a następnie wybierz pozycję **Punkty końcowe**.

       ![Dostawcy tożsamości/punkty końcowe][59]

    l. W sekcji **Wyświetl punkty końcowe SAML 2.0** portalu administracyjnego DocuSign wykonaj następujące kroki:
       1. Skopiuj **adres URL wystawcy dostawcy usług,** a następnie wklej go do pola **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

       1. Skopiuj **adres URL logowania usługodawcy,** a następnie wklej go do pola Adresu **URL logowania** w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

       1. Wybierz polecenie **Zamknij**.

       ![Wyświetlanie punktów końcowych SAML 2.0][60]

### <a name="create-docusign-test-user"></a>Utwórz użytkownika testowego DocuSign

W tej sekcji użytkownik o nazwie B.Simon jest tworzony w DocuSign. DocuSign obsługuje just-in-time inicjowania obsługi administracyjnej użytkownika, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w DocuSign, nowy jest tworzony po uwierzytelnieniu.

>[!Note]
>Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej DocuSign](https://support.docusign.com/).

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka DocuSign w Panelu dostępu należy automatycznie zalogować się do wystąpienia DocuSign, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj docusign z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png