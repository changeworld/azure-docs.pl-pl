---
title: 'Samouczek: Integracja usługi Azure Active Directory z Syncplicity | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 3b74ca178d3bf380dc759ce0325d4047891a39d3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422396"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Samouczek: Integracja usługi Azure Active Directory z Syncplicity

W tym samouczku dowiesz się, jak zintegrować Syncplicity w usłudze Azure Active Directory (Azure AD).

Integrowanie Syncplicity z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Syncplicity
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Syncplicity (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Syncplicity, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Syncplicity logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Syncplicity z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-syncplicity-from-the-gallery"></a>Dodawanie Syncplicity z galerii
Aby skonfigurować integrację Syncplicity w usłudze Azure AD, należy dodać Syncplicity z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Syncplicity z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Syncplicity**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/syncplicity-tutorial/tutorial_syncplicity_search.png)

1. W panelu wyników wybierz **Syncplicity**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Syncplicity w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Syncplicity do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Syncplicity musi można ustanowić.

W Syncplicity, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Syncplicity, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Syncplicity](#creating-a-syncplicity-test-user)**  — aby odpowiednikiem Britta Simon w Syncplicity połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Syncplicity.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Syncplicity, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Syncplicity** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

1. Na **Syncplicity domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.syncplicity.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Syncplicity](https://www.syncplicity.com/contact-us) do uzyskania tych wartości. 
 

1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/syncplicity-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Syncplicity** , kliknij przycisk **skonfigurować Syncplicity** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/syncplicity-tutorial/tutorial_syncplicity_configure.png) 

1. Zaloguj się do Twojej **Syncplicity** dzierżawy.

1. W menu u góry kliknij **administratora**, wybierz opcję **ustawienia**, a następnie kliknij przycisk **niestandardowej domeny i logowanie jednokrotne**.
   
    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na **pojedynczego logowania jednokrotnego (SSO)** okna dialogowego strony, wykonaj następujące czynności:
   
    ![Logowanie jednokrotne \(logowania jednokrotnego\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. W **domena niestandardowa** polu tekstowym wpisz nazwę domeny.
  
    b. Wybierz **włączone** jako **pojedynczego stanu logowania jednokrotnego**.

    c. W **identyfikator jednostki** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** skopiowanej w witrynie Azure portal.

    d. W **adres URL logowania strony** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

    e. W **adres URL strony wylogowania** pola tekstowego, Wklej **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    f. W **certyfikatu dostawcy tożsamości**, kliknij przycisk **Choose file**, a następnie przekaż certyfikat, który został pobrany z witryny Azure portal. 

    g. Kliknij przycisk **ZAPISAĆ zmiany w**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/syncplicity-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/syncplicity-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/syncplicity-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/syncplicity-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-syncplicity-test-user"></a>Tworzenie użytkownika testowego Syncplicity
Dla użytkowników usługi AAD można było się zalogować musi być obsługiwana Syncplicity aplikacji. W tej sekcji opisano sposób tworzenia konta użytkownika usługi AAD w Syncplicity.

**Aby udostępnić konto Syncplicity, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Syncplicity** dzierżawy (na przykład: `https://company.Syncplicity.com`).

1. Kliknij przycisk **administratora** i wybierz **kont użytkowników**.

1. Kliknij przycisk **Dodawanie użytkownika**.
   
    ![Zarządzanie użytkownikami](./media/syncplicity-tutorial/ic769764.png "Zarządzanie użytkownikami")

1. Typ **Zezwól E-mail** chcesz aprowizować konta usługi AAD, wybierz **użytkownika** jako **roli**, a następnie kliknij przycisk **dalej**.
   
    ![Informacje o koncie](./media/syncplicity-tutorial/ic769765.png "informacje o koncie")
   
    >[!NOTE]
    >Właściciel konta usługi AAD otrzymuje wiadomość e-mail, w tym link do potwierdzenia i aktywować konto. 
    > 

1. Wybierz grupę w Twojej firmie, która nowy użytkownik powinien zostać członkiem, a następnie kliknij **dalej**.
   
    ![Członkostwo w grupie](./media/syncplicity-tutorial/ic769772.png "członkostwo w grupie")
   
    >[!NOTE]
    >Jeśli nie ma żadnych grup, na liście, kliknij przycisk **dalej**. 
    > 

1. Wybierz foldery, o których chcesz umieścić pod kontrolą firmy Syncplicity na komputerze użytkownika, a następnie kliknij przycisk **dalej**.
   
    ![Foldery Syncplicity](./media/syncplicity-tutorial/ic769773.png "Syncplicity folderów")

>[!NOTE]
>Można użyć jakichkolwiek innych Syncplicity użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Syncplicity do aprowizacji kont użytkowników usługi AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Syncplicity.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Syncplicity, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Syncplicity**.

    ![Konfigurowanie logowania jednokrotnego](./media/syncplicity-tutorial/tutorial_syncplicity_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Syncplicity w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Syncplicity.
## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/syncplicity-tutorial/tutorial_general_203.png

