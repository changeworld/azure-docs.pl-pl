---
title: 'Samouczek: Integracji Azure Active Directory z Picturepark | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.openlocfilehash: cd2caca48992d5fa3bec64004047ecd0fc2b2bfa
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35941218"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Samouczek: Integracji Azure Active Directory z Picturepark

Z tego samouczka dowiesz się integrowanie Picturepark z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Picturepark zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Picturepark
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Picturepark (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Picturepark, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Picturepark logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Picturepark z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-picturepark-from-the-gallery"></a>Dodawanie Picturepark z galerii
Aby skonfigurować integrację usługi Azure AD Picturepark, należy dodać Picturepark z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Picturepark z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Picturepark**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/tutorial_picturepark_search.png)

5. W panelu wyników wybierz **Picturepark**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Picturepark w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Picturepark jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Picturepark musi się.

W Picturepark, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Picturepark, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Picturepark](#creating-a-picturepark-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Picturepark połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Picturepark.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Picturepark, wykonaj następujące czynności:**

1. W portalu Azure na **Picturepark** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/picturepark-tutorial/tutorial_picturepark_samlbase.png)

3. Na **Picturepark domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/picturepark-tutorial/tutorial_picturepark_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.picturepark.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Picturepark](https://picturepark.com/about/contact/) uzyskać te wartości. 
 
4. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartości certyfikatu.

    ![Konfigurowanie rejestracji jednokrotnej](./media/picturepark-tutorial/tutorial_picturepark_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/picturepark-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Picturepark** , kliknij przycisk **skonfigurować Picturepark** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/picturepark-tutorial/tutorial_picturepark_configure.png) 

7. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy Picturepark jako administrator.

8. Na pasku narzędzi u góry kliknij **narzędzia administracyjne**, a następnie kliknij przycisk **konsoli zarządzania**.
   
    ![Konsola zarządzania](./media/picturepark-tutorial/ic795062.png "Konsola zarządzania")

9. Kliknij przycisk **uwierzytelniania**, a następnie kliknij przycisk **dostawców tożsamości**.
   
    ![Uwierzytelnianie](./media/picturepark-tutorial/ic795063.png "uwierzytelniania")

10. W **konfiguracji dostawcy tożsamości** sekcji, wykonaj następujące czynności:
   
    ![Konfiguracja dostawcy tożsamości](./media/picturepark-tutorial/ic795064.png "konfiguracji dostawcy tożsamości")
   
    a. Kliknij pozycję **Add** (Dodaj).
  
    b. Wpisz nazwę dla danej konfiguracji.
   
    c. Wybierz **Ustaw jako domyślny**.
   
    d. W **URI wystawcy** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.
   
    e. W **zaufanego wystawcy miniatury** pole tekstowe, Wklej wartość **odcisk palca** , które zostały skopiowane z **certyfikat podpisywania SAML** sekcji. 

11. Kliknij przycisk **JoinDefaultUsersGroup**.

12. Aby ustawić **Emailaddress** atrybutu w **oświadczeń** pole tekstowe, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` i kliknij przycisk **zapisać**.

      ![Konfiguracja](./media/picturepark-tutorial/ic795065.png "konfiguracji")

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-picturepark-test-user"></a>Tworzenie użytkownika testowego Picturepark

Aby włączyć użytkowników usługi Azure AD zalogować się do Picturepark, musi być przygotowana do Picturepark. W przypadku Picturepark Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **Picturepark** dzierżawy.

2. Na pasku narzędzi u góry kliknij **narzędzia administracyjne**, a następnie kliknij przycisk **użytkowników**.
   
    ![Użytkownicy](./media/picturepark-tutorial/ic795067.png "użytkowników")

3. W **Przegląd użytkowników** , kliknij pozycję **nowy**.
   
    ![Zarządzanie użytkownikami](./media/picturepark-tutorial/ic795068.png "Zarządzanie użytkownikami")

4. Na **Tworzenie użytkownika** okna dialogowego, wykonaj następujące kroki prawidłowego Azure Active Directory użytkownika mają do przepisu:
   
    ![Utwórz użytkownika](./media/picturepark-tutorial/ic795069.png "Tworzenie użytkownika")
   
    a. W **adres E-mail** pole tekstowe, typ **adres e-mail** użytkownika **BrittaSimon@contoso.com**.  
   
    b. W **hasło** i **Potwierdź hasło** pól tekstowych, typ **hasło** z BrittaSimon. 
   
    c. W **imię** pole tekstowe, typ **imię** użytkownika **Britta**. 
   
    d. W **nazwisko** pole tekstowe, typ **nazwisko** użytkownika **Simona**.
   
    e. W **firmy** pole tekstowe, typ **nazwa firmy** użytkownika. 
   
    f. W **kraju** pole tekstowe, wybierz opcję **kraju** użytkownika.
  
    g. W **ZIP** pole tekstowe, typ **kod POCZTOWY** miasta.
   
    h. W **miasta** pole tekstowe, typ **nazwę miejscowości** użytkownika.

    i. Wybierz **języka**.
   
    j. Kliknij przycisk **Utwórz**.

>[!NOTE]
>Możesz użyć innych Picturepark użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Picturepark do udostępnienia konta użytkownika usługi Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Picturepark.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Picturepark, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Picturepark**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/picturepark-tutorial/tutorial_picturepark_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Picturepark w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Picturepark. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
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

