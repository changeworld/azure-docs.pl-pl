---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Sansan | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Sansan.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 6bd84e42bf46885a9dced363724538cbd4d2066a
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815008"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Sansan

W tym samouczku dowiesz się, jak zintegrować Sansan w usłudze Azure Active Directory (Azure AD).

Integrowanie Sansan z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Sansan
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Sansan (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Sansan, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Sansan logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Sansan z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-sansan-from-the-gallery"></a>Dodawanie Sansan z galerii
Aby skonfigurować integrację Sansan w usłudze Azure AD, należy dodać Sansan z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Sansan z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Sansan**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sansan-tutorial/tutorial_sansan_search.png)

5. W panelu wyników wybierz **Sansan**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sansan-tutorial/tutorial_sansan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Sansan w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Sansan do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Sansan musi można ustanowić.

W Sansan, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Sansan, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Sansan](#creating-a-sansan-test-user)**  — aby odpowiednikiem Britta Simon w Sansan połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Sansan.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Sansan, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Sansan** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/sansan-tutorial/tutorial_sansan_samlbase.png)

3. Na **Sansan domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/sansan-tutorial/tutorial_sansan_url.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następujących wzorców: 
    
    | Środowisko | Adres URL |
    |:--- |:--- |
    | Komputer w sieci web |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | Natywnych aplikacji mobilnych |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Ustawienia w przeglądarce dla urządzeń przenośnych |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywisty adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Sansan](https://www.sansan.com/form/contact) do uzyskania tych wartości. 
     
4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/sansan-tutorial/tutorial_sansan_certificate.png) 

5. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/sansan-tutorial/tutorial_general_400.png)

6. Aplikacja Sansan oczekuje wielu **identyfikatory** i **adresy URL odpowiedzi** do obsługi wielu środowisk (komputer w sieci web, aplikacji mobilnych z macierzystego, ustawienia przeglądarce dla urządzeń przenośnych), które można skonfigurować przy użyciu programu PowerShell skrypt. Szczegółowe instrukcje zostały podane zostały wyjaśnione poniżej.

7. Aby skonfigurować wiele **identyfikatory** i **adresy URL odpowiedzi** Sansan aplikacji za pomocą skryptu programu PowerShell, należy wykonać następujące kroki:

    ![Konfigurowanie logowania jednokrotnego obj](./media/sansan-tutorial/tutorial_sansan_objid.png)  

    a. Przejdź do **właściwości** strony **Sansan** aplikacji i skopiuj **obiektu o identyfikatorze** przy użyciu **kopiowania** przycisk i wklej go w Notatniku.

    b. **Obiektu o identyfikatorze**, które zostały skopiowane z witryny Azure portal będzie służyć jako **ServicePrincipalObjectId** skryptów programu PowerShell wykorzystywanych w dalszej części tego samouczka. 

    c. Teraz Otwórz wiersz polecenia programu Windows PowerShell.
    
    >[!NOTE] 
    > Musisz zainstalować moduł usługi Azure AD (Użyj polecenia `Install-Module -Name AzureAD`). Jeśli zostanie wyświetlony monit, aby zainstalować moduł NuGet lub nowego modułu programu PowerShell usługi Azure Active Directory w wersji 2, wpisz Y, a następnie naciśnij klawisz ENTER.

    d. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta użytkownika administratora globalnego.

    e. Aby zaktualizować wiele adresów URL do aplikacji, użyj następującego skryptu:

    ```poweshell
     Param(
    [Parameter(Mandatory=$true)][guid]$ServicePrincipalObjectId,
    [Parameter(Mandatory=$false)][string[]]$ReplyUrls,
    [Parameter(Mandatory=$false)][string[]]$IdentifierUrls
    )

    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId

    if($ReplyUrls.Length)
    {
    echo "Updating Reply urls"
    Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls $ReplyUrls
    echo "updated"
    }
    if($IdentifierUrls.Length)
    {
    echo "Updating Identifier urls"
    $applications = Get-AzureADApplication -SearchString $servicePrincipal.AppDisplayName 
    echo "Found Applications =" $applications.Length
    $i = 0;
    do
    {  
    $application = $applications[$i];
    if($application.AppId -eq $servicePrincipal.AppId){
    Set-AzureADApplication -ObjectId $application.ObjectId -IdentifierUris $IdentifierUrls
    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId
    echo "Updated"
    return;
    }
    $i++;
    }while($i -lt $applications.Length);
    echo "Not able to find the matched application with this service principal"
    }
    ```

8. Po pomyślnym ukończeniu skryptu programu PowerShell, wynik skryptu będzie tak jak pokazano poniżej i zaktualizowani wartości adresu URL, ale nie uzyskać odzwierciedlone w witrynie Azure portal. 

    ![Konfigurowanie logowania jednokrotnego skryptu](./media/sansan-tutorial/tutorial_sansan_powershell.png)


9. Na **konfiguracji Sansan** , kliknij przycisk **skonfigurować Sansan** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/sansan-tutorial/tutorial_sansan_configure.png) 

10. Aby skonfigurować logowanie jednokrotne na **Sansan** stronie, musisz wysłać pobrany **certyfikatu**, **adres URL wylogowania**, **identyfikator jednostki SAML**, i **SAML pojedynczego logowania jednokrotnego usługi adresu URL** do [zespołem pomocy technicznej Sansan](https://www.sansan.com/form/contact). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

>[!NOTE]
>Ustawienia przeglądarki komputera również działać dla aplikacji mobilnych i przeglądarce dla urządzeń przenośnych oraz komputer w sieci web. 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sansan-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sansan-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sansan-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sansan-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-sansan-test-user"></a>Tworzenie użytkownika testowego Sansan

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Sansan. Aplikacja Sansan musi użytkownikowi można udostępnić w aplikacji przed wykonaniem logowania jednokrotnego. 

>[!NOTE]
>Jeśli musisz ręcznie utworzyć użytkownika lub partii użytkowników, musisz skontaktować się z [zespołem pomocy technicznej Sansan](https://www.sansan.com/form/contact). 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Sansan.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Sansan, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Sansan**.

    ![Konfigurowanie logowania jednokrotnego](./media/sansan-tutorial/tutorial_sansan_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Sansan w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Sansan.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sansan-tutorial/tutorial_general_01.png
[2]: ./media/sansan-tutorial/tutorial_general_02.png
[3]: ./media/sansan-tutorial/tutorial_general_03.png
[4]: ./media/sansan-tutorial/tutorial_general_04.png

[100]: ./media/sansan-tutorial/tutorial_general_100.png

[200]: ./media/sansan-tutorial/tutorial_general_200.png
[201]: ./media/sansan-tutorial/tutorial_general_201.png
[202]: ./media/sansan-tutorial/tutorial_general_202.png
[203]: ./media/sansan-tutorial/tutorial_general_203.png

