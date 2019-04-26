---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą LiquidFiles | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i LiquidFiles.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: cb517134-0b34-4a74-b40c-5a3223ca81b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20a3144f2a8727420803034426106a29a7924727
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60258575"
---
# <a name="tutorial-azure-active-directory-integration-with-liquidfiles"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą LiquidFiles

W tym samouczku dowiesz się, jak zintegrować LiquidFiles w usłudze Azure Active Directory (Azure AD).

Integrowanie LiquidFiles z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do LiquidFiles
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do LiquidFiles (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą LiquidFiles, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- LiquidFiles logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowiska próbnego usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie LiquidFiles z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-liquidfiles-from-the-gallery"></a>Dodawanie LiquidFiles z galerii
Aby skonfigurować integrację LiquidFiles w usłudze Azure AD, należy dodać LiquidFiles z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać LiquidFiles z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **LiquidFiles**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/liquidfiles-tutorial/tutorial_liquidfiles_search.png)

1. W panelu wyników wybierz **LiquidFiles**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/liquidfiles-tutorial/tutorial_liquidfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą LiquidFiles w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w LiquidFiles do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w LiquidFiles musi można ustanowić.

W LiquidFiles, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą LiquidFiles, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego LiquidFiles](#creating-a-liquidfiles-test-user)**  — aby odpowiednikiem Britta Simon w LiquidFiles połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji LiquidFiles.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z LiquidFiles, wykonaj następujące czynności:**

1. W witrynie Azure portal na **LiquidFiles** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/liquidfiles-tutorial/tutorial_liquidfiles_samlbase.png)

1. Na **LiquidFiles domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/liquidfiles-tutorial/tutorial_liquidfiles_url.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<YOUR_SERVER_URL>/saml/init`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<YOUR_SERVER_URL>`

    c. b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<YOUR_SERVER_URL>/saml/consume`

    > [!NOTE] 
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywisty adres URL logowania, identyfikator i adres URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta LiquidFiles](https://www.liquidfiles.com/support.html) do uzyskania tych wartości. 
 
1. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartość certyfikatu.

    ![Konfigurowanie logowania jednokrotnego](./media/liquidfiles-tutorial/tutorial_liquidfiles_certificate.png) 

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie logowania jednokrotnego](./media/liquidfiles-tutorial/tutorial_general_400.png)

1. Na **konfiguracji LiquidFiles** , kliknij przycisk **skonfigurować LiquidFiles** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/liquidfiles-tutorial/tutorial_liquidfiles_configure.png)
 
1. Zaloguj się do witryny firmy LiquidFiles jako administrator.

1. Kliknij przycisk **logowania jednokrotnego** w **Administrator > Konfiguracja** z menu.

1. Na **konfiguracji rejestracji jednokrotnej** strony, wykonaj następujące czynności

    ![Konfigurowanie logowania jednokrotnego](./media/liquidfiles-tutorial/tutorial_single_01.png)

    a. Jako **pojedynczy znak na metodę**, wybierz opcję **SAML 2**.

    b. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    c. W **adres URL wylogowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    d. W **odcisk palca certyfikatu tożsamości** pola tekstowego, Wklej **odcisk PALCA** wartości, które zostały skopiowane z witryny Azure portal...

    e. W polu tekstowym Format identyfikatora nazwy, wpisz wartość **urn: oasis: nazwy: tc: SAML:1.1:nameid — format: emailAddress**.

    f. W polu tekstowym kontekstu uwierzytelniania, wpisz wartość **urn: oasis: nazwy: tc: SAML:2.0:ac:classes:PasswordProtectedTransport**.

    g. Kliknij pozycję **Zapisz**.  

> [!TIP]
> Teraz możesz korzystać ze zwięzłej wersji tych instrukcji w witrynie [Azure Portal](https://portal.azure.com) podczas konfigurowania aplikacji.  Po dodaniu tej aplikacji z sekcji **Active Directory > Aplikacje dla przedsiębiorstw** wystarczy kliknąć kartę **Logowanie jednokrotne** i uzyskać dostęp do osadzonej dokumentacji za pośrednictwem sekcji  **Konfiguracja** w dolnej części strony. Dalsze informacje o funkcji dokumentacji osadzonej można znaleźć tutaj: [Osadzona dokumentacja usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/liquidfiles-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/liquidfiles-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/liquidfiles-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/liquidfiles-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-liquidfiles-test-user"></a>Tworzenie użytkownika testowego LiquidFiles

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w LiquidFiles. Współpracować z administratorem serwera LiquidFiles zapoznania dodany jako użytkownik, przed zalogowaniem się do aplikacji LiquidFiles.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do LiquidFiles.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon LiquidFiles, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **LiquidFiles**.

    ![Konfigurowanie logowania jednokrotnego](./media/liquidfiles-tutorial/tutorial_liquidfiles_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka LiquidFiles w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji LiquidFiles.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/liquidfiles-tutorial/tutorial_general_01.png
[2]: ./media/liquidfiles-tutorial/tutorial_general_02.png
[3]: ./media/liquidfiles-tutorial/tutorial_general_03.png
[4]: ./media/liquidfiles-tutorial/tutorial_general_04.png

[100]: ./media/liquidfiles-tutorial/tutorial_general_100.png

[200]: ./media/liquidfiles-tutorial/tutorial_general_200.png
[201]: ./media/liquidfiles-tutorial/tutorial_general_201.png
[202]: ./media/liquidfiles-tutorial/tutorial_general_202.png
[203]: ./media/liquidfiles-tutorial/tutorial_general_203.png

