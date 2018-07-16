---
title: 'Samouczek: Integracja usługi Azure Active Directory z Tableau Server | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 4c88d6a8b22e4c5c19112c30e15bc960bc8ed1b5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045227"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Samouczek: Integracja usługi Azure Active Directory z Tableau Server

W tym samouczku dowiesz się, jak zintegrować Tableau Server w usłudze Azure Active Directory (Azure AD).

Integrowanie Tableau Server z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Tableau Server
- Użytkowników, aby automatycznie uzyskać zalogowanych do Tableau Server (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Tableau Server, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Serwer firmy Tableau logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Tableau Server z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-tableau-server-from-the-gallery"></a>Dodawanie Tableau Server z galerii
Aby skonfigurować integrację Tableau Server w usłudze Azure AD, należy dodać Tableau Server z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać serwer firmy Tableau z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Tableau Server**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauserver-tutorial/tutorial_tableauserver_search.png)

5. W panelu wyników wybierz **Tableau Server**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauserver-tutorial/tutorial_tableauserver_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Tableau Server, w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Tableau Server dla użytkownika w usłudze Azure AD. Oznacza to relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika Tableau Server musi zostać ustanowione.

Serwer firmy Tableau przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Tableau Server, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Tableau Server](#creating-a-tableau-server-test-user)**  — aby odpowiednikiem Britta Simon w serwer firmy Tableau, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji serwer firmy Tableau.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Tableau Server, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Tableau Server** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial_tableauserver_samlbase.png)

3. Na **Tableau Server domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial_tableauserver_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://azure.<domain name>.link`
    
    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://azure.<domain name>.link`

    c. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://azure.<domain name>.link/wg/saml/SSO/index.html`
     
    > [!NOTE] 
    > Powyższe wartości nie są rzeczywiste wartości. Później należy zaktualizować wartości przy użyciu rzeczywistego adresu URL i identyfikator na stronie konfiguracji serwer firmy Tableau. 

4. TABLEAU Server aplikacja oczekuje twierdzenia SAML, w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z **"Atrybuty użytkownika"** sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego samego.
    
    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/3.png)
    
5. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | ---------------| --------------- |    
    | nazwa użytkownika | *user.mailnickname* |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial_officespace_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial_officespace_05.png)
    
    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.
    
    d. Kliknij przycisk **Ok**


6. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial_tableauserver_certificate.png) 

7. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial_general_400.png)
<CS>
8. Aby uzyskać logowanie Jednokrotne skonfigurowane pod kątem swojej aplikacji, musisz logowanie jednokrotne do swojej dzierżawy Tableau Server jako administrator.
   
   a. W konfiguracji Tableau Server kliknij **SAML** kartę.
  
    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial_tableauserver_001.png) 
  
   b. Zaznacz pole wyboru z **SAML użycia dla logowania jednokrotnego**.
   
   c. Serwer firmy TABLEAU zwrotny adres URL — adres URL, który serwer firmy Tableau użytkownicy będą uzyskiwać dostęp do, takich jak http://tableau_server. Za pomocą http://localhost nie jest zalecane. Przy użyciu adresu URL kończących się ukośnikiem (na przykład http://tableau_server/) nie jest obsługiwane. Kopiuj **Tableau Server zwrotny adres URL** i wkleić go do usługi Azure AD **na adres URL logowania** polu tekstowym w **Tableau Server domena i adresy URL** sekcji.
   
   d. Identyfikator jednostki SAML — identyfikator jednostki jednoznacznie identyfikuje serwer firmy Tableau instalacji dostawcy tożsamości. Możesz wprowadzić adres URL serwera Tableau ponownie, jeśli chcesz, ale nie musi być adresem URL Twojej Tableau Server. Kopiowanie **identyfikator jednostki SAML** i wkleić go do usługi Azure AD **identyfikator** polu tekstowym w **Tableau Server domena i adresy URL** sekcji.
     
   e. Kliknij przycisk **Eksportuj plik metadanych** i otwórz go w aplikacji edytora tekstu. Znajdź adres URL usługi konsumenta potwierdzenia z żądania Http Post i indeks 0 i skopiuj adres URL. Teraz wklej go do usługi Azure AD **adres URL odpowiedzi** polu tekstowym w **Tableau Server domena i adresy URL** sekcji.
   
   f. Zlokalizuj plik metadanych Federacji, pobrany z witryny Azure portal, a następnie przekaż go w **plik metadanych SAML dostawcy tożsamości**.
   
   g. Kliknij przycisk **OK** przycisk na stronie Konfiguracja serwera Tableau.
   
    >[!NOTE] 
    >Klient musi przekazać dowolny certyfikat w konfiguracji logowania jednokrotnego SAML serwer firmy Tableau i będą Pobierz ignorowane w przepływie logowania jednokrotnego.
    >Jeśli potrzebujesz pomoc dotyczącą konfigurowania SAML na serwer firmy Tableau można znaleźć w tym artykule [skonfigurować SAML](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).
    >
<CE>

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauserver-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauserver-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauserver-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauserver-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-tableau-server-test-user"></a>Tworzenie użytkownika testowego Tableau Server

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon Tableau Server. Należy aprowizować wszystkich użytkowników na serwerze Tableau. 

Że nazwa użytkownika powinna odpowiadać wartości, które zostały skonfigurowane w atrybucie niestandardowym usługi Azure AD z **username**. Za pomocą mapowania poprawne integracji powinny działać [usługi Azure AD Konfigurowanie logowania jednokrotnego](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Jeśli musisz ręcznie utworzyć użytkownika, należy skontaktować się z administratorem Tableau Server w Twojej organizacji.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do serwera Tableau.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Tableau Server, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Tableau Server**.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauserver-tutorial/tutorial_tableauserver_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Tableau Server w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Tableau Server.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/tableauserver-tutorial/tutorial_general_04.png

[100]: ./media/tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/tableauserver-tutorial/tutorial_general_201.png
[202]: ./media/tableauserver-tutorial/tutorial_general_202.png
[203]: ./media/tableauserver-tutorial/tutorial_general_203.png

