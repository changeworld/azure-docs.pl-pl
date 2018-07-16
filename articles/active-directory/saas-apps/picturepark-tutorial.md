---
title: 'Samouczek: Integracja usługi Azure Active Directory z Picturepark | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.openlocfilehash: 274e8802d0723b4f9197210c51d41714788652a0
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041878"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Samouczek: Integracja usługi Azure Active Directory z Picturepark

W tym samouczku dowiesz się, jak zintegrować Picturepark w usłudze Azure Active Directory (Azure AD).

Integrowanie Picturepark z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Picturepark
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Picturepark (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Picturepark, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Picturepark logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Picturepark z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-picturepark-from-the-gallery"></a>Dodawanie Picturepark z galerii
Aby skonfigurować integrację Picturepark w usłudze Azure AD, należy dodać Picturepark z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Picturepark z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Picturepark**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/tutorial_picturepark_search.png)

5. W panelu wyników wybierz **Picturepark**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Picturepark w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Picturepark do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Picturepark musi można ustanowić.

W Picturepark, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Picturepark, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Picturepark](#creating-a-picturepark-test-user)**  — aby odpowiednikiem Britta Simon w Picturepark połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Picturepark.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Picturepark, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Picturepark** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/picturepark-tutorial/tutorial_picturepark_samlbase.png)

3. Na **Picturepark domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/picturepark-tutorial/tutorial_picturepark_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.picturepark.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Picturepark](https://picturepark.com/about/contact/) do uzyskania tych wartości. 
 
4. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość certyfikatu.

    ![Konfigurowanie logowania jednokrotnego](./media/picturepark-tutorial/tutorial_picturepark_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/picturepark-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Picturepark** , kliknij przycisk **skonfigurować Picturepark** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/picturepark-tutorial/tutorial_picturepark_configure.png) 

7. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Picturepark, jako administrator.

8. Na pasku narzędzi u góry kliknij **narzędzia administracyjne**, a następnie kliknij przycisk **konsoli zarządzania**.
   
    ![Konsola zarządzania](./media/picturepark-tutorial/ic795062.png "Konsola zarządzania")

9. Kliknij przycisk **uwierzytelniania**, a następnie kliknij przycisk **dostawców tożsamości**.
   
    ![Uwierzytelnianie](./media/picturepark-tutorial/ic795063.png "uwierzytelniania")

10. W **konfiguracji dostawcy tożsamości** sekcji, wykonaj następujące czynności:
   
    ![Konfiguracja dostawcy tożsamości](./media/picturepark-tutorial/ic795064.png "konfiguracji dostawcy tożsamości")
   
    a. Kliknij pozycję **Add** (Dodaj).
  
    b. Wpisz nazwę dla danej konfiguracji.
   
    c. Wybierz **Ustaw jako domyślny**.
   
    d. W **URI wystawcy** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
   
    e. W **zaufanego wystawcy odcisk palca** pola tekstowego, Wklej wartość **odcisk palca** skopiowanej z **certyfikat podpisywania SAML** sekcji. 

11. Kliknij przycisk **JoinDefaultUsersGroup**.

12. Aby ustawić **Emailaddress** atrybutu w **oświadczenia** pole tekstowe, wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` i kliknij przycisk **Zapisz**.

      ![Konfiguracja](./media/picturepark-tutorial/ic795065.png "konfiguracji")

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-picturepark-test-user"></a>Tworzenie użytkownika testowego Picturepark

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Picturepark, musi być obsługiwana w Picturepark. W przypadku Picturepark Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Picturepark** dzierżawy.

2. Na pasku narzędzi u góry kliknij **narzędzia administracyjne**, a następnie kliknij przycisk **użytkowników**.
   
    ![Użytkownicy](./media/picturepark-tutorial/ic795067.png "użytkowników")

3. W **Przegląd użytkowników** kliknij pozycję **New**.
   
    ![Zarządzanie użytkownikami](./media/picturepark-tutorial/ic795068.png "Zarządzanie użytkownikami")

4. Na **Create User** okna dialogowego, wykonaj następujące kroki prawidłowego usługi Azure Active Directory użytkownika ma aprowizację:
   
    ![Tworzenie użytkownika](./media/picturepark-tutorial/ic795069.png "Tworzenie użytkownika")
   
    a. W **adres E-mail** polu tekstowym wpisz **adres e-mail** użytkownika **BrittaSimon@contoso.com**.  
   
    b. W **hasło** i **Potwierdź hasło** pola tekstowe, wpisz **hasło** z BrittaSimon. 
   
    c. W **imię** polu tekstowym wpisz **imię** użytkownika **Britta**. 
   
    d. W **nazwisko** polu tekstowym wpisz **nazwisko** użytkownika **Simon**.
   
    e. W **firmy** polu tekstowym wpisz **nazwa firmy** użytkownika. 
   
    f. W **kraju** pola tekstowego, wybierz opcję **kraju** użytkownika.
  
    g. W **ZIP** polu tekstowym wpisz **kod POCZTOWY** miasta.
   
    h. W **Miasto** polu tekstowym wpisz **nazwę miejscowości** użytkownika.

    i. Wybierz **języka**.
   
    j. Kliknij przycisk **Utwórz**.

>[!NOTE]
>Można użyć jakichkolwiek innych Picturepark użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Picturepark można uaktywniać ich konta usługi Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Picturepark.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Picturepark, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Picturepark**.

    ![Konfigurowanie logowania jednokrotnego](./media/picturepark-tutorial/tutorial_picturepark_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Picturepark w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Picturepark. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/picturepark-tutorial/tutorial_general_01.png
[2]: ./media/picturepark-tutorial/tutorial_general_02.png
[3]: ./media/picturepark-tutorial/tutorial_general_03.png
[4]: ./media/picturepark-tutorial/tutorial_general_04.png

[100]: ./media/picturepark-tutorial/tutorial_general_100.png

[200]: ./media/picturepark-tutorial/tutorial_general_200.png
[201]: ./media/picturepark-tutorial/tutorial_general_201.png
[202]: ./media/picturepark-tutorial/tutorial_general_202.png
[203]: ./media/picturepark-tutorial/tutorial_general_203.png

