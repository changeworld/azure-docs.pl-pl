---
title: 'Samouczek: Integracja usługi Azure Active Directory z lokalnego programu SharePoint | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i programu SharePoint w środowisku lokalnym.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2018
ms.author: jeedes
ms.openlocfilehash: 31f4c64296c180ac441f2fb691514a4b9fb35a57
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51687201"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Samouczek: Integracja usługi Azure Active Directory z lokalnego programu SharePoint

W tym samouczku dowiesz się, jak zintegrować lokalny program SharePoint za pomocą usługi Azure Active Directory (Azure AD).

Integrowanie lokalnych programu SharePoint z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do lokalnego programu SharePoint.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do lokalnego programu SharePoint (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą programu SharePoint w środowisku lokalnym, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Lokalny program SharePoint logowanie jednokrotne włączone subskrypcja pojedyncza

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie lokalnych programu SharePoint z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Dodawanie lokalnych programu SharePoint z galerii

Aby skonfigurować integrację z lokalnego programu SharePoint w usłudze Azure AD, należy dodać lokalny program SharePoint z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać lokalny program SharePoint z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **lokalny program SharePoint**, wybierz opcję **lokalny program SharePoint** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Lokalny program SharePoint na liście wyników](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą lokalnego programu SharePoint w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w lokalnych programu SharePoint do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w programie SharePoint lokalnych powinien być określony.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą lokalnego programu SharePoint, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Udzielić dostępu użytkownika testowego w środowisku lokalnym program SharePoint](#grant-access-to-sharePoint-on-premises-test-user)**  — aby mają odpowiednika pozycji Britta simon w lokalnych programu SharePoint, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i skonfigurować logowanie jednokrotne w aplikacji programu SharePoint w środowisku lokalnym.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą programu SharePoint w środowisku lokalnym, wykonaj następujące czynności:**

1. W witrynie Azure portal na **lokalny program SharePoint** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

3. Na **lokalny program SharePoint domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Lokalny program SharePoint domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. W **identyfikator** pole tekstowe, wpisz adres URL: `urn:sharepoint:federation`

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<YourSharePointServerURL>/_trust/default.aspx`

4. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

    > [!Note]
    > . Zanotuj ścieżkę pliku, do którego został pobrany plik certyfikatu na potrzeby jej użyć później WE skrypt programu PowerShell w konfiguracji.

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media\sharepoint-on-premises-tutorial/tutorial_general_400.png)

6. Na **lokalny program SharePoint konfiguracji** kliknij **Konfiguruj program SharePoint w środowisku lokalnym** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **krótki przewodnik po sekcji.** Dla **pojedynczy znak na adres URL usługi**, użyj wartości następującego wzorca: `https://login.microsoftonline.com/_my_directory_id_/wsfed` 

    > [!Note]
    > _my_directory_id_ jest identyfikator dzierżawy subskrypcji usługi Azure Ad.

    ![SharePoint — lokalna konfiguracja](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > Lokalny program SharePoint, aplikacja używa języka SAML 1.1 tokenu, dzięki czemu usługa Azure AD oczekuje żądanie usługi WS-Fed z serwera programu SharePoint, a po uwierzytelnieniu wystawia SAML 1.1. token.

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny programu SharePoint w środowisku lokalnym firmy.

8. **Konfigurowanie nowego zaufanego dostawcy tożsamości w programie SharePoint Server 2016**

    Zaloguj się do serwera programu SharePoint Server 2016, a następnie otwórz powłokę zarządzania programu SharePoint 2016. Wypełnij wartości (wartości identyfikatora z lokalnej domeny i adresów URL sekcja dotycząca programu SharePoint w witrynie Azure portal), $realm $wsfedurl (pojedynczego logowania jednokrotnego usługi adresu URL) i $filepath (ścieżka pliku, do którego został pobrany plik certyfikatu) w witrynie Azure portal i wykonywania następujące polecenia, aby skonfigurować nowe zaufanego dostawcy tożsamości.

    > [!TIP]
    > Jeśli dopiero zaczynasz pracę z przy użyciu programu PowerShell, czy chcesz dowiedzieć się więcej na temat sposobu działania programu PowerShell, zobacz [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps). 

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
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Następnie wykonaj następujące kroki, aby włączyć zaufanego dostawcy tożsamości dla aplikacji:

    a. W witrynie Administracja centralna, przejdź do **Zarządzanie aplikacją sieci Web** i wybierz aplikację sieci web, które chcesz zabezpieczyć za pomocą usługi Azure AD.

    b. Na wstążce kliknij **dostawców uwierzytelniania** i wybierz strefę, dla której chcesz użyć.

    c. Wybierz **dostawcy tożsamości zaufanych** i wybierz dostawcę identyfikowanie niedawno zarejestrowane o nazwie *AzureAD*.

    d. Ustawienie adresu URL strony logowania, wybierz **niestandardowy strona logowania** i podaj wartość "/_trust/".

    e. Kliknij przycisk **OK**.

    ![Konfigurowanie dostawcy uwierzytelniania](./media\sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Niektóre użytkownicy zewnętrzni nie będzie w stanie używać pojedynczego integracja logowania jednokrotnego, zgodnie z ich nazwy UPN będzie zniekształcone wartość coś w rodzaju `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Wkrótce będziemy umożliwi klientom konfiguracji aplikacji w sposób obsługi nazwy UPN w zależności od typu użytkownika. Po tym wszyscy użytkownicy-goście powinien móc za pomocą logowania jednokrotnego bezproblemowo jako pracowników swojej organizacji.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media\sharepoint-on-premises-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media\sharepoint-on-premises-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media\sharepoint-on-premises-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media\sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="grant-access-to-sharepoint-on-premises-test-user"></a>Udzielanie dostępu do użytkownika testowego w środowisku lokalnym program SharePoint

Użytkownicy, którzy będą rejestrować w usłudze Azure AD i dostęp do programu SharePoint musi otrzymać dostęp do aplikacji wykonaj następujące kroki, aby ustawić uprawnienia dostępu do aplikacji sieci web.

1. W witrynie Administracja centralna, kliknij przycisk **Zarządzanie aplikacjami**.

2. Na **Zarządzanie aplikacjami** stronie **aplikacji sieci Web** kliknij **Zarządzaj aplikacjami sieci web**.

3. Kliknij aplikację sieci web odpowiednie, a następnie kliknij przycisk **zasady użytkownika**.

4. W zasadach dla aplikacji sieci Web, kliknij przycisk **Add Users**.

    ![Wyszukiwanie według ich oświadczenia nazwy użytkownika](./media\sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. W **Add Users** okno dialogowe, kliknij przycisk odpowiedniej strefy **stref**, a następnie kliknij przycisk **dalej**.

6. W **zasad dla aplikacji sieci Web** dialogowym **Wybieranie użytkowników** , kliknij przycisk **Przeglądaj** ikony.

7. W **znaleźć** polu tekstowym wpisz **główna użytkownika** wartość, dla którego skonfigurowano lokalną aplikację programu SharePoint w usłudze Azure AD i kliknij przycisk **wyszukiwania**. </br>Przykład: *brittasimon@contoso.com*.

8. W pozycji AzureAD w widoku listy, wybierz właściwości name, a następnie kliknij przycisk **Dodaj** kliknięcie **OK** aby zamknąć okno dialogowe.

9. W obszarze uprawnienia kliknij **Pełna kontrola**.

    ![Udzielanie uprawnień Pełna kontrola do oświadczenia użytkownika](./media\sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. Kliknij przycisk **Zakończ**, a następnie kliknij przycisk **OK**.

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Konfigurowanie jednego zaufanego dostawcy tożsamości dla wielu aplikacji sieci web

Konfiguracja działa w przypadku aplikacji sieci web jednym, ale wymaga dodatkowej konfiguracji, jeśli zamierzasz używać tego samego zaufanego dostawcy tożsamości dla wielu aplikacji sieci web. Załóżmy na przykład, firma Microsoft ma rozszerzone aplikacji sieci web do używania adresu URL `https://portal.contoso.local` i teraz chcemy wyłączyć uwierzytelnianie użytkowników umożliwiające `https://sales.contoso.local` także. Aby to zrobić, należy zaktualizować dostawcy tożsamości, uznaje parametr WReply i zaktualizować rejestracji aplikacji w usłudze Azure AD, aby dodać adres URL odpowiedzi.

1. W witrynie Azure Portal otwórz katalog usługi Azure AD. Kliknij przycisk **rejestracje aplikacji**, następnie kliknij przycisk **Wyświetl wszystkie aplikacje**. Kliknij aplikację, który został utworzony wcześniej (Integracja z programem SharePoint SAML).

2. Kliknij przycisk **ustawienia**.

3. W bloku ustawienia kliknij **adresy URL odpowiedzi**. 

4. Dodaj adres URL aplikacji sieci web dodatkowe przy użyciu `/_trust/default.aspx` dołączane do adresu URL (takie jak `https://sales.contoso.local/_trust/default.aspx`) i kliknij przycisk **Zapisz**.

5. Na serwerze programu SharePoint otwórz **powłoki zarządzania programu SharePoint 2016** i wykonaj następujące polecenia, używając nazwy wystawcy tokenów zaufanej tożsamości, której użyto poprzednio.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. W witrynie Administracja centralna przejdź do aplikacji sieci web i umożliwić istniejących zaufanego dostawcy tożsamości. Pamiętaj, aby również skonfigurować adres URL strony logowania jako niestandardowa strona logowania `/_trust/`.

7. W witrynie Administracja centralna, kliknij żądaną aplikację sieci web, a następnie wybierz **zasady użytkownika**. Jak przedstawiono wcześniej w tym artykule, należy dodać użytkownika z odpowiednimi uprawnieniami.

### <a name="fixing-people-picker"></a>Naprawianie selektora osób

Użytkownicy mogą logować się do programu SharePoint 2016 przy użyciu tożsamości z usługi Azure AD, ale nadal są możliwości poprawy środowiska użytkownika. Na przykład wyszukać użytkownika przedstawia wiele wyników wyszukiwania w Selektor osób. Brak wyniku wyszukiwania dla każdego z typów oświadczeń 3, które zostały utworzone w Mapowanie oświadczeń. Aby wybrać użytkownika, za pomocą selektora osób, należy dokładnie wpisz swoją nazwę użytkownika i wybierz **nazwa** oświadczenia wynik.

![Wyniki wyszukiwania oświadczeń](./media\sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Nie ma możliwości weryfikacji na podstawie wartości wyszukiwania, co może prowadzić do błędów pisowni lub użytkowników, wybierając przypadkowo nieprawidłowa oświadczenia typu, takie jak przypisać **nazwisko** oświadczenia. Może to uniemożliwić użytkownikom dostęp do zasobów.

Aby pomóc w tym scenariuszu, jest typu open-source rozwiązanie o nazwie [AzureCP](https://yvand.github.io/AzureCP/) zapewniający dostawcy oświadczenia niestandardowe programu SharePoint 2016. Azure AD Graph zostanie użyty do rozwiązania, co użytkownicy wprowadzać i wykonać sprawdzanie poprawności. Dowiedz się więcej o [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do lokalnego programu SharePoint.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon do lokalnego programu SharePoint, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **lokalny program SharePoint**.

    ![Łącza programu SharePoint, na liście aplikacji](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka lokalnych programu SharePoint w panelu dostępu, należy uzyskać automatycznie zalogowanych do lokalnych aplikacji programu SharePoint.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Używanie programu Azure AD do uwierzytelniania serwera programu SharePoint](https://docs.microsoft.com/office365/enterprise/using-azure-ad-for-sharepoint-server-authentication)

<!--Image references-->

[1]: ./media\sharepoint-on-premises-tutorial/tutorial_general_01.png
[2]: ./media\sharepoint-on-premises-tutorial/tutorial_general_02.png
[3]: ./media\sharepoint-on-premises-tutorial/tutorial_general_03.png
[4]: ./media\sharepoint-on-premises-tutorial/tutorial_general_04.png

[100]: ./media\sharepoint-on-premises-tutorial/tutorial_general_100.png

[200]: ./media\sharepoint-on-premises-tutorial/tutorial_general_200.png
[201]: ./media\sharepoint-on-premises-tutorial/tutorial_general_201.png
[202]: ./media\sharepoint-on-premises-tutorial/tutorial_general_202.png
[203]: ./media\sharepoint-on-premises-tutorial/tutorial_general_203.png
