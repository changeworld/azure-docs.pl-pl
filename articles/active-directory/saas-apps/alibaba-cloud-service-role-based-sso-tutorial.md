---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z usługą Alibaba Cloud Service (logowanie jednokrotne oparte na rolach) | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą sygnoskopową Alibaba (logowanie oparte na rolach).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e22bec224d185d0306f2b0032aef929f627c910e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77367923"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z usługą Alibaba Cloud Service (logowanie jednokrotne oparte na rolach)

W tym samouczku dowiesz się, jak zintegrować usługę Alibaba Cloud Service (samoso) opartą na rolach z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Alibaba Cloud Service (SSO opartej na rolach) z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do usługi Alibaba Cloud Service (samoso oparte na rolach.
* Włącz użytkownikom automatyczne logowanie do usługi Alibaba Cloud Service (samoso) oparte na rolach za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja jednookrotna usługi Alibaba Cloud Service (SSO) oparta na rolach.Alibaba Cloud Service (Role-based SSO) single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Usługa Alibaba Cloud Service (SSO oparta na rolach) obsługuje zainicjowane przez protokół SSO inicjowane przez **protokół IDP**

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Dodawanie usługi Alibaba Cloud Service (SSO opartej na rolach) z galerii

Aby skonfigurować integrację usługi Alibaba Cloud Service (SSO oparte na rolach) z usługą Azure AD, należy dodać usługę Alibaba Cloud Service (SSO opartą na rolach) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Alibaba Cloud Service (SSO oparte na rolach)** w polu wyszukiwania.
1. Wybierz **Alibaba Cloud Service (SSO oparte na rolach)** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.
5. Na stronie **Alibaba Cloud Service (SSO)** kliknij pozycję **Właściwości** w lewym okienku nawigacji, a następnie skopiuj **identyfikator obiektu** i zapisz go na komputerze do późniejszego użycia.

    ![Właściwości config](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-alibaba-cloud-service-role-based-sso"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi Alibaba Cloud Service (logowania jednokrotnego opartego na rolach)

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usługi Alibaba Cloud Service (samoso) opartej na rolach przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sygnali działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze Alibaba Cloud Service (samouszce oparte na rolach).

Aby skonfigurować i przetestować samouszeńców usługi Azure AD z usługą Alibaba Cloud Service (samouczce samosoczeń, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj logowanie jednokrotne oparte na rolach w usłudze Alibaba Cloud Service](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Skonfiguruj logowanie jednokrotne usługi w chmurze Alibaba (logowania jednokrotnego oparte na rolach)](#configure-alibaba-cloud-service-role-based-sso-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Alibaba Cloud Service (SSO oparte na rolach)](#create-alibaba-cloud-service-role-based-sso-test-user)** — aby mieć odpowiednik Britta Simon w alibaba cloud service (samoso oparte na rolach), który jest połączony z reprezentacją użytkownika usługi Azure AD.
3. **[Przetestuj pojedynczy pojedynczy pojedynczy syt-](#test-sso)** aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie integracji aplikacji **Alibaba Cloud Service (SSO oparte na rolach)** znajdź sekcję **Zarządzaj** i wybierz **opcję logowania jednokrotnego**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    >[!NOTE]
    >Metadane usługodawcy zostaną odebrane z tego [adresu URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Kliknij pozycję **Przekaż plik metadanych**.

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    d. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatora** i adresu URL odpowiedzi są automatycznie wypełniane w polu tekstowym sekcji Alibaba Cloud Service (SSO oparte na rolach):Once the metadata file is successfully uploaded, the **Identifier** and Reply URL values get auto populated in Alibaba Cloud Service (Role-based SSO) section textbox:

    > [!Note]
    > Jeśli wartości **adresu URL** **identyfikatora** i odpowiedzi nie są wypełniane automatycznie, wprowadź je ręcznie zgodnie z wymaganiami.

1. Usługa w chmurze Alibaba (samouszce samouszeńców opartych na rolach) wymaga skonfigurowania ról w usłudze Azure AD. Oświadczenie roli jest wstępnie skonfigurowane, więc nie trzeba go skonfigurować, ale nadal trzeba je utworzyć w usłudze Azure AD przy użyciu tego [artykułu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management).

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie usługi w chmurze Alibaba (SSO oparte na rolach)** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do usługi Alibaba Cloud Service (logowania jednokrotnego opartego na rolach).

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Alibaba Cloud Service (Asso oparte na rolach).**
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. Na karcie **Użytkownicy i grupy** wybierz u2 z listy użytkowników i kliknij przycisk **Wybierz**. Następnie kliknij przycisk **Przypisz**.

    ![Test config](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. Wyświetl przypisaną rolę i przetestuj usługę Alibaba Cloud Service (samouszce samouszcowe oparte na rolach).

    ![Test config](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >Po przypisaniu użytkownika (u2) utworzona rola jest automatycznie dołączana do użytkownika. Jeśli utworzono wiele ról, należy dołączyć odpowiednią rolę do użytkownika w razie potrzeby. Jeśli chcesz zaimplementować samoso oparte na rolach z usługi Azure AD na wiele kont Alibaba Cloud, powtórz poprzednie kroki.

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Konfigurowanie logowania jednokrotnego opartego na rolach w usłudze w chmurze Alibaba

1. Zaloguj się do [konsoli Pamięci RAM](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) Alibaba Cloud przy użyciu konta1.

2. W lewym okienku nawigacji wybierz pozycję **SSO**.

3. Na karcie **Samouszce samoso oparte** na rolach kliknij pozycję **Utwórz idP**.

4. Na wyświetlonych stronach `AAD` wprowadź w polu Nazwa IdP wprowadź opis w polu **Uwaga,** kliknij przycisk **Przekaż,** aby przesłać pobrany wcześniej plik metadanych federacji, a następnie kliknij przycisk **OK**.

5. Po pomyślnym utworzeniu IdP kliknij pozycję **Utwórz rolę pamięci RAM**.

6. W polu Nazwa roli `AADrole`pamięci `AAD` **RAM** wprowadź pozycję Wybierz z listy rozwijanej **Wybierz IdP** i kliknij przycisk OK.

    >[!NOTE]
    >W razie potrzeby można udzielić uprawnień do roli. Po utworzeniu IdP i odpowiedniej roli zaleca się zapisanie sieci APN i roli do późniejszego użycia. Nazwy ARN można uzyskać na stronie z informacjami o IdP i na stronie z informacjami o rolach.

7. Skojarz rolę Pamięci RAM w chmurze Alibaba (AADrole) z użytkownikiem usługi Azure AD (u2): Aby skojarzyć rolę pamięci RAM z użytkownikiem usługi Azure AD, należy utworzyć rolę w usłudze Azure AD, wykonując następujące kroki:

    a. Zaloguj się do [Eksploratora wykresów firmy Microsoft](https://developer.microsoft.com/graph/graph-explorer).

    b. Kliknij **przycisk modyfikuj uprawnienia,** aby uzyskać wymagane uprawnienia do tworzenia roli.

    ![Konfig wykresu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    d. Wybierz następujące uprawnienia z listy i kliknij pozycję **Modyfikuj uprawnienia**, jak pokazano na poniższym rysunku.

    ![Konfig wykresu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >Po przyznaniu uprawnień ponownie zaloguj się do Eksploratora wykresów.

    d. Na stronie Eksplorator wykresów wybierz **pozycję POBIERZ** z pierwszej listy rozwijanej i **wersję beta** z drugiej listy rozwijanej. Następnie `https://graph.microsoft.com/beta/servicePrincipals` wprowadź pole obok list rozwijanych i kliknij przycisk **Uruchom kwerendę**.

    ![Konfig wykresu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Jeśli używasz wielu katalogów, `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` można wprowadzić w polu kwerendy.

    e. W **wersji zapoznawczej odpowiedzi** sekcji wyodrębnić appRoles właściwości z "Service principal" do późniejszego użycia.

    ![Konfig wykresu](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Właściwość appRoles można zlokalizować, wprowadzając `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` pole kwerendy. Należy zauważyć, że `objectID` jest identyfikator obiektu skopiowany ze strony **właściwości** usługi Azure AD.

    f. Wróć do Eksploratora wykresu, zmień metodę z **POBIERZ** na **PATCH,** wklej następującą zawartość do sekcji **Treść żądania** i kliknij przycisk **Uruchom kwerendę:**
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > Jest `value` to liczba APN i rola utworzona w konsoli pamięci RAM. W tym miejscu można dodać wiele ról w razie potrzeby. Usługa Azure AD wyśle wartość tych ról jako wartość oświadczenia w odpowiedzi SAML. Można jednak dodawać tylko nowe `msiam_access` role po części dla operacji poprawki. Aby usprawnić proces tworzenia, zaleca się użycie generatora identyfikatorów, takiego jak generator identyfikatorów GUID, do generowania identyfikatorów w czasie rzeczywistym.

    g. Po "Service principal" jest załatana z wymaganej roli, dołączyć rolę z użytkownikiem usługi Azure AD (u2), wykonując kroki **Przypisywanie** sekcji użytkownika testu usługi Azure AD w samouczku.

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>Konfigurowanie usługi Alibaba Cloud Service (samoso posyłek syd) opartej na rolach

Aby skonfigurować logowanie jednokrotne po stronie **usługi Alibaba Cloud Service (logowania jednokrotnego oparte na rolach),** należy wysłać pobrany kod **XML metadanych federacyjnej** i odpowiednie skopiowane adresy URL z witryny Azure portal do [zespołu pomocy technicznej Alibaba Cloud Service (SSO oparte na rolach).](https://www.aliyun.com/service/) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Utwórz użytkownika testowego Usługi w chmurze Alibaba (SSO oparte na rolach)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Alibaba Cloud Service (Samouczeńcowi samouczepne oparte na rolach). Współpracuj z [zespołem pomocy technicznej Alibaba Cloud Service (SSO opartym na rolach),](https://www.aliyun.com/service/) aby dodać użytkowników na platformie Alibaba Cloud Service (Asso oparte na rolach). Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Test SSO 

Po zakończeniu poprzednich konfiguracji przetestuj usługę Alibaba Cloud Service (SSO opartą na rolach), wykonując następujące kroki:

1. W witrynie Azure portal przejdź do strony **Alibaba Cloud Service (SSO oparte na rolach),** wybierz opcję **Logowanie jednokrotne**i kliknij przycisk **Testuj**.

    ![Test config](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Kliknij pozycję **Zaloguj się jako bieżący użytkownik**.

    ![Test config](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. Na stronie wyboru konta wybierz u2.

    ![Test config](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Zostanie wyświetlona następująca strona wskazująca, że samoso oparte na rolach jest skuteczne.

    ![Test config](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Alibaba Cloud Service (samoso oparte na rolach) z usługą Azure AD](https://aad.portal.azure.com/)

