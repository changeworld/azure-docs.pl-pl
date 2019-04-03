---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Tableau Online | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5e3087c21908600be9cd369a15f3036e5acb2f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884707"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Tableau Online

W tym samouczku dowiesz się, jak zintegrować Tableau Online z usługą Azure Active Directory (Azure AD).

Integrowanie Tableau Online z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi Online firmy Tableau
- Użytkowników, aby automatycznie uzyskać zalogowanych do Tableau Online (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Tableau Online, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Tableau Online logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Tableau Online za pomocą galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-tableau-online-from-the-gallery"></a>Dodawanie Tableau Online za pomocą galerii
Aby skonfigurować integrację Tableau online w usłudze Azure AD, należy dodać Tableau Online z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Tableau Online z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Tableau Online**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_search.png)

1. W panelu wyników wybierz **Tableau Online**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji konfigurowania i testowania usługi Azure AD logowanie jednokrotne z usługą Online Tableau oparte na użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Tableau w trybie Online dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w dokumentacji Online Tableau musi nawiązać.

W dokumentacji Online Tableau, przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z usługą Tableau Online, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Tableau Online](#creating-a-tableau-online-test-user)**  — aby odpowiednikiem Britta Simon w Tableau Online, które jest połączone z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Tableau Online.

**Aby skonfigurować usługi Azure AD logowanie jednokrotne z usługą Tableau Online, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Tableau Online** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

1. Na **adresy URL i domen w trybie Online Tableau** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://sso.online.tableau.com`

    b. W polu tekstowym **Identifier** (Identyfikator) wpisz adres URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_general_400.png)

1. W oknie innej przeglądarki logowanie jednokrotne do aplikacji Tableau Online. Przejdź do **ustawienia** i następnie **uwierzytelniania**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)
    
1. Aby włączyć protokół SAML, w obszarze **typy uwierzytelniania** sekcji. Sprawdź **logowania jednokrotnego przy użyciu protokołu SAML** pola wyboru.
   
    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

1. Przewiń w dół aż **Importuj plik metadanych do usługi Online firmy Tableau** sekcji.  Kliknij przycisk Przeglądaj, a następnie Importuj plik metadanych, które zostały pobrane z usługi Azure AD. Następnie kliknij przycisk **Zastosuj**.
   
   ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

1. W **dopasowania potwierdzenia** sekcji, Wstaw nazwę odpowiedniego potwierdzenie dostawcy tożsamości dla **adres e-mail**, **imię**, i **nazwisko**. Aby uzyskać te informacje z usługi Azure AD: 
  
    a. W witrynie Azure portal, przejdź na **Tableau Online** strony integracji aplikacji.
    
    b. W obszarze atrybuty wybierz **"Wyświetl i Edytuj wszystkie inne atrybuty użytkownika"** pola wyboru. 
    
   ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/attributesection.png)
      
    c. Skopiuj wartość przestrzeni nazw dla tych atrybutów: givenname, poczty e-mail i nazwisko wykonując następujące kroki:

   ![Usługa Azure AD logowanie jednokrotne](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. Kliknij przycisk **user.givenname** wartość 
    
    e. Skopiuj wartości z **przestrzeni nazw** pola tekstowego.

   ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/attributesection2.png)

    f. Aby skopiować przestrzeni nazw wartości dla poczty e-mail i nazwisko postępuj zgodnie z poprzednich kroków.

    g. Przełącz się do aplikacji firmy Tableau Online, a następnie ustaw **atrybuty w trybie Online Tableau** sekcji w następujący sposób:
     * Adres e-mail: **poczty** lub **userprincipalname**
     * Imię: **givenname**
     * Nazwisko: **nazwisko**
   
   ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-tableau-online-test-user"></a>Tworzenie użytkownika testowego Tableau Online

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Tableau w trybie Online.

1. Na **Tableau Online**, kliknij przycisk **ustawienia** i następnie **uwierzytelniania** sekcji. Przewiń w dół do **Wybieranie: Użytkownicy** sekcji. Kliknij przycisk **dodawania użytkowników** i następnie **wprowadź adresy E-mail**.
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)
1. Wybierz **Dodawanie użytkowników do uwierzytelniania logowania jednokrotnego (SSO)**. W **wprowadź adresy E-mail** Dodaj pole tekstowe britta.simon@contoso.com
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)
1. Kliknij pozycję **Utwórz**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do usługi Online firmy Tableau.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Tableau Online, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Tableau Online**.

    ![Konfigurowanie logowania jednokrotnego](./media/tableauonline-tutorial/tutorial_tableauonline_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Add** (Dodaj). Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka Tableau Online, w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do Tableau Online aplikacji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/tableauonline-tutorial/tutorial_general_203.png
