---
title: 'Samouczek: Integracja usługi Azure Active Directory z Jobscience | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Jobscience.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 92ff93f9836b1ab8157602569c8171f81b976d6f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422675"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Samouczek: Integracja usługi Azure Active Directory z Jobscience

W tym samouczku dowiesz się, jak zintegrować Jobscience w usłudze Azure Active Directory (Azure AD).

Integrowanie Jobscience z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Jobscience
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Jobscience (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Jobscience, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Jobscience logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięcznej wersji próbnej tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Jobscience z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-jobscience-from-the-gallery"></a>Dodawanie Jobscience z galerii
Aby skonfigurować integrację Jobscience w usłudze Azure AD, należy dodać Jobscience z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Jobscience z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Jobscience**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. W panelu wyników wybierz **Jobscience**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Jobscience w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Jobscience do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Jobscience musi można ustanowić.

W Jobscience, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Jobscience, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Jobscience](#creating-a-jobscience-test-user)**  — aby odpowiednikiem Britta Simon w Jobscience połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Jobscience.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Jobscience, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Jobscience** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Na **Jobscience domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywisty adres URL logowania. Uzyskać tę wartość [zespołem pomocy technicznej klienta Jobscience](https://www.jobscience.com/support) lub z logowania jednokrotnego profilu zostanie utworzona, co jest opisane w dalszej części tego samouczka. 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Jobscience** , kliknij przycisk **skonfigurować Jobscience** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Zaloguj się do witryny firmy Jobscience jako administrator.

1. Przejdź do **Instalatora**.
   
   ![Instalator](./media/jobscience-tutorial/IC784358.png "instalacji")

1. W okienku nawigacji po lewej stronie w **administrowanie** kliknij **Zarządzanie domenami** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moja domena** otworzyć **Moja domena** strony. 
   
   ![Moja domena](./media/jobscience-tutorial/ic767825.png "mojej domeny")

1. Aby sprawdzić, czy domenę zostało skonfigurowane prawidłowo, upewnij się, że jest on "**kroku 4 wdrożone dla użytkowników**" i zapoznaj się z "**Moje ustawienia domeny**".

    ![Nie wdrożono użytkownika domeny](./media/jobscience-tutorial/ic784377.png "domeny wdrożenia użytkownika")

1. W witrynie firmy Jobscience kliknij **środki kontroli bezpieczeństwa**, a następnie kliknij przycisk **ustawienia rejestracji jednokrotnej**.
    
    ![Środki kontroli bezpieczeństwa](./media/jobscience-tutorial/ic784364.png "kontroli zabezpieczeń")

1. W **ustawienia rejestracji jednokrotnej** sekcji, wykonaj następujące czynności:
    
    ![Pojedynczy ustawień logowania jednokrotnego](./media/jobscience-tutorial/ic781026.png "pojedynczy ustawień logowania jednokrotnego")
    
    a. Wybierz **SAML włączone**.

    b. Kliknij przycisk **Nowy**.

1. Na **SAML pojedynczego logowania jednokrotnego ustawienie Edytuj** okno dialogowe, należy wykonać następujące czynności:
    
    ![SAML pojedynczego logowania jednokrotnego ustawienia](./media/jobscience-tutorial/ic784365.png "SAML pojedynczego logowania jednokrotnego ustawienia")
    
    a. W **nazwa** polu tekstowym wpisz nazwę dla danej konfiguracji.

    b. W **wystawcy** pola tekstowego, Wklej wartość **identyfikator jednostki SAML**, który skopiowano z witryny Azure portal.

    c. W **identyfikator jednostki** pole tekstowe, wpisz `https://salesforce-jobscience.com`

    d. Kliknij przycisk **Przeglądaj** Aby przekazać certyfikat usługi Azure AD.

    e. Jako **typu tożsamości SAML**, wybierz opcję **potwierdzenie zawiera identyfikator federacji z obiektu użytkownika**.

    f. Jako **lokalizacji tożsamości SAML**, wybierz opcję **tożsamość jest w elemencie NameIdentfier instrukcji podmiotu**.

    g. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    h. W **adres URL wylogowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    i. Kliknij pozycję **Zapisz**.

1. W okienku nawigacji po lewej stronie w **administrowanie** kliknij **Zarządzanie domenami** rozwiń sekcję powiązane, a następnie kliknij przycisk **Moja domena** otworzyć **Moja domena** strony. 
    
    ![Moja domena](./media/jobscience-tutorial/ic767825.png "mojej domeny")

1. Na **Moja domena** stronie **znakowanie strony logowania** kliknij **Edytuj**.
    
    ![Strona logowania znakowania](./media/jobscience-tutorial/ic767826.png "strony logowania, znakowania")

1. Na **znakowanie strony logowania** strony w **usługi uwierzytelniania** sekcji Nazwa Twojej **ustawień logowania jednokrotnego SAML** jest wyświetlana. Zaznacz go, a następnie kliknij przycisk **Zapisz**.
    
    ![Strona logowania znakowania](./media/jobscience-tutorial/ic784366.png "strony logowania, znakowania")

1. Można pobrać PS inicjować logowanie po kliknięciu adres URL logowania na **ustawień logowania jednokrotnego** w **środki kontroli bezpieczeństwa** sekcji menu.

    ![Środki kontroli bezpieczeństwa](./media/jobscience-tutorial/ic784368.png "kontroli zabezpieczeń")
    
    Kliknij profil rejestracji Jednokrotnej, utworzonej w poprzednim kroku. Ta strona zawiera pojedynczy znak w adresie URL w firmie (na przykład [ https://companyname.my.salesforce.com?so=companyid ](https://companyname.my.salesforce.com?so=companyid).    

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-jobscience-test-user"></a>Tworzenie użytkownika testowego Jobscience

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Jobscience, musi być obsługiwana w Jobscience. W przypadku Jobscience Inicjowanie obsługi administracyjnej jest zadanie ręczne.

>[!NOTE]
>Można użyć jakichkolwiek innych Jobscience użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Jobscience do świadczenia usługi Azure Active Directory kont użytkowników.
>  
        
**Aby skonfigurować aprowizację użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Jobscience** witryny firmy jako administrator.

1. Przejdź do instalacji.
   
   ![Instalator](./media/jobscience-tutorial/ic784358.png "instalacji")
1. Przejdź do **Zarządzanie użytkownikami \> użytkowników**.
   
   ![Użytkownicy](./media/jobscience-tutorial/ic784369.png "użytkowników")
1. Kliknij przycisk **nowego użytkownika**.
   
   ![Wszyscy użytkownicy](./media/jobscience-tutorial/ic784370.png "wszystkich użytkowników")
1. Na **Edytowanie użytkownika** okno dialogowe, należy wykonać następujące czynności:
   
   ![Edycja użytkownika](./media/jobscience-tutorial/ic784371.png "Edycja użytkownika")
   
   a. W **imię** polu tekstowym wpisz imię użytkownika, takich jak Britta.
   
   b. W **nazwisko** polu tekstowym wpisz nazwisko użytkownika, takich jak Simon.
   
   c. W **Alias** polu tekstowym wpisz nazwę aliasu użytkownika, takich jak brittas.

   d. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.

   e. W **nazwa_użytkownika** pole tekstowe, wpisz nazwę użytkownika, takie jak Brittasimon@contoso.com.

   f. W **pseudonim** polu tekstowym wpisz nazwę użytkownika, takich jak Simon nick.

   g. Kliknij pozycję **Zapisz**.

    
> [!NOTE]
> Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail, a także następujące łącze, aby potwierdzić swoje konto, zanim stanie się aktywny.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Jobscience.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Jobscience, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Jobscience**.

    ![Konfigurowanie logowania jednokrotnego](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Jobscience w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Jobscience.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

