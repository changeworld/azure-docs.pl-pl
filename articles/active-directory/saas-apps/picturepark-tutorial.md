---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Picturepark | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b458632ddb6e23ab1ddf642752aa0cbfc11f1ce
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2019
ms.locfileid: "57839072"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Picturepark

W tym samouczku dowiesz się, jak zintegrować Picturepark w usłudze Azure Active Directory (Azure AD).

Integrowanie Picturepark z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Picturepark
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Picturepark (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Picturepark, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Picturepark logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Picturepark z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-picturepark-from-the-gallery"></a>Dodawanie Picturepark z galerii
Aby skonfigurować integrację Picturepark w usłudze Azure AD, należy dodać Picturepark z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Picturepark z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Picturepark**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/tutorial_picturepark_search.png)

1. W panelu wyników wybierz **Picturepark**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Picturepark w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Picturepark do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Picturepark musi można ustanowić.

W Picturepark, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Picturepark, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Picturepark](#creating-a-picturepark-test-user)**  — aby odpowiednikiem Britta Simon w Picturepark połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Picturepark.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Picturepark, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Picturepark** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/picturepark-tutorial/tutorial_picturepark_samlbase.png)

1. Na **Picturepark domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/picturepark-tutorial/tutorial_picturepark_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.picturepark.com`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Picturepark](https://picturepark.com/about/contact/) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość certyfikatu.

    ![Konfigurowanie logowania jednokrotnego](./media/picturepark-tutorial/tutorial_picturepark_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/picturepark-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Picturepark** , kliknij przycisk **skonfigurować Picturepark** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/picturepark-tutorial/tutorial_picturepark_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy Picturepark, jako administrator.

1. Na pasku narzędzi u góry kliknij **narzędzia administracyjne**, a następnie kliknij przycisk **konsoli zarządzania**.
   
    ![Konsola zarządzania](./media/picturepark-tutorial/ic795062.png "Konsola zarządzania")

1. Kliknij przycisk **uwierzytelniania**, a następnie kliknij przycisk **dostawców tożsamości**.
   
    ![Uwierzytelnianie](./media/picturepark-tutorial/ic795063.png "Uwierzytelnianie")

1. W **konfiguracji dostawcy tożsamości** sekcji, wykonaj następujące czynności:
   
    ![Konfiguracja dostawcy tożsamości](./media/picturepark-tutorial/ic795064.png "konfiguracji dostawcy tożsamości")
   
    a. Kliknij pozycję **Add** (Dodaj).
  
    b. Wpisz nazwę dla danej konfiguracji.
   
    c. Wybierz **Ustaw jako domyślny**.
   
    d. W **URI wystawcy** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.
   
    e. W **zaufanego wystawcy odcisk palca** pola tekstowego, Wklej wartość **odcisk palca** skopiowanej z **certyfikat podpisywania SAML** sekcji. 

1. Kliknij przycisk **JoinDefaultUsersGroup**.

1. Aby ustawić **Emailaddress** atrybutu w **oświadczenia** pole tekstowe, wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` i kliknij przycisk **Zapisz**.

      ![Konfiguracja](./media/picturepark-tutorial/ic795065.png "konfiguracji")

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/picturepark-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-picturepark-test-user"></a>Tworzenie użytkownika testowego Picturepark

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Picturepark, musi być obsługiwana w Picturepark. W przypadku Picturepark Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Twojej **Picturepark** dzierżawy.

1. Na pasku narzędzi u góry kliknij **narzędzia administracyjne**, a następnie kliknij przycisk **użytkowników**.
   
    ![Użytkownicy](./media/picturepark-tutorial/ic795067.png "Użytkownicy")

1. W **Przegląd użytkowników** kliknij pozycję **New**.
   
    ![Zarządzanie użytkownikami](./media/picturepark-tutorial/ic795068.png "Zarządzanie użytkownikami")

1. Na **Create User** okna dialogowego, wykonaj następujące kroki prawidłowego usługi Azure Active Directory użytkownika ma aprowizację:
   
    ![Utwórz użytkownika](./media/picturepark-tutorial/ic795069.png "Utwórz użytkownika")
   
    a. W **adres E-mail** polu tekstowym wpisz **adres e-mail** użytkownika **BrittaSimon\@contoso.com**.  
   
    b. W **hasło** i **Potwierdź hasło** pola tekstowe, wpisz **hasło** z BrittaSimon. 
   
    c. W **imię** polu tekstowym wpisz **imię** użytkownika **Britta**. 
   
    d. W **nazwisko** polu tekstowym wpisz **nazwisko** użytkownika **Simon**.
   
    e. W **firmy** polu tekstowym wpisz **nazwa firmy** użytkownika. 
   
    f. W **kraju** pola tekstowego, wybierz opcję **kraju** użytkownika.
  
    g. W **ZIP** polu tekstowym wpisz **kod POCZTOWY** miasta.
   
    h. W **Miasto** polu tekstowym wpisz **nazwę miejscowości** użytkownika.

    i. Wybierz **języka**.
   
    j. Kliknij pozycję **Utwórz**.

>[!NOTE]
>Można użyć jakichkolwiek innych Picturepark użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Picturepark można uaktywniać ich konta usługi Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Picturepark.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Picturepark, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Picturepark**.

    ![Konfigurowanie logowania jednokrotnego](./media/picturepark-tutorial/tutorial_picturepark_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Picturepark w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Picturepark. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

