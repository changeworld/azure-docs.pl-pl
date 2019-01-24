---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem chlorowców | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i chlorowców oprogramowania.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 8220f94be58cd5602ffcb814d8b24db4c19e1f41
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827979"
---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem chlorowców

W tym samouczku dowiesz się, jak zintegrować chlorowców oprogramowania z usługi Azure Active Directory (Azure AD).

Integracja oprogramowania chlorowców z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do oprogramowania chlorowców
- Użytkowników, aby automatycznie uzyskać zalogowanych do oprogramowania chlorowców (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu oprogramowania chlorowców, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Oprogramowanie chlorowców logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie oprogramowania chlorowców z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-halogen-software-from-the-gallery"></a>Dodawanie oprogramowania chlorowców z galerii

Aby skonfigurować integrację chlorowców oprogramowania w usłudze Azure AD, należy dodać chlorowców oprogramowania z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać chlorowców oprogramowania z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **oprogramowania chlorowców**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/halogen-software-tutorial/tutorial_halogensoftware_search.png)

1. W panelu wyników wybierz **oprogramowania chlorowców**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/halogen-software-tutorial/tutorial_halogensoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą oprogramowania chlorowców oparte na użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w oprogramowaniu chlorowców do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w oprogramowaniu chlorowców musi nawiązać.

W oprogramowaniu chlorowców przypisze się wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu oprogramowania chlorowców, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego oprogramowania chlorowców](#creating-a-halogen-software-test-user)**  — aby odpowiednikiem Britta Simon w oprogramowaniu chlorowców, połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji chlorowców oprogramowania.

**Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu oprogramowania chlorowców, wykonaj następujące czynności:**

1. W witrynie Azure portal na **oprogramowania chlorowców** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/halogen-software-tutorial/tutorial_halogensoftware_samlbase.png)

1. Na **chlorowców oprogramowania domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/halogen-software-tutorial/tutorial_halogensoftware_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://global.hgncloud.com/<companyname>`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://global.halogensoftware.com/<companyname>`, `https://global.hgncloud.com/<companyname>`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL i identyfikatora logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej chlorowców oprogramowanie klienckie](https://support.halogensoftware.com/) do uzyskania tych wartości. 
 


1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/halogen-software-tutorial/tutorial_halogensoftware_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/halogen-software-tutorial/tutorial_general_400.png)

1. W oknie innej przeglądarki, zaloguj się do Twojej **oprogramowania chlorowców** aplikacji jako administrator.

1. Kliknij przycisk **opcje** kartę. 
   
    ![Co to jest program Azure AD Connect][12]

1. W okienku nawigacji po lewej stronie kliknij **plik konfiguracji SAML**. 
   
    ![Co to jest program Azure AD Connect][13]

1. Na **plik konfiguracji SAML** strony, wykonaj następujące czynności: 

    ![Co to jest program Azure AD Connect][14]

     a. Jako **Unikatowy identyfikator**, wybierz opcję **NameID**.

     b. Jako **mapy Unikatowy identyfikator do**, wybierz opcję **Username**.
  
     c. Aby przekazać plik metadanych pobrany, kliknij przycisk **Przeglądaj** wybrać plik, a następnie **Przekaż plik**.
 
     d. Aby przetestować konfigurację, kliknij pozycję **Uruchom Test**. 
    
    >[!NOTE]
    >Musisz czekać na komunikat "*testu SAML. Zamknij to okno*". Zamknięcie okna przeglądarki otwartego. **Włącz SAML** pole wyboru jest włączona jedynie wtedy, jeśli test został ukończony. 
     
     e. Wybierz **Włącz SAML**.
    
     f. Kliknij przycisk **Save Changes** (Zapisz zmiany). 

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/halogen-software-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/halogen-software-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/halogen-software-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/halogen-software-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz nazwę jako **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-halogen-software-test-user"></a>Tworzenie użytkownika testowego chlorowców oprogramowania

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon chlorowców oprogramowania.

**Aby utworzyć użytkownika o nazwie Britta Simon chlorowców oprogramowania, wykonaj następujące czynności:**

1. Zaloguj się na swoje **oprogramowania chlorowców** aplikacji jako administrator.

1. Kliknij przycisk **użytkownika Centrum** kartę, a następnie kliknij przycisk **Create User**.
   
    ![Co to jest program Azure AD Connect][300]  

1. Na **nowego użytkownika** okna dialogowego strony, wykonaj następujące czynności:
   
    ![Co to jest program Azure AD Connect][301]

    a. W **imię** polu tekstowym wpisz imię użytkownika, takich jak **Britta**.
    
    b. W **nazwisko** pole tekstowe, wpisz nazwisko użytkownika, takich jak **Simon**. 

    c. W **Username** polu tekstowym wpisz **Britta Simon**, nazwę użytkownika, tak jak w witrynie Azure portal.

    d. W **hasło** pole tekstowe, wpisz hasło dla Britta.
    
    e. Kliknij pozycję **Zapisz**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do oprogramowania chlorowców za pomocą platformy Azure logowania jednokrotnego.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon chlorowców oprogramowania, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **oprogramowania chlorowców**.

    ![Konfigurowanie logowania jednokrotnego](./media/halogen-software-tutorial/tutorial_halogensoftware_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po kliknięciu kafelka chlorowców oprogramowania w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji oprogramowania chlorowców.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/halogen-software-tutorial/tutorial_general_01.png
[2]: ./media/halogen-software-tutorial/tutorial_general_02.png
[3]: ./media/halogen-software-tutorial/tutorial_general_03.png
[4]: ./media/halogen-software-tutorial/tutorial_general_04.png

[12]: ./media/halogen-software-tutorial/tutorial_halogen_12.png

[13]: ./media/halogen-software-tutorial/tutorial_halogen_13.png

[14]: ./media/halogen-software-tutorial/tutorial_halogen_14.png

[100]: ./media/halogen-software-tutorial/tutorial_general_100.png

[200]: ./media/halogen-software-tutorial/tutorial_general_200.png
[201]: ./media/halogen-software-tutorial/tutorial_general_201.png
[202]: ./media/halogen-software-tutorial/tutorial_general_202.png
[203]: ./media/halogen-software-tutorial/tutorial_general_203.png

[300]: ./media/halogen-software-tutorial/tutorial_halogen_300.png

[301]: ./media/halogen-software-tutorial/tutorial_halogen_301.png
