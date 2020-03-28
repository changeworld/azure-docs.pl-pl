---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z f5 | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9c5fb47a-1c5d-437a-b4c1-dbf739eaf5e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 984fd0c7946a50922315269c87e08b1c35b74348
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74074755"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z F5

W tym samouczku dowiesz się, jak zintegrować F5 z usługą Azure Active Directory (Azure AD). Po zintegrowaniu F5 z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do F5.
* Włącz użytkownikom automatyczne logowanie do F5 za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* F5 rejestracji jednokrotnej (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* F5 obsługuje **sp i idp** zainicjowane SSO
* F5 SSO można skonfigurować na trzy różne sposoby.

- [Konfigurowanie logowania jednokrotnego F5 dla zaawansowanej aplikacji Protokołu Kerberos](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji opartej na nagłówku](headerf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji Kerberos](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Dodawanie F5 z galerii

Aby skonfigurować integrację F5 z usługą Azure AD, należy dodać F5 z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **klawisz F5** w polu wyszukiwania.
1. Wybierz **klawisz F5** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla F5

Konfigurowanie i testowanie usługi Azure AD SSO z F5 przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w F5.

Aby skonfigurować i przetestować syg jako jeden na wirtualną usługę Azure ad za pomocą f5, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj F5-SSO](#configure-f5-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego F5](#create-f5-test-user)** — aby mieć odpowiednik B.Simon w F5, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **F5** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<YourCustomFQDN>.f5.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<YourCustomFQDN>.f5.com/`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta F5,](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie F5** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do F5.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **F5**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-f5-sso"></a>Konfigurowanie sytego syda dla F5

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji opartej na nagłówku](headerf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji Kerberos](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Konfigurowanie logowania jednokrotnego F5 dla zaawansowanej aplikacji Protokołu Kerberos

1. Otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy F5 (Advanced Kerberos) jako administrator i wykonaj następujące czynności:

1. Należy zaimportować certyfikat metadanych do F5 (Zaawansowane Kerberos), który będzie używany w dalszej części procesu instalacji. Przejdź do **listy certyfikatów >> certyfikatów SSL zarządzanie certyfikatami > systemowymi > >> .** Kliknij **przycisk Importuj** prawy róg.

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Aby skonfigurować dostawcę tożsamości SAML, przejdź do **strony Access > Federation > SAML Service Provider > Create > From Metadata**.

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure02.png)

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure04.png)

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Określanie certyfikatu przesłanego z zadania 3

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure06.png)

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure07.png)

 1. Aby skonfigurować dodatek SP SAML, przejdź do **usługi > Federation access > SAML Service Federation > Local SP Services > Create**.

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Kliknij przycisk **OK**.

