---
title: 'Samouczek: Integracja usługi Azure Active Directory z lokalnym programem SharePoint | Microsoft Docs'
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
ms.devlang: na
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: jeedes
ms.openlocfilehash: 9c956f89d890f93a887d2412c74c906095acf4db
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164362"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Samouczek: Integracja usługi Azure Active Directory z lokalnym programem SharePoint

Z tego samouczka dowiesz się, jak zintegrować lokalny program SharePoint z usługą Azure Active Directory (Azure AD).
Zintegrowanie lokalnego programu SharePoint z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do lokalnego programu SharePoint.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do lokalnego programu SharePoint (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z lokalnym programem SharePoint potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Subskrypcja lokalnego programu SharePoint z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Lokalny program SharePoint obsługuje logowanie jednokrotne inicjowane przez **SP**

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Dodawanie lokalnego programu SharePoint z galerii

Aby skonfigurować integrację lokalnego programu SharePoint z usługą Azure AD, musisz dodać lokalny program SharePoint z galerii do swojej listy zarządzanych aplikacji SaaS.

**Aby dodać lokalny program SharePoint z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](common/select-azuread.png)

    > [!NOTE]   
    > Jeśli element nie powinien być dostępny, można go również otworzyć za pomocą linku naprawione **wszystkie usługi** w górnej części lewego panelu nawigacji. W poniższym omówieniu link **Azure Active Directory** znajduje się w sekcji **tożsamość** lub można go wyszukać przy użyciu pola tekstowego filtr.

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Lokalny program SharePoint**, wybierz pozycję **Lokalny program SharePoint** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

    ![Lokalny program SharePoint na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z lokalnym programem SharePoint, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem lokalnego programu SharePoint.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z lokalnym programem SharePoint, należy ukończyć następujące bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w lokalnym programie SharePoint](#configure-sharepoint-on-premises-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. Aby włączyć logowanie jednokrotne w usłudze Azure AD **[, Utwórz w Azure Portal grupę zabezpieczeń usługi Azure AD](#create-an-azure-ad-security-group-in-the-azure-portal)** .
5. **[Przyznaj dostęp do lokalnej grupy zabezpieczeń programu SharePoint](#grant-access-to-sharepoint-on-premises-security-group)** — Przyznaj dostęp dla określonej grupy do usługi Azure AD.
6. **[Przypisz grupę zabezpieczeń usługi Azure AD w Azure Portal](#assign-the-azure-ad-security-group-in-the-azure-portal)** — aby przypisać konkretną grupę do usługi Azure AD na potrzeby uwierzytelniania.
7. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą lokalnego programu SharePoint, wykonaj następujące czynności:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **Lokalny program SharePoint** wybierz pozycję **Logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL lokalnego programu SharePoint na potrzeby logowania jednokrotnego](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `urn:sharepoint:federation`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta lokalnego programu SharePoint](https://support.office.com/). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

    > [!Note]
    > Zanotuj ścieżkę pliku, do którego został pobrany plik certyfikatu, ponieważ będzie ona używana później w skrypcie programu PowerShell na potrzeby konfiguracji.

6. W sekcji **Skonfiguruj lokalny program SharePoint**  skopiuj odpowiednie adresy URL zgodnie z wymaganiami. W polu **Adres URL usługi logowania jednokrotnego** użyj wartości następującego wzorca: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ to identyfikator dzierżawy subskrypcji usługi Azure AD.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    > [!NOTE]
    > Aplikacja Lokalny program SharePoint używa tokenu SAML 1.1, dzięki czemu usługa Azure AD oczekuje żądania usługi WS-Fed z serwera programu SharePoint, a po uwierzytelnieniu wystawia token SAML 1.1 .

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w lokalnym programie SharePoint

1. W innym oknie przeglądarki sieci Web Zaloguj się do lokalnej witryny firmowej programu SharePoint jako administrator.

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

    c. Kliknij pozycję **Trusted Identity Provider** (Zaufany dostawca tożsamości), a następnie kliknij pozycję *Add Trusted Identity Provider* (Dodaj zaufanego dostawcę tożsamości).

    d. W ustawieniu adresu URL strony logowania wybierz pozycję **Niestandardowa strona logowania** i podaj wartość "/_trust/".

    e. Kliknij przycisk **OK**.

    ![Konfigurowanie dostawcy uwierzytelniania](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Niektórzy użytkownicy zewnętrzni nie będą w stanie korzystać z tej integracji logowania jednokrotnego, ponieważ ich nazwa UPN będzie miała zniekształconą wartość, podobną do `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Wkrótce umożliwimy klientom konfigurację aplikacji w celu określenia sposobu obsługi nazwy UPN w zależności od typu użytkownika. Wtedy wszyscy użytkownicy-goście powinni móc bezproblemowo korzystać z logowania jednokrotnego jako pracownicy organizacji.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz`brittasimon@yourcompanydomain.extension`  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Utwórz grupę zabezpieczeń usługi Azure AD w Azure Portal

1. Kliknij **Azure Active Directory > wszystkie grupy**.

    ![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Kliknij pozycję **Nowa grupa**:

    ![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Wypełnij pola **Typ grupy**, **Nazwa grupy**, **Opis grupy**, **Typ członkostwa**. Kliknij strzałkę, aby wybrać elementy członkowskie, a następnie wyszukaj lub kliknij element członkowski, który chcesz dodać do grupy. Kliknij pozycję **Wybierz** , aby dodać wybrane elementy członkowskie, a następnie kliknij pozycję **Utwórz**.

    ![Tworzenie grupy zabezpieczeń usługi Azure AD](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Aby można było przypisywać Azure Active Directory grupy zabezpieczeń do lokalnego programu SharePoint, konieczne będzie zainstalowanie i skonfigurowanie usługi [AzureCP](https://yvand.github.io/AzureCP/) w lokalnej farmie programu SharePoint lub opracowanie i skonfigurowanie alternatywnego niestandardowego dostawcy oświadczeń dla programu SharePoint.  Zapoznaj się z sekcją więcej informacji na końcu dokumentu w celu utworzenia własnego niestandardowego dostawcy oświadczeń, jeśli nie używasz AzureCP.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Udzielanie dostępu do lokalnej grupy zabezpieczeń programu SharePoint

**Konfigurowanie grup zabezpieczeń i uprawnień do rejestracji aplikacji**

1. W Azure Portal wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **rejestracje aplikacji**.

    ![Blok Aplikacje dla przedsiębiorstw](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. W polu wyszukiwania wpisz i wybierz pozycję **SharePoint lokalnie**.

    ![Lokalny program SharePoint na liście wyników](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Kliknij **manifest**.

    ![Opcja manifestu](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Modyfikuj `groupMembershipClaims`: `NULL`, do `groupMembershipClaims`: .`SecurityGroup` Następnie kliknij pozycję Zapisz

    ![Edytuj manifest](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Kliknij pozycję **Ustawienia**, a następnie kliknij pozycję **wymagane uprawnienia**.

    ![Wymagane uprawnienia](./media/sharepoint-on-premises-tutorial/settings.png)

6. Kliknij przycisk **Dodaj** , a następnie **Wybierz interfejs API**.

    ![Dostęp do interfejsu API](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Dodaj zarówno **Azure Active Directory Windows** , jak i **interfejs API Microsoft Graph**, ale można tylko wybrać jeden z nich naraz.

    ![Wybór interfejsu API](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Wybierz pozycję Windows Azure Active Directory, sprawdź Odczytaj dane katalogu, a następnie kliknij pozycję Wybierz. Wróć i Dodaj Microsoft Graph i zaznacz również opcję Odczytaj dane katalogu.  Kliknij pozycję Wybierz, a następnie kliknij pozycję Gotowe.

    ![Włączanie dostępu](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Teraz w obszarze wymagane ustawienia kliknij pozycję **Udziel uprawnień** , a następnie kliknij przycisk tak, aby przyznać uprawnienia.

    ![Udziel uprawnień](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Zaznacz opcję powiadomienia, aby określić, czy uprawnienia zostały pomyślnie przyznane.  Jeśli tak nie jest, AzureCP nie będzie działać prawidłowo i nie będzie możliwe skonfigurowanie lokalnego programu SharePoint przy użyciu grup zabezpieczeń Azure Active Directory.

10. Skonfiguruj AzureCP w farmie lokalnej programu SharePoint lub alternatywne rozwiązanie niestandardowego dostawcy oświadczeń.  W tym przykładzie korzystamy z AzureCP.

    > [!NOTE]
    > Należy pamiętać, że AzureCP nie jest produktem firmy Microsoft ani nie jest obsługiwany przez pomoc techniczną firmy Microsoft. Pobierz, zainstaluj i skonfiguruj AzureCP w lokalnej farmie programu SharePoint na https://yvand.github.io/AzureCP/ 

11. **Przyznaj dostęp do Azure Active Directory grupy zabezpieczeń w lokalnym programie SharePoint** : — grupy muszą mieć udzielony dostęp do aplikacji w lokalnym programie SharePoint.  Wykonaj następujące kroki, aby ustawić uprawnienia dostępu do aplikacji sieci Web.

12. W obszarze Administracja centralna kliknij pozycję Zarządzanie aplikacjami, Zarządzaj aplikacjami sieci Web, a następnie wybierz aplikację sieci Web, aby aktywować Wstążkę, a następnie kliknij pozycję zasady użytkownika.

    ![Administracja centralna](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. W obszarze zasady dla aplikacji sieci Web kliknij pozycję Dodaj użytkowników, a następnie wybierz strefę, a następnie kliknij przycisk Dalej.  Kliknij książkę adresową.

    ![Zasady dla aplikacji sieci Web](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Następnie wyszukaj i Dodaj Azure Active Directory grupę zabezpieczeń i kliknij przycisk OK.

    ![Dodawanie grupy zabezpieczeń](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Wybierz uprawnienia, a następnie kliknij przycisk Zakończ.

    ![Dodawanie grupy zabezpieczeń](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. W obszarze zasady dla aplikacji sieci Web zostanie dodana grupa Azure Active Directory.  W polu Nazwa użytkownika jest wyświetlany Azure Active Directory identyfikator obiektu grupy zabezpieczeń.

    ![Dodawanie grupy zabezpieczeń](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Przejdź do kolekcji witryn programu SharePoint i Dodaj również grupę. Kliknij pozycję Ustawienia witryny, a następnie kliknij pozycję uprawnienia lokacji i Udziel uprawnień.  Wyszukaj pozycję rola grupy, przypisz poziom uprawnień, a następnie kliknij pozycję Udostępnij.

    ![Dodawanie grupy zabezpieczeń](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurowanie jednego zaufanego dostawcy tożsamości dla wielu aplikacji internetowych

Konfiguracja działa w przypadku jednej aplikacji internetowej, ale wymaga dodatkowej konfiguracji, jeśli zamierzasz używać tego samego zaufanego dostawcy tożsamości dla wielu aplikacji internetowych. Załóżmy na przykład, że rozszerzyliśmy aplikację internetową w celu używania adresu URL `https://portal.contoso.local` i teraz chcemy również uwierzytelniać użytkowników w `https://sales.contoso.local`. Aby to zrobić, musimy zaktualizować dostawcę tożsamości, aby uwzględnić parametr WReply i zaktualizować rejestrację aplikacji w usłudze Azure AD w celu dodania adresu URL odpowiedzi.

1. W Azure Portal Otwórz katalog usługi Azure AD. Kliknij pozycję **Rejestracje aplikacji**, następnie kliknij pozycję **Wyświetl wszystkie aplikacje**. Kliknij aplikację, która została utworzony wcześniej (Integracja programu SharePoint z protokołem SAML).

2. Kliknij pozycję **Ustawienia**.

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

Użytkownicy mogą teraz logować się do programu SharePoint 2016 przy użyciu tożsamości z usługi Azure AD, ale nadal istnieją okazje do ulepszenia środowiska użytkownika. Na przykład wyszukiwanie użytkownika przedstawia wiele wyników wyszukiwania w selektorze osób. Istnieją wyniki wyszukiwania dla każdego z trzech typów oświadczeń, które zostały utworzone w mapowaniu oświadczeń. Aby wybrać użytkownika za pomocą selektora osób, należy dokładnie wpisać jego nazwę użytkownika i wybrać wynik oświadczenia **name**.

![Wyniki wyszukiwania oświadczeń](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Nie ma weryfikacji wyszukiwanych wartości, co może prowadzić do błędów pisowni lub przypadkowego wybierania przez użytkowników nieprawidłowego typu oświadczenia, takiego jak oświadczenie **SurName**. Może to uniemożliwić użytkownikom pomyślny dostęp do zasobów.

Aby pomóc w tym scenariuszu, istnieje rozwiązanie typu open-source o nazwie [AzureCP](https://yvand.github.io/AzureCP/), które zapewnia dostawcę oświadczeń niestandardowych dla programu SharePoint 2016. Będzie ono korzystało z funkcji Azure AD Graph do rozpoznawania wartości wprowadzanych przez użytkowników i wykonywania weryfikacji. Dowiedz się więcej o rozwiązaniu [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Przypisz grupę zabezpieczeń usługi Azure AD w Azure Portal

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Lokalny program SharePoint**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz **Lokalny program SharePoint**.

    ![Link lokalnego programu SharePoint na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. Wyszukaj grupę zabezpieczeń, której chcesz użyć, a następnie kliknij grupę, aby dodać ją do sekcji Wybierz członków. Kliknij pozycję **Wybierz**, a następnie kliknij pozycję **Przypisz**.

    ![Wyszukaj grupę zabezpieczeń](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Sprawdź powiadomienia na pasku menu, aby otrzymywać powiadomienia o pomyślnym przypisaniu grupy do aplikacji przedsiębiorstwa w Azure Portal.

### <a name="create-sharepoint-on-premises-test-user"></a>Tworzenie użytkownika testowego lokalnego programu SharePoint

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w lokalnym programie SharePoint. Skontaktuj się z  [zespołem pomocy technicznej lokalnego programu SharePoint](https://support.office.com/), aby dodać użytkowników na platformie lokalnego programu SharePoint. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka lokalnego programu SharePoint na panelu dostępu powinno nastąpić automatyczne zalogowanie do lokalnego programu SharePoint, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
