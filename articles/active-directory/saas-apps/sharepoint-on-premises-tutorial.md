---
title: 'Samouczek: Azure Active Directory integracji z lokalnym SharePoint | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i programu SharePoint.
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
ms.date: 06/14/2018
ms.author: jeedes
ms.openlocfilehash: 02421ace226f42da58eb9864fe0ef2e1ca550391
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319286"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Samouczek: Integracji Azure Active Directory z programu SharePoint

Z tego samouczka dowiesz się sposób integrowania programu SharePoint z usługą Azure Active Directory (Azure AD).

Integrowanie programu SharePoint z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do programu SharePoint.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do programu SharePoint na lokalnym (logowanie jednokrotne) konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z programu SharePoint, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Programu SharePoint lokalnej pojedynczego logowania jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie programu SharePoint z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Dodawanie programu SharePoint z galerii
Aby skonfigurować integrację usługi Azure AD programu SharePoint, należy dodać programu SharePoint z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać programu SharePoint z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **programu SharePoint**, wybierz pozycję **SharePoint lokalnymi** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![SharePoint lokalnie na liście wyników](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z programu SharePoint lokalnie w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w lokalnym programu SharePoint jest dla użytkownika, w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i powiązane użytkownika w programie SharePoint lokalnymi powinien być określony.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z programu SharePoint, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego lokalnymi SharePoint](#create-a-sharePoint-on-premises-test-user)**  — aby odpowiednikiem Simona Britta programu SharePoint w lokalnym programie połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne do aplikacji lokalnych programu SharePoint.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z programu SharePoint, wykonaj następujące czynności:**

1. W portalu Azure na **SharePoint lokalnymi** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

3. Na **SharePoint lokalnej domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![SharePoint lokalnej domeny i adres URL pojedynczego logowania jednokrotnego informacji](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `urn:sharepoint:<YourSharePointServerURL>`

    > [!NOTE]
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [SharePoint zespołem pomocy technicznej klienta lokalnymi](https://support.office.com/) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na komputerze z rozszerzeniem cer. Skopiuj i Wklej Pełna ścieżka pliku metadanych pobranych do Notatnika.

    ![Łącze pobierania certyfikatu](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media\sharepoint-on-premises-tutorial/tutorial_general_400.png)

6. Na **SharePoint lokalnej konfiguracji** kliknij **Konfigurowanie programu SharePoint** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja lokalnego programu SharePoint](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > Aplikacja programu SharePoint używa SAML 1.1 token, więc żądania WS Fed z serwera programu SharePoint i po uwierzytelnieniu oczekuje usługi Azure AD wystawia SAML 1.1. token.

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator witryny programu SharePoint na obszarze firmy.

8. **Konfigurowanie nowego dostawcę zaufanych tożsamości w SharePoint Server 2016**

    Zaloguj się do serwera programu SharePoint Server 2016, a następnie otwórz powłokę zarządzania programu SharePoint 2016. Wypełnij wartości $realm, $wsfedurl i $filepath z portalu Azure, a następnie uruchom następujące polecenia, aby skonfigurować nowy dostawca tożsamości zaufanych.

    > [!TIP]
    > Jesteś nowym użytkownikiem przy użyciu programu PowerShell lub chcesz dowiedzieć się więcej na temat działania programu PowerShell, zobacz [programu PowerShell programu SharePoint](https://docs.microsoft.com/en-us/powershell/sharepoint/overview?view=sharepoint-ps). 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have copied from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Następnie wykonaj następujące kroki, aby włączyć zaufanego dostawcę tożsamości aplikacji:

    a. W witrynie Administracja centralna, przejdź do **Zarządzanie aplikacji sieci Web** i wybierz aplikację sieci web, którą chcesz zabezpieczyć za pomocą usługi Azure AD.

    b. Na wstążce kliknij **dostawców uwierzytelniania** i wybierz strefę, dla której chcesz użyć.

    c. Wybierz **dostawcy tożsamości zaufane** i wybierz dostawcę Identyfikuj został zarejestrowany o nazwie *AzureAD*.

    d. Na ustawienie adresu URL strony logowania, wybierz **niestandardowa strona logowania** i podaj wartość "/_trust/".

    e. Kliknij przycisk **OK**.

    ![Konfigurowanie dostawcy uwierzytelniania](./media\sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media\sharepoint-on-premises-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media\sharepoint-on-premises-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media\sharepoint-on-premises-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media\sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-sharepoint-on-premises-test-user"></a>Tworzenie użytkownika testowego lokalnego programu SharePoint

1. W witrynie Administracja centralna, kliknij przycisk **Zarządzanie aplikacjami**.

2. Na **Zarządzanie aplikacjami** strony w **aplikacji sieci Web** kliknij **zarządzania aplikacjami sieci web**.

3. Kliknij aplikację sieci web odpowiednie, a następnie kliknij przycisk **zasady użytkownika**.

4. W zasadach dla aplikacji sieci Web, kliknij przycisk **Dodaj użytkowników**.

    ![Wyszukiwanie według ich oświadczenia nazwy użytkownika](./media\sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. W **Dodaj użytkowników** okna dialogowego kliknij odpowiedniej strefy **strefy**, a następnie kliknij przycisk **dalej**.

6. W **zasady dla aplikacji sieci Web** okna dialogowego, **Wybieranie użytkowników** kliknij **Przeglądaj** ikony.

7. W **znaleźć** pole tekstowe, wpisz nazwę logowania dla użytkownika w katalogu i kliknij przycisk **wyszukiwania**. </br>Przykład: *demouser@blueskyabove.onmicrosoft.com*.

8. Pod pozycją AzureAD w widoku listy, wybierz właściwość name, a następnie kliknij przycisk **Dodaj** kliknięcie **OK** aby zamknąć okno dialogowe.

9. W polu uprawnienia kliknij **Pełna kontrola**.

    ![Udzielanie uprawnień Pełna kontrola do oświadczenia użytkownika](./media\sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. Kliknij przycisk **Zakończ**, a następnie kliknij przycisk **OK**.

### <a name="fixing-people-picker"></a>Ustalania selektora osób

Użytkownicy mogą teraz zalogować się do programu SharePoint 2016 przy użyciu tożsamości z usługi Azure AD, ale są nadal możliwości poprawy środowiska użytkownika. Na przykład wyszukiwanie użytkownika przedstawia wiele wyników wyszukiwania w selektora osób. Brak wyniku wyszukiwania dla poszczególnych typów oświadczeń 3, które zostały utworzone w mapowaniu oświadczeń. Aby wybrać użytkownika, za pomocą selektora osób, musisz wpisać dokładnie nazwy użytkownika i wybrać **nazwa** oświadczeń wynik.

![Wyniki wyszukiwania oświadczeń](./media\sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Nie istnieje żadne sprawdzanie poprawności wartości wyszukiwania, które mogą prowadzić do błędów pisowni lub typ można przypisać takich jak oświadczenia użytkowników przypadkowo Wybieranie nieprawidłowa **nazwisko** oświadczeń. Może to uniemożliwić użytkownikom dostęp do zasobów.

Aby pomóc w tym scenariuszu, jest open source rozwiązanie o nazwie [AzureCP](https://yvand.github.io/AzureCP/) zapewnia dostawcy oświadczeń niestandardowego programu SharePoint 2016. Azure AD Graph zostanie użyty do rozwiązania, co użytkownicy wprowadzać i wykonywać sprawdzania poprawności. Dowiedz się więcej o [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do programu SharePoint.

![Przypisanie roli użytkownika][200]

**Aby przypisać Simona Britta programu SharePoint, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **SharePoint lokalnymi**.

    ![Łącza programu SharePoint na liście aplikacji](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka lokalnego programu SharePoint, w panelu dostępu, należy pobrać zalogowane automatycznie do aplikacji lokalnych programu SharePoint.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


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

