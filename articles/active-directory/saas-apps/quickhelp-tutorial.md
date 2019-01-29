---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą QuickHelp | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i QuickHelp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 655c9ad3-2076-4e2c-8e47-9ed3bf04be56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: jeedes
ms.openlocfilehash: a4b528e912fe267d5988080fb15b85db0c87fdfd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193747"
---
# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą QuickHelp

W tym samouczku dowiesz się, jak zintegrować QuickHelp w usłudze Azure Active Directory (Azure AD).

Integrowanie QuickHelp z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do QuickHelp
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do QuickHelp (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą QuickHelp, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- QuickHelp logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie QuickHelp z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-quickhelp-from-the-gallery"></a>Dodawanie QuickHelp z galerii
Aby skonfigurować integrację QuickHelp w usłudze Azure AD, należy dodać QuickHelp z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać QuickHelp z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **QuickHelp**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/quickhelp-tutorial/tutorial_quickhelp_search.png)

1. W panelu wyników wybierz **QuickHelp**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/quickhelp-tutorial/tutorial_quickhelp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą QuickHelp w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w QuickHelp do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w QuickHelp musi można ustanowić.

W QuickHelp, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą QuickHelp, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego QuickHelp](#creating-a-quickhelp-test-user)**  — aby odpowiednikiem Britta Simon w QuickHelp połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji QuickHelp.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z QuickHelp, wykonaj następujące czynności:**

1. W witrynie Azure portal na **QuickHelp** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/quickhelp-tutorial/tutorial_quickhelp_samlbase.png)

1. Na **QuickHelp domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/quickhelp-tutorial/tutorial_quickhelp_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://quickhelp.com/<ROUTEURL>`

    b. W **identyfikator** pole tekstowe, wpisz adres URL: `https://auth.quickhelp.com`

    > [!NOTE] 
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z administratorem QuickHelp Twojej organizacji lub mózgów Menedżera Powodzenie klienta, aby uzyskać wartość.
 
1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/quickhelp-tutorial/tutorial_quickhelp_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/quickhelp-tutorial/tutorial_general_400.png) 

1. Zaloguj się do witryny firmy QuickHelp jako administrator.

1. W menu u góry kliknij pozycję **Admin** (Administrator).
   
    ![Konfigurowanie logowania jednokrotnego][21]

1. W **administratora QuickHelp** menu, kliknij przycisk **ustawienia**.
   
    ![Konfigurowanie logowania jednokrotnego][22]

1. Kliknij przycisk **ustawienia uwierzytelniania**.

1. Na **ustawienia uwierzytelniania** strony, wykonaj następujące czynności
   
    ![Konfigurowanie logowania jednokrotnego][23]
   
    a. Jako **typu logowania jednokrotnego**, wybierz opcję **WSFederation**.
   
    b. Aby przekazać plik pobrany metadanych platformy Azure, kliknij przycisk **Przeglądaj**, przejdź do pliku, następnie kliknij przycisk Zakończ **Przekazywanie metadanych**.
   
    c. W polu tekstowym **Email** (Adres e-mail) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
   
    d. W **imię** polu tekstowym `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
   
    e. W **nazwisko** polu tekstowym `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
   
    f. W **pasek akcji**, kliknij przycisk **Zapisz**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/quickhelp-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/quickhelp-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/quickhelp-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/quickhelp-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-quickhelp-test-user"></a>Tworzenie użytkownika testowego QuickHelp

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w QuickHelp.
Dla logowania jednokrotnego do pracy usługi Azure AD musi wiedzieć, co to jest odpowiednikiem użytkownikowi w QuickHelp użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w QuickHelp musi można ustanowić.

QuickHelp obsługę just-in-time. Oznacza to, jeśli to konieczne, konto użytkownika zostanie automatycznie utworzone w QuickHelp i konto jest połączony z kontem usługi Azure AD.

W tej sekcji nie musisz niczego robić.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do QuickHelp.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon QuickHelp, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **QuickHelp**.

    ![Konfigurowanie logowania jednokrotnego](./media/quickhelp-tutorial/tutorial_quickhelp_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.  

Po kliknięciu kafelka QuickHelp w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji QuickHelp.


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/quickhelp-tutorial/tutorial_general_04.png

[100]: ./media/quickhelp-tutorial/tutorial_general_100.png

[200]: ./media/quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/quickhelp-tutorial/tutorial_general_202.png
[203]: ./media/quickhelp-tutorial/tutorial_general_203.png
[21]: ./media/quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/quickhelp-tutorial/tutorial_quickhelp_07.png
