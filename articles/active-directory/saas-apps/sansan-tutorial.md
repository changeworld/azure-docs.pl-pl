---
title: 'Samouczek: Integracji Azure Active Directory z Sansan | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Sansan.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: c8b5a84c853a974ede77e716b77f0a5007775ef7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231216"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>Samouczek: Integracji Azure Active Directory z Sansan

Z tego samouczka dowiesz się integrowanie Sansan z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Sansan zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Sansan
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Sansan (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Sansan, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Sansan logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Sansan z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-sansan-from-the-gallery"></a>Dodawanie Sansan z galerii
Aby skonfigurować integrację usługi Azure AD Sansan, należy dodać Sansan z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Sansan z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Sansan**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sansan-tutorial/tutorial_sansan_search.png)

5. W panelu wyników wybierz **Sansan**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sansan-tutorial/tutorial_sansan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Sansan w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Sansan jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Sansan musi się.

W Sansan, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Sansan, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Sansan](#creating-a-sansan-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Sansan połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Sansan.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Sansan, wykonaj następujące czynności:**

1. W portalu Azure na **Sansan** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/sansan-tutorial/tutorial_sansan_samlbase.png)

3. Na **Sansan domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/sansan-tutorial/tutorial_sansan_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL za pomocą następujących wzorców: 
    
    | Środowisko | Adres URL |
    |:--- |:--- |
    | Komputer w sieci web |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | Natywnych aplikacji mobilnej |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Ustawienia przeglądarki przenośnych |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Adres URL logowania rzeczywiste, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Sansan](https://www.sansan.com/form/contact) uzyskać te wartości. 
     
4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/sansan-tutorial/tutorial_sansan_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/sansan-tutorial/tutorial_general_400.png)

6. Aplikacja Sansan oczekuje wielu **identyfikatory** i **adresy URL odpowiedzi** do obsługi wielu środowisk (komputer w sieci web, aplikacji mobilnej macierzystego, ustawienia przeglądarki przenośnych), które można skonfigurować przy użyciu programu PowerShell skrypt. Poniżej opisano szczegółowy opis kroków.

7. Aby skonfigurować wiele **identyfikatory** i **adresy URL odpowiedzi** dla aplikacji Sansan przy użyciu skryptu programu PowerShell, należy wykonać następujące kroki:

    ![Konfigurowanie rejestracji jednokrotnej obj.](./media/sansan-tutorial/tutorial_sansan_objid.png)  

    a. Przejdź do **właściwości** strony **Sansan** aplikacji i skopiuj **obiektu o identyfikatorze** przy użyciu **kopiowania** przycisk i wklej go do Notatnika.

    b. **Obiektu o identyfikatorze**, które zostały skopiowane z portalu Azure będzie używany jako **ServicePrincipalObjectId** w skrypcie programu PowerShell używane w dalszej części tego samouczka. 

    c. Teraz Otwórz wiersz polecenia z podwyższonym poziomem uprawnień programu Windows PowerShell.
    
    >[!NOTE] 
    > Musisz zainstalować moduł AzureAD (za pomocą polecenia `Install-Module -Name AzureAD`). Jeśli zostanie wyświetlony monit, aby zainstalować moduł NuGet lub nowego modułu programu PowerShell usługi Azure Active Directory w wersji 2, typ T, a następnie naciśnij klawisz ENTER.

    d. Uruchom `Connect-AzureAD` i zaloguj się przy użyciu konta administratora globalnego.

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

8. Po pomyślnym ukończeniu skrypt programu PowerShell wynik skryptu będzie podobny do tego, jak pokazano poniżej i wartości adresu URL pobierania aktualizacji, ale nie pobrać odzwierciedlone w portalu Azure. 

    ![Konfigurowanie rejestracji jednokrotnej skryptu](./media/sansan-tutorial/tutorial_sansan_powershell.png)


9. Na **konfiguracji Sansan** , kliknij przycisk **skonfigurować Sansan** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/sansan-tutorial/tutorial_sansan_configure.png) 

10. Skonfigurować logowanie jednokrotne w **Sansan** stronie, musisz wysłać pobrany **certyfikatu**, **Sign-Out adres URL**, **identyfikator jednostki SAML**, i **SAML pojedynczy znak na adres URL usługi** do [Sansan zespołem pomocy technicznej](https://www.sansan.com/form/contact). To ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML one wartość.

>[!NOTE]
>Ustawienia przeglądarki komputera również działać dla aplikacji mobilnej oraz przeglądarkę dla telefonów wraz z Komputerami w sieci web. 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sansan-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sansan-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sansan-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/sansan-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-sansan-test-user"></a>Tworzenie użytkownika testowego Sansan

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Sansan. Aplikacja Sansan musi użytkownika na potrzeby aprowizacji w aplikacji przed wykonaniem logowania jednokrotnego. 

>[!NOTE]
>Jeśli trzeba ręcznie utworzyć użytkownika ani partii użytkowników, należy skontaktować się [zespołem pomocy technicznej Sansan](https://www.sansan.com/form/contact). 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Sansan.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Sansan, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Sansan**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/sansan-tutorial/tutorial_sansan_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Sansan w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Sansan.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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