1. Wybierz konfigurację sp i kliknij przycisk **Bind/UnBind IdP Connectors**.

     ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Kliknij **pozycję Dodaj nowy wiersz** i wybierz **łącznik zewnętrzny IdP** utworzony w poprzednim kroku.

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Do konfigurowania logowania jednokrotnego protokołu Kerberos, **programu Access > logowania jednokrotnego > protokołu Kerberos**

    >[!Note]
    >Konto delegowania Protokołu Kerberos będzie potrzebne do utworzenia i określenia. Zapoznaj się z sekcją KCD (Zapoznaj się z dodatkiem do odniesień do zmiennych)

    • Źródło nazwy użytkownika`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Źródło obszaru użytkownika`session.logon.last.domain`

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure11.png)

1. Aby skonfigurować profil programu Access, **program Access > Profil/Zasady > profilu dostępu (na zasady sesji).**

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure12.png)

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure13.png)

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure14.png)

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure15.png)

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logn.last.usernameUPN expr {[mcget {session.saml.last.identity}]}

    * session.ad.lastactualdomain TEKST superdemo.live

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logn.last.usernameUPN})

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure18.png)

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logn.last.username expr { "[mcget {session.ad.last.attr.sAMAccountName}]" } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } } }

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.loginn.last.username}
    * mcget {session.logn.last.password}

1. Aby dodać nowy węzeł, przejdź do **> > listy węzłów > węzł >ów**ruchu lokalnego + .

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Aby utworzyć nową pulę, przejdź do lokalizacji **> puli > > tworzenie.**

     ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure22.png)

 1. Aby utworzyć nowy serwer wirtualny, przejdź do > **> listy serwerów wirtualnych > local traffic > virtual server list +**.

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure23.png)

1. Określ profil dostępu utworzony w poprzednim kroku.

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Konfigurowanie delegowania protokołu Kerberos 

>[!Note]
>Więcej informacji znajdziesz [tutaj](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

* **Krok 1: Tworzenie konta delegowania**

    * Przykład
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **Krok 2: Ustaw nazwę SPN (na koncie delegowania APM)**

    *  Przykład
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **Krok 3: Delegowanie SPN (dla konta usługi app service)**

    * Konfigurowanie odpowiedniej delegacji dla konta delegowania F5.
    * W poniższym przykładzie konto delegowania APM jest konfigurowane dla aplikacji KCD dla aplikacji FRP-App1.superdemo.live.

        ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure25.png)

1. Podać szczegóły wymienione w powyższym dokumencie referencyjnym [this](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

1. Załącznik- SAML – F5 BIG-IP Zmienne mapowania pokazane poniżej:

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure26.png)

    ![Konfiguracja F5 (Zaawansowane Kerberos)](./media/advance-kerbf5-tutorial/configure27.png) 

1. Poniżej znajduje się cała lista domyślnych atrybutów SAML. GivenName jest reprezentowana przy użyciu następującego ciągu.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| | |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | `<TENANT ID>` |
| eb46b6b6.session.saml.last.assertionIssueInstant  | `<ID>` |
| eb46b6b6.session.saml.last.assertionWynik | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/displayname | użytkownik0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | użytkownik0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience eb46b6b6.session.saml.last.audience eb46b6b6.session.saml.last.audience eb4 | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oasis:names:tc:SAML:2.0:ac:classes:Password |
| eb46b6b6.session.saml.last.authNInstant eb46b6b6.session.saml.last.authNInstant eb46b6b6.session.saml.last.authNInstant eb4 | `<ID>` |
| eb46b6b6.session.saml.last.identity eb46b6b6.session.saml.last.identity eb46b6b6.session.saml.last.identity eb4 | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.inResponseTo | `<TENANT ID>` |
| eb46b6b6.session.saml.last.nameIDValue | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.nameIdFormat | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| eb46b6b6.session.saml.last.responseSestynation | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId eb46b6b6.session.saml.last.responseId eb46b6b6.session.saml.last.responseId eb4 | `<TENANT ID>` |
| eb46b6b6.session.saml.last.responseIssueInstant | `<ID>` |
| eb46b6b6.session.saml.last.responseWynik | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result eb46b6b6.session.saml.last | 1 |
| eb46b6b6.session.saml.last.samlVersion eb46b6b6.session.saml.last.samlVersion eb46b6b6.session.saml.last.samlVersion eb4 | 2.0 |
| eb46b6b6.session.saml.last.sessionIndex | `<TENANT ID>` |
| eb46b6b6.session.saml.last.statusValue | urn:oasis:names:tc:SAML:2.0:status:Success |
| eb46b6b6.session.saml.last.subjectConfirmaDatOnOrter | `<ID>` |
| eb46b6b6.session.saml.last.subjectKonfirmaDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectKonfirmametod | urn:oasis:names:tc:SAML:2.0:cm:okaziciela |
| eb46b6b6.session.saml.last.validityNotBefore eb46b6b6.session.saml.last.validityNotBefore eb46b6.session.saml.last.validityNotBefore eb46b | `<ID>` |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>Tworzenie użytkownika testowego F5

W tej sekcji utworzysz użytkownika o nazwie B.Simon w F5. Współpracuj z [zespołem pomocy technicznej klienta F5,](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) aby dodać użytkowników na platformie F5. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka F5 w Panelu dostępu należy automatycznie zalogować się do F5, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj F5 z usługą Azure AD](https://aad.portal.azure.com/)

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji opartej na nagłówku](headerf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego F5 dla aplikacji Kerberos](kerbf5-tutorial.md)

