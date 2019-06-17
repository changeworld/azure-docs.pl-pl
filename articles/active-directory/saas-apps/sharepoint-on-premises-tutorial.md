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
ms.openlocfilehash: 21c10e142802914bcf3292ef7f25066675e6cdd1
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67143922"
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

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
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
    > Jeśli element nie powinien być dostępny, jego można również otworzyć za pomocą stałe **wszystkich usług** łącze w górnej części panelu nawigacyjnym po lewej stronie. W poniższym omówieniu **usługi Azure Active Directory** łącze to znajduje się w **tożsamości** sekcji lub mogą być wyszukiwane przy użyciu pola tekstowego filtru.

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
4. **[Utwórz grupę zabezpieczeń usługi Azure AD w witrynie Azure portal](#create-an-azure-ad-security-group-in-the-azure-portal)**  — Aby włączyć nową grupę zabezpieczeń w usłudze Azure AD na potrzeby logowania jednokrotnego.
5. **[Udzielanie dostępu do programu SharePoint w lokalnej grupy zabezpieczeń](#grant-access-to-sharepoint-on-premises-security-group)**  — udzielanie dostępu do określonej grupy do usługi Azure AD.
6. **[Przypisz grupy zabezpieczeń usługi Azure AD, w witrynie Azure portal](#assign-the-azure-ad-security-group-in-the-azure-portal)**  — przypisywanie określonej grupy do usługi Azure AD do uwierzytelniania.
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

1. W oknie przeglądarki innej witryny sieci web Zaloguj się w witrynie programu SharePoint w środowisku lokalnym firmy jako administrator.

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
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Utwórz grupę zabezpieczeń usługi Azure AD w witrynie Azure portal

1. Kliknij pozycję **usługi Azure Active Directory > wszystkie grupy**.

    ![Utwórz grupę zabezpieczeń usługi Azure AD](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Kliknij przycisk **nową grupę**:

    ![Utwórz grupę zabezpieczeń usługi Azure AD](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Wypełnij **typ grupy**, **Nazwa grupy**, **opis grupy**, **Typ członkostwa**. Kliknij strzałkę, aby Wybieranie elementów członkowskich, a następnie wyszukiwania lub kliknij element członkowski, który chcesz dodać do grupy. Kliknij pozycję **wybierz** można dodać wybranych członków, następnie kliknij **Utwórz**.

    ![Utwórz grupę zabezpieczeń usługi Azure AD](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Aby można było przypisać grupy zabezpieczeń usługi Active Directory platformy Azure do programu SharePoint w środowisku lokalnym, będą niezbędne do zainstalowania i skonfigurowania [AzureCP](https://yvand.github.io/AzureCP/) w lokalnym programem SharePoint farmy lub tworzenie i konfigurowanie alternatywnych oświadczenia niestandardowe Dostawca dla programu SharePoint.  Sekcja więcej informacji na końcu dokumentu do tworzenia własnego dostawcy oświadczenia niestandardowe, jeśli nie używasz AzureCP.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Udzielanie dostępu do programu SharePoint w lokalnej grupy zabezpieczeń

**Konfigurowanie grup zabezpieczeń i uprawnień na rejestrowanie aplikacji**

1. W witrynie Azure portal wybierz **usługi Azure Active Directory**, a następnie wybierz **rejestracje aplikacji**.

    ![Blok Aplikacje dla przedsiębiorstw](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. W polu wyszukiwania wpisz i wybierz **lokalny program SharePoint**.

    ![Lokalny program SharePoint na liście wyników](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Kliknij pozycję **manifestu**.

    ![Manifest — opcja](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Modyfikowanie `groupMembershipClaims`: `NULL`, `groupMembershipClaims`: `SecurityGroup`. Następnie kliknij przycisk przy zapisywaniu

    ![Edytuj Manifest](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Kliknij pozycję **ustawienia**, następnie kliknij pozycję **wymagane uprawnienia**.

    ![Wymagane uprawnienia](./media/sharepoint-on-premises-tutorial/settings.png)

6. Kliknij pozycję **Dodaj** i następnie **wybierz interfejs API**.

    ![Dostęp do interfejsu API](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Dodaj oba **Windows Azure Active Directory** i **interfejsu API Microsoft Graph**, ale tylko wtedy można wybrać jeden z nich jednocześnie.

    ![Wybierz interfejs API](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Wybierz pakiet Windows Azure Active Directory, sprawdź Czytaj dane katalogu, a następnie kliknij pozycję Wybierz. Przejdź wstecz i dodać program Microsoft Graph wybierz Czytaj dane katalogu, a także.  Kliknij Wybierz i kliknij pozycję gotowe.

    ![Włącz dostęp](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Teraz, w obszarze wymagane ustawienia, kliknij pozycję **udzielić uprawnień** a następnie kliknij przycisk Tak, do udzielania uprawnień.

    ![Udziel uprawnień](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Sprawdź w obszarze powiadomień, aby określić, jeśli pomyślnie zostały przyznane uprawnienia.  Jeśli nie są one AzureCP nie będzie działać prawidłowo, i nie będzie możliwe do skonfigurowania programu SharePoint przy użyciu grup zabezpieczeń usługi Azure Active Directory.

10. Konfigurowanie AzureCP na farmie programu SharePoint w środowisku lokalnym lub rozwiązanie alternatywne, niestandardowe oświadczenia dostawcy.  W tym przykładzie używamy AzureCP.

    > [!NOTE]
    > Należy pamiętać, że AzureCP nie jest produktem firmy Microsoft lub obsługiwane przez pomoc techniczną firmy Microsoft. Pobieranie, instalowanie i konfigurowanie AzureCP w farmie programu SharePoint w środowisku lokalnym na https://yvand.github.io/AzureCP/ 

11. **Udzielić dostępu do usługi Azure Security grupy usługi Active Directory w programie SharePoint w lokalnej** :-grupy musi otrzymać dostęp do aplikacji w lokalnych programu SharePoint.  Wykonaj następujące kroki, aby ustawić uprawnienia dostępu do aplikacji sieci web.

12. W witrynie Administracja centralna kliknij pozycję Zarządzanie aplikacjami, zarządzanie aplikacjami sieci web, a następnie wybierz aplikację sieci web do aktywowania wstążki, a następnie kliknij przycisk w zasadach użytkownika.

    ![Administracja centralna](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. W ramach zasad dla aplikacji sieci Web wybierz polecenie Dodaj użytkowników, a następnie wybierz strefę, kliknij przycisk Dalej.  Kliknij w książce adresowej.

    ![Zasady dla aplikacji sieci Web](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Następnie wyszukaj i Dodaj grupy zabezpieczeń Active Directory platformy Azure i kliknij przycisk OK.

    ![Dodawanie grupy zabezpieczeń](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Wybierz uprawnienia, a następnie kliknij przycisk Zakończ.

    ![Dodawanie grupy zabezpieczeń](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Zobacz, w ramach zasad dla aplikacji sieci Web, grupy usługi Azure Active Directory zostanie dodany.  Oświadczenia grupy pokazuje usługi Azure Active Directory zabezpieczeń grupy identyfikator obiektu dla nazwy użytkownika.

    ![Dodawanie grupy zabezpieczeń](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Przejdź do zbioru witryn programu SharePoint, a także dodać grupę, w miejscu. Kliknij pozycję Ustawienia witryny, a następnie kliknij uprawnień do witryny i przyznawanie uprawnień.  Wyszukaj oświadczenie roli grupy, przypisać poziom uprawnień, a następnie kliknij przycisk Udostępnij.

    ![Dodawanie grupy zabezpieczeń](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurowanie jednego zaufanego dostawcy tożsamości dla wielu aplikacji internetowych

Konfiguracja działa w przypadku jednej aplikacji internetowej, ale wymaga dodatkowej konfiguracji, jeśli zamierzasz używać tego samego zaufanego dostawcy tożsamości dla wielu aplikacji internetowych. Załóżmy na przykład, że rozszerzyliśmy aplikację internetową w celu używania adresu URL `https://portal.contoso.local` i teraz chcemy również uwierzytelniać użytkowników w `https://sales.contoso.local`. Aby to zrobić, musimy zaktualizować dostawcę tożsamości, aby uwzględnić parametr WReply i zaktualizować rejestrację aplikacji w usłudze Azure AD w celu dodania adresu URL odpowiedzi.

1. W witrynie Azure portal Otwórz katalog usługi Azure AD. Kliknij pozycję **Rejestracje aplikacji**, następnie kliknij pozycję **Wyświetl wszystkie aplikacje**. Kliknij aplikację, która została utworzony wcześniej (Integracja programu SharePoint z protokołem SAML).

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

Użytkownicy mogą teraz logować się do programu SharePoint 2016 przy użyciu tożsamości z usługi Azure AD, ale nadal są możliwości poprawy środowiska użytkownika. Na przykład wyszukiwanie użytkownika przedstawia wiele wyników wyszukiwania w selektorze osób. Istnieją wyniki wyszukiwania dla każdego z trzech typów oświadczeń, które zostały utworzone w mapowaniu oświadczeń. Aby wybrać użytkownika za pomocą selektora osób, należy dokładnie wpisać jego nazwę użytkownika i wybrać wynik oświadczenia **name**.

![Wyniki wyszukiwania oświadczeń](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Nie ma weryfikacji wyszukiwanych wartości, co może prowadzić do błędów pisowni lub przypadkowego wybierania przez użytkowników nieprawidłowego typu oświadczenia, takiego jak oświadczenie **SurName**. Może to uniemożliwić użytkownikom pomyślny dostęp do zasobów.

Aby pomóc w tym scenariuszu, istnieje rozwiązanie typu open-source o nazwie [AzureCP](https://yvand.github.io/AzureCP/), które zapewnia dostawcę oświadczeń niestandardowych dla programu SharePoint 2016. Będzie ono korzystało z funkcji Azure AD Graph do rozpoznawania wartości wprowadzanych przez użytkowników i wykonywania weryfikacji. Dowiedz się więcej o rozwiązaniu [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Przypisz grupy zabezpieczeń usługi Azure AD, w witrynie Azure portal

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **Lokalny program SharePoint**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz **Lokalny program SharePoint**.

    ![Link lokalnego programu SharePoint na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. Wyszukaj grupy zabezpieczeń, które chcesz użyć, a następnie kliknij grupę, aby dodać go do sekcji wybierz elementy członkowskie. Kliknij przycisk **wybierz**, następnie kliknij przycisk **przypisać**.

    ![Wyszukaj grupy zabezpieczeń](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Sprawdź powiadomienia na pasku menu, aby otrzymywać powiadomienia, że grupa została pomyślnie przypisana do aplikacji przedsiębiorstwa w witrynie Azure portal.

### <a name="create-sharepoint-on-premises-test-user"></a>Tworzenie użytkownika testowego lokalnego programu SharePoint

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w lokalnym programie SharePoint. Skontaktuj się z  [zespołem pomocy technicznej lokalnego programu SharePoint](https://support.office.com/), aby dodać użytkowników na platformie lokalnego programu SharePoint. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka lokalnego programu SharePoint na panelu dostępu powinno nastąpić automatyczne zalogowanie do lokalnego programu SharePoint, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
