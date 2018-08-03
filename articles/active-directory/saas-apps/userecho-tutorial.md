---
title: 'Samouczek: Integracja usługi Azure Active Directory z UserEcho | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i UserEcho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: ecdd37db662c6861e35f80bfbf4ac8ff7e0d08c9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443277"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Samouczek: Integracja usługi Azure Active Directory z UserEcho

W tym samouczku dowiesz się, jak zintegrować UserEcho w usłudze Azure Active Directory (Azure AD).

Integrowanie UserEcho z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do UserEcho
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do UserEcho (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą UserEcho, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- UserEcho logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięcznej wersji próbnej tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie UserEcho z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-userecho-from-the-gallery"></a>Dodawanie UserEcho z galerii
Aby skonfigurować integrację UserEcho w usłudze Azure AD, należy dodać UserEcho z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać UserEcho z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **UserEcho**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/userecho-tutorial/tutorial_userecho_search.png)

1. W panelu wyników wybierz **UserEcho**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą UserEcho w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w UserEcho do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w UserEcho musi można ustanowić.

W UserEcho, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą UserEcho, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego UserEcho](#creating-a-userecho-test-user)**  — aby odpowiednikiem Britta Simon w UserEcho połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji UserEcho.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z UserEcho, wykonaj następujące czynności:**

1. W witrynie Azure portal na **UserEcho** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_samlbase.png)

1. Na **UserEcho domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.userecho.com/`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta UserEcho](https://feedback.userecho.com/) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_general_400.png)

1. Na **konfiguracji UserEcho** , kliknij przycisk **skonfigurować UserEcho** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_configure.png) 

1. W innym oknie przeglądarki Zaloguj się do witryny firmy UserEcho jako administrator.

1. Na pasku narzędzi u góry kliknij swoją nazwę użytkownika, aby rozwinąć menu, a następnie kliknij przycisk **Instalatora**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_06.png) 

1. Kliknij przycisk **integracje**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_07.png) 

1. Kliknij przycisk **witryny sieci Web**, a następnie kliknij przycisk **logowanie jednokrotne (SAML2)**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_08.png) 

1. Na **logowanie jednokrotne (SAML)** strony, wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Jako **włączone SAML**, wybierz opcję **tak**.
    
    b. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal do **adres URL logowania jednokrotnego SAML** pole tekstowe.
    
    c. Wklej **adres URL wylogowania**, który skopiowano z witryny Azure portal do **logoout zdalnego adresu URL** pola tekstowego.
    
    d. Otwórz pobranego certyfikatu w programie Notatnik, skopiuj zawartość, a następnie wklej go do **certyfikat X.509** pola tekstowego.
    
    e. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/userecho-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/userecho-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/userecho-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/userecho-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-userecho-test-user"></a>Tworzenie użytkownika testowego UserEcho

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w UserEcho.

**Aby utworzyć użytkownika o nazwie Britta Simon w UserEcho, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy UserEcho jako administrator.

1. Na pasku narzędzi u góry kliknij swoją nazwę użytkownika, aby rozwinąć menu, a następnie kliknij przycisk **Instalatora**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_06.png)

1. Kliknij przycisk **użytkowników**, aby rozwinąć **użytkowników** sekcji.
   
    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_10.png)

1. Kliknij przycisk **użytkowników**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_11.png)

1. Kliknij przycisk **Zaproś nowego użytkownika**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_12.png)

1. Na **Zaproś nowego użytkownika** okno dialogowe, należy wykonać następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. W **nazwa** polu tekstowym Nazwa typu użytkownika, takich jak Britta Simon.
    
    b.  W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.
    
    c. Kliknij przycisk **zaprosić**.

Zaproszenia są wysyłane do Britta, co umożliwia jej rozpocząć korzystanie z UserEcho. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do UserEcho.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon UserEcho, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **UserEcho**.

    ![Konfigurowanie logowania jednokrotnego](./media/userecho-tutorial/tutorial_userecho_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest test konfiguracji logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.  

Po kliknięciu kafelka UserEcho w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji UserEcho.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/userecho-tutorial/tutorial_general_01.png
[2]: ./media/userecho-tutorial/tutorial_general_02.png
[3]: ./media/userecho-tutorial/tutorial_general_03.png
[4]: ./media/userecho-tutorial/tutorial_general_04.png

[100]: ./media/userecho-tutorial/tutorial_general_100.png

[200]: ./media/userecho-tutorial/tutorial_general_200.png
[201]: ./media/userecho-tutorial/tutorial_general_201.png
[202]: ./media/userecho-tutorial/tutorial_general_202.png
[203]: ./media/userecho-tutorial/tutorial_general_203.png

