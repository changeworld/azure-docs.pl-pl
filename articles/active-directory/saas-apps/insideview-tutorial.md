---
title: 'Samouczek: Integracja usługi Azure Active Directory z InsideView | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: fd004302018bf032675957c12e58759a0d4ed6e5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436278"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Samouczek: Integracja usługi Azure Active Directory z InsideView

W tym samouczku dowiesz się, jak zintegrować InsideView w usłudze Azure Active Directory (Azure AD).

Integrowanie InsideView z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do InsideView
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do InsideView (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą InsideView, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- InsideView logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie InsideView z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-insideview-from-the-gallery"></a>Dodawanie InsideView z galerii
Aby skonfigurować integrację InsideView w usłudze Azure AD, należy dodać InsideView z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać InsideView z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **InsideView**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/insideview-tutorial/tutorial_insideview_search.png)

1. W panelu wyników wybierz **InsideView**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/insideview-tutorial/tutorial_insideview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą InsideView w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w InsideView do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w InsideView musi można ustanowić.

W InsideView, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą InsideView, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego InsideView](#creating-a-insideview-test-user)**  — aby odpowiednikiem Britta Simon w InsideView połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji InsideView.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z InsideView, wykonaj następujące czynności:**

1. W witrynie Azure portal na **InsideView** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/insideview-tutorial/tutorial_insideview_samlbase.png)

1. Na **InsideView domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/insideview-tutorial/tutorial_insideview_url.png)
    
    W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywistego adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej InsideView ](mailto:support@insideview.com) aby zyskać tę wartość.
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Raw)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/insideview-tutorial/tutorial_insideview_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/insideview-tutorial/tutorial_general_400.png)

1. Na **konfiguracji InsideView** , kliknij przycisk **skonfigurować InsideView** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/insideview-tutorial/tutorial_insideview_configure.png) 

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy InsideView.

1. Na pasku narzędzi u góry kliknij **administratora**, **ustawienia SingleSignOn**, a następnie kliknij przycisk **Dodaj SAML**.
   
   ![SAML logowania jednokrotnego ustawienia](./media/insideview-tutorial/ic794135.png "SAML logowania jednokrotnego ustawienia")

1. W **Dodaj nowe SAML** sekcji, wykonaj następujące czynności:

    ![Dodaj nowe SAML](./media/insideview-tutorial/ic794136.png "dodać nowego języka SAML")
   
    a. W **nazwa usługi STS** polu tekstowym wpisz nazwę dla danej konfiguracji.

    b. W **SamlP/WS-Fed niechciane punktu końcowego** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.
    
    c. Otwórz certyfikat zakodowany base-64, który został pobrany z witryny Azure portal, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu usługi STS** pola tekstowego.

    d. W **mapowanie identyfikatora użytkownika Crm** polu tekstowym wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
        
    e. W **mapowanie E-mail Crm** polu tekstowym wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. W **mapowanie imię Crm** polu tekstowym wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    g. W **Crm lastName mapowanie** polu tekstowym wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.  

    h. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 
 
### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/insideview-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/insideview-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/insideview-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/insideview-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-insideview-test-user"></a>Tworzenie użytkownika testowego InsideView

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do InsideView, ich musi być obsługiwana w celu InsideView. W przypadku InsideView Inicjowanie obsługi administracyjnej jest zadanie ręczne.

Aby uzyskać użytkowników lub utworzone w InsideView kontakty, skontaktuj się z pomocą [zespołem pomocy technicznej InsideView](mailto:support@insideview.com).

>[!NOTE]
>Można użyć jakichkolwiek innych InsideView użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez InsideView można uaktywniać ich konta usługi Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do InsideView.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon InsideView, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **InsideView**.

    ![Konfigurowanie logowania jednokrotnego](./media/insideview-tutorial/tutorial_insideview_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka InsideView w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji InsideView.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/insideview-tutorial/tutorial_general_01.png
[2]: ./media/insideview-tutorial/tutorial_general_02.png
[3]: ./media/insideview-tutorial/tutorial_general_03.png
[4]: ./media/insideview-tutorial/tutorial_general_04.png

[100]: ./media/insideview-tutorial/tutorial_general_100.png

[200]: ./media/insideview-tutorial/tutorial_general_200.png
[201]: ./media/insideview-tutorial/tutorial_general_201.png
[202]: ./media/insideview-tutorial/tutorial_general_202.png
[203]: ./media/insideview-tutorial/tutorial_general_203.png

