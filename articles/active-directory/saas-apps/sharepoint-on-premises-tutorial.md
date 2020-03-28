---
title: 'Samouczek: Integracja usługi Azure Active Directory z lokalnym programem SharePoint | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i lokalnym programem SharePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 22b673ef481593247b6ee1007c13390a498c66be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048632"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharepoint-on-premises"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z lokalnym programem SharePoint

W tym samouczku dowiesz się, jak zintegrować program SharePoint lokalnie z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu SharePoint lokalnie z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do programu SharePoint lokalnie.
* Włącz użytkownikom automatyczne logowanie do lokalnego programu SharePoint za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) programu SharePoint.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Lokalny program SharePoint obsługuje logowanie jednokrotne inicjowane przez **SP**
* Po skonfigurowaniu programu SharePoint lokalnie można wymusić formanty sesji, które chronią eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
* Zapoznaj się z tym [łączem,](https://docs.microsoft.com/archive/blogs/kaevans/sharepoint-2013-user-profile-sync-for-claims-users) aby dowiedzieć się, jak synchronizować profile użytkowników z lokalnego programu SharePoint z usługą Azure AD

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Dodawanie lokalnego programu SharePoint z galerii

Aby skonfigurować integrację lokalnego programu SharePoint z usługą Azure AD, musisz dodać lokalny program SharePoint z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**

    > [!NOTE]
    > Jeśli element nie powinien być dostępny, można go również otworzyć za pośrednictwem łącza Stałe **wszystkie usługi** w górnej części lewego panelu nawigacyjnego. W poniższym omówieniu **łącze usługi Azure Active Directory** znajduje się w sekcji **Tożsamość** lub można je przeszukiwać za pomocą pola tekstowego filtru.

1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz program **SharePoint lokalnie** w polu wyszukiwania.
1. Wybierz program **SharePoint lokalnie** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sharepoint-on-premises"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla lokalnego programu SharePoint

Konfigurowanie i testowanie samoustawy samoustawne usługi Azure AD za pomocą lokalnego programu SharePoint przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik samouszeńców działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie SharePoint lokalnie.

Aby skonfigurować i przetestować samoustawę samoustawną usługi Azure AD za pomocą lokalnego programu SharePoint, wykonaj następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w lokalnym programie SharePoint](#configure-sharepoint-on-premises-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Utwórz grupę zabezpieczeń usługi Azure AD w witrynie Azure portal](#create-an-azure-ad-security-group-in-the-azure-portal)** — aby włączyć nową grupę zabezpieczeń w usłudze Azure AD do logowania jednokrotnego.
5. **[Udziel dostępu do lokalnej grupy zabezpieczeń programu SharePoint](#grant-access-to-sharepoint-on-premises-security-group)** — udziel dostępu dla określonej grupy do usługi Azure AD.
6. **[Przypisz grupę zabezpieczeń usługi Azure AD w portalu Azure](#assign-the-azure-ad-security-group-in-the-azure-portal)** — aby przypisać określoną grupę do usługi Azure AD w celu uwierzytelnienia.
7. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą lokalnego programu SharePoint, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Lokalny program SharePoint** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:


    a. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://<YourSharePointServerURL>/_trust/default.aspx`

    b. W polu **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`urn:sharepoint:federation`

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta lokalnego programu SharePoint](https://support.office.com/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

    > [!Note]
    > Zanotuj ścieżkę pliku, do którego został pobrany plik certyfikatu, ponieważ będzie ona używana później w skrypcie programu PowerShell na potrzeby konfiguracji.

6. W sekcji **Skonfiguruj lokalny program SharePoint ** skopiuj odpowiednie adresy URL zgodnie z wymaganiami. W polu **Adres URL usługi logowania jednokrotnego** użyj wartości następującego wzorca: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ to identyfikator dzierżawy subskrypcji usługi Azure AD.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

    > [!NOTE]
    > Aplikacja Lokalny program SharePoint używa tokenu SAML 1.1, dzięki czemu usługa Azure AD oczekuje żądania usługi WS-Fed z serwera programu SharePoint, a po uwierzytelnieniu wystawia token SAML 1.1 .

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w lokalnym programie SharePoint

1. W innym oknie przeglądarki sieci Web zaloguj się do lokalnej witryny firmy programu SharePoint jako administrator.

2. **Konfigurowanie nowego zaufanego dostawcy tożsamości w programie SharePoint Server 2016**

    Zaloguj się do programu SharePoint Server 2016, a następnie otwórz powłokę zarządzania programu SharePoint 2016. Wypełnij wartości $realm (wartość identyfikatora z sekcji domeny i adresów URL lokalnego programu SharePoint w witrynie Azure Portal), $wsfedurl (adres URL usługi logowania jednokrotnego) i $filepath (ścieżka pliku, do którego został pobrany plik certyfikatu) z witryny Azure Portal i uruchom następujące polecenia, aby skonfigurować nowego zaufanego dostawcy tożsamości.

    > [!TIP]
    > Jeśli dopiero zaczynasz pracę z programem PowerShell lub chcesz dowiedzieć się więcej na temat tego, jak działa program PowerShell, zobacz [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4,$map5 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Następnie wykonaj następujące kroki, aby włączyć zaufanego dostawcę tożsamości dla aplikacji:

    a. W witrynie Administracja centralna przejdź do sekcji **Zarządzanie aplikacją internetową** i wybierz aplikację internetową, którą chcesz zabezpieczyć za pomocą usługi Azure AD.

    b. Na wstążce kliknij pozycję **Dostawcy uwierzytelniania** i wybierz strefę, której chcesz użyć.

    d. Kliknij pozycję **Trusted Identity Provider** (Zaufany dostawca tożsamości), a następnie kliknij pozycję *Add Trusted Identity Provider* (Dodaj zaufanego dostawcę tożsamości).

    d. W ustawieniu adresu URL strony logowania wybierz pozycję **Niestandardowa strona logowania** i podaj wartość "/_trust/".

    e. Kliknij przycisk **OK**.

    ![Konfigurowanie dostawcy uwierzytelniania](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Niektórzy użytkownicy zewnętrzni nie będą w stanie korzystać z tej integracji logowania jednokrotnego, ponieważ ich nazwa UPN będzie miała zniekształconą wartość, podobną do `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Wkrótce umożliwimy klientom konfigurację aplikacji w celu określenia sposobu obsługi nazwy UPN w zależności od typu użytkownika. Wtedy wszyscy użytkownicy-goście powinni móc bezproblemowo korzystać z logowania jednokrotnego jako pracownicy organizacji.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension`  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Tworzenie grupy zabezpieczeń usługi Azure AD w witrynie Azure portal

1. Kliknij **pozycję Azure Active Directory > wszystkie grupy**.

    ![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Kliknij **pozycję Nowa grupa**:

    ![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Wypełnij **typ grupy,** **Nazwa grupy,** **Opis grupy,** **Typ członkostwa**. Kliknij strzałkę, aby wybrać członków, a następnie wyszukaj lub kliknij członka, którego chcesz dodać do grupy. Kliknij **wybierz,** aby dodać wybranych członków, a następnie kliknij **przycisk Utwórz**.

    ![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Aby przypisać lokalne grupy zabezpieczeń usługi Azure Active Directory do lokalnego programu SharePoint, konieczne będzie zainstalowanie i skonfigurowanie [usługi AzureCP](https://yvand.github.io/AzureCP/) w lokalnej farmie programu SharePoint LUB opracowanie i skonfigurowanie alternatywnego dostawcy oświadczeń niestandardowych dla programu SharePoint.  Zobacz sekcję więcej informacji na końcu dokumentu do tworzenia własnego dostawcy oświadczeń niestandardowych, jeśli nie używasz usługi AzureCP.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Udzielanie dostępu lokalnej grupie zabezpieczeń programu SharePoint

**Konfigurowanie grup zabezpieczeń i uprawnień w rejestracji aplikacji**

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Rejestracje aplikacji**.

    ![Blok Aplikacje dla przedsiębiorstw](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. W polu wyszukiwania wpisz i wybierz pozycję **SharePoint lokalnie**.

    ![Lokalny program SharePoint na liście wyników](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Kliknij **manifest**.

    ![Opcja Manifestu](./media/sharepoint-on-premises-tutorial/manifest.png)

4. `groupMembershipClaims`Modyfikacja `NULL`: `groupMembershipClaims` `SecurityGroup`, Do : . Następnie kliknij zapisz

    ![Edytuj manifest](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Kliknij **ustawienia**, a następnie kliknij **wymagane uprawnienia**.

    ![Wymagane uprawnienia](./media/sharepoint-on-premises-tutorial/settings.png)

6. Kliknij **przycisk Dodaj,** a następnie **wybierz interfejs API**.

    ![Dostęp do interfejsu API](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Dodaj zarówno **usługę Windows Azure Active Directory,** jak i interfejs API programu Microsoft **Graph**, ale można wybrać tylko jeden naraz.

    ![Wybierz interfejs API](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Wybierz pozycję Windows Azure Active Directory, sprawdź odczyt danych katalogu i kliknij pozycję Wybierz. Wróć i dodaj program Microsoft Graph i wybierz pozycję Odczytuj dla niego dane katalogu.  Kliknij wybierz i kliknij gotowe.

    ![Włączanie dostępu](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Teraz w obszarze Wymagane ustawienia kliknij **pozycję Udziel uprawnień,** a następnie kliknij przycisk Tak, aby udzielić uprawnień.

    ![Udziel uprawnień](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Sprawdź w obszarze powiadomienia, aby ustalić, czy uprawnienia zostały pomyślnie przyznane.  Jeśli tak nie jest, usługa AzureCP nie będzie działać poprawnie i nie będzie można skonfigurować lokalnego programu SharePoint za pomocą grup zabezpieczeń usługi Azure Active Directory.

10. Skonfiguruj usługę AzureCP w lokalnej farmie programu SharePoint lub w alternatywnym rozwiązaniu dostawcy oświadczeń niestandardowych.  W tym przykładzie używamy usługi AzureCP.

    > [!NOTE]
    > Należy pamiętać, że usługa AzureCP nie jest produktem firmy Microsoft ani obsługiwana przez dział pomocy technicznej firmy Microsoft. Pobieranie, instalowanie i konfigurowanie usługi AzureCP w lokalnej farmie programu SharePoint[https://yvand.github.io/AzureCP/](https://yvand.github.io/AzureCP/) 

11. **Udziel dostępu do grupy zabezpieczeń usługi Azure Active Directory w lokalnym programie SharePoint** :- Grupom musi zostać przyznany dostęp do aplikacji w programie SharePoint lokalnie.  Aby ustawić uprawnienia dostępu do aplikacji sieci web, należy wykonać następujące kroki.

12. W administracji centralnej kliknij zarządzanie aplikacjami, zarządzaj aplikacjami internetowymi, a następnie wybierz aplikację internetową, aby aktywować wstążkę i kliknij zasady użytkownika.

    ![Administracja centralna](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. W obszarze Zasady dotyczące aplikacji sieci Web kliknij pozycję Dodaj użytkowników, a następnie wybierz strefę, kliknij przycisk Dalej.  Kliknij książkę adresową.

    ![Zasady dotyczące aplikacji sieci Web](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Następnie wyszukaj i dodaj grupę zabezpieczeń usługi Azure Active Directory i kliknij przycisk OK.

    ![Dodawanie grupy zabezpieczeń](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Wybierz uprawnienia, a następnie kliknij przycisk Zakończ.

    ![Dodawanie grupy zabezpieczeń](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Zobacz w obszarze Zasady dla aplikacji sieci Web dodawana jest grupa usługi Azure Active Directory.  Oświadczenie grupy pokazuje identyfikator obiektu grupy zabezpieczeń usługi Azure Active Directory dla nazwy użytkownika.

    ![Dodawanie grupy zabezpieczeń](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Przejdź do zbioru witryn programu SharePoint i dodaj tam również grupę. Kliknij pozycję Ustawienia witryny, a następnie kliknij pozycję Uprawnienia witryny i Udziel uprawnień.  Wyszukaj oświadczenie o roli grupy, przypisz poziom uprawnień i kliknij pozycję Udostępnij.

    ![Dodawanie grupy zabezpieczeń](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurowanie jednego zaufanego dostawcy tożsamości dla wielu aplikacji internetowych

Konfiguracja działa w przypadku jednej aplikacji internetowej, ale wymaga dodatkowej konfiguracji, jeśli zamierzasz używać tego samego zaufanego dostawcy tożsamości dla wielu aplikacji internetowych. Załóżmy na przykład, że rozszerzyliśmy aplikację internetową w celu używania adresu URL `https://portal.contoso.local` i teraz chcemy również uwierzytelniać użytkowników w `https://sales.contoso.local`. Aby to zrobić, musimy zaktualizować dostawcę tożsamości, aby uwzględnić parametr WReply i zaktualizować rejestrację aplikacji w usłudze Azure AD w celu dodania adresu URL odpowiedzi.

1. W witrynie Azure portal otwórz katalog usługi Azure AD. Kliknij pozycję **Rejestracje aplikacji**, następnie kliknij pozycję **Wyświetl wszystkie aplikacje**. Kliknij aplikację, która została utworzony wcześniej (Integracja programu SharePoint z protokołem SAML).

2. Kliknij przycisk **Ustawienia**.

3. W bloku ustawień kliknij pozycję **Adresy URL odpowiedzi**.

4. Dodaj adres URL dla dodatkowej aplikacji internetowej z ciągiem `/_trust/default.aspx` dołączonym do adresu URL (na przykład `https://sales.contoso.local/_trust/default.aspx`) i kliknij pozycję **Zapisz**.

5. W programie SharePoint Server otwórz **powłokę zarządzania programu SharePoint 2016** i wykonaj następujące polecenia, używając nazwy zaufanego wystawcy tokenów tożsamości, której użyto poprzednio.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```

6. W witrynie Administracja centralna przejdź do aplikacji internetowej i włącz istniejącego zaufanego dostawcy tożsamości. Pamiętaj, aby skonfigurować również adres URL strony logowania jako niestandardową stronę logowania `/_trust/`.

7. W witrynie Administracja centralna kliknij aplikację internetową, a następnie wybierz pozycję **Zasady użytkownika**. Dodaj użytkownika z odpowiednimi uprawnieniami, jak przedstawiono wcześniej w tym artykule.

### <a name="fixing-people-picker"></a>Naprawianie selektora osób

Użytkownicy mogą teraz logować się do programu SharePoint 2016 przy użyciu tożsamości z usługi Azure AD, ale nadal istnieją możliwości poprawy środowiska użytkownika. Na przykład wyszukiwanie użytkownika przedstawia wiele wyników wyszukiwania w selektorze osób. Istnieją wyniki wyszukiwania dla każdego z trzech typów oświadczeń, które zostały utworzone w mapowaniu oświadczeń. Aby wybrać użytkownika za pomocą selektora osób, należy dokładnie wpisać jego nazwę użytkownika i wybrać wynik oświadczenia **name**.

![Wyniki wyszukiwania oświadczeń](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Nie ma weryfikacji wyszukiwanych wartości, co może prowadzić do błędów pisowni lub przypadkowego wybierania przez użytkowników nieprawidłowego typu oświadczenia, takiego jak oświadczenie **SurName**. Może to uniemożliwić użytkownikom pomyślny dostęp do zasobów.

Aby pomóc w tym scenariuszu, istnieje rozwiązanie typu open-source o nazwie [AzureCP](https://yvand.github.io/AzureCP/), które zapewnia dostawcę oświadczeń niestandardowych dla programu SharePoint 2016. Użyje interfejsu API programu Microsoft Graph, aby rozpoznać, co użytkownicy wejdą i wykonają sprawdzanie poprawności. Dowiedz się więcej o rozwiązaniu [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Przypisywanie grupy zabezpieczeń usługi Azure AD w witrynie Azure portal

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Lokalny program SharePoint**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz **Lokalny program SharePoint**.

    ![Link lokalnego programu SharePoint na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. Wyszukaj grupę zabezpieczeń, której chcesz użyć, a następnie kliknij grupę, aby dodać ją do sekcji Wybierz członków. Kliknij **pozycję Zaznacz**, a następnie kliknij pozycję **Przypisz**.

    ![Grupa zabezpieczeń wyszukiwania](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Sprawdź powiadomienia na pasku menu, aby otrzymywać powiadomienia, że grupa została pomyślnie przypisana do aplikacji Enterprise w witrynie Azure portal.

### <a name="create-sharepoint-on-premises-test-user"></a>Tworzenie użytkownika testowego lokalnego programu SharePoint

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w lokalnym programie SharePoint. Pracuj z [lokalnym zespołem pomocy technicznej programu SharePoint,](https://support.office.com/) aby dodać użytkowników na platformie lokalnej programu SharePoint. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka lokalnego programu SharePoint na panelu dostępu powinno nastąpić automatyczne zalogowanie do lokalnego programu SharePoint, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)