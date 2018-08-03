---
title: 'Samouczek: Integracja usługi Azure Active Directory z Origami | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Origami.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a28bb0ba-b564-46ba-accc-e587699295d4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 9ec165253898ec77da2c7ae0e98cab578e773094
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429169"
---
# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Samouczek: Integracja usługi Azure Active Directory z Origami

W tym samouczku dowiesz się, jak zintegrować Origami w usłudze Azure Active Directory (Azure AD).

Integrowanie Origami z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Origami
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Origami (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Origami, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Origami logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Origami z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-origami-from-the-gallery"></a>Dodawanie Origami z galerii
Aby skonfigurować integrację Origami w usłudze Azure AD, należy dodać Origami z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Origami z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **Origami**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/origami-tutorial/tutorial_origami_search.png)

1. W panelu wyników wybierz **Origami**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/origami-tutorial/tutorial_origami_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Origami w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Origami do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Origami musi można ustanowić.

W Origami, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Origami, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Origami](#creating-an-origami-test-user)**  — aby odpowiednikiem Britta Simon w Origami połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Origami.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Origami, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Origami** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_origami_samlbase.png)

1. Na **Origami domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_origami_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://live.origamirisk.com/origami/account/login?account=<companyname>`

    > [!NOTE] 
    > Wartość nie jest prawdziwe. Zaktualizuj wartość za pomocą adresu URL logowania rzeczywistych. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Origami](https://wordpress.org/support/theme/origami) można uzyskać wartość. 
 
1. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_origami_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_general_400.png)

1. Na **konfiguracji Origami** , kliknij przycisk **skonfigurować Origami** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_origami_configure.png) 

1. Zaloguj się do konta Origami przy użyciu uprawnień administratora.

1. W menu u góry kliknij **administratora**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_origami_51.png)

1. Na stronie okna dialogowego konfiguracji na znak pojedynczego wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_origami_531.png)

    a. Wybierz **Włącz logowanie jednokrotne**.

    b. W **dostawcy tożsamości za logowania adres URL strony** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    c. W **adres URL strony wylogowania dostawcy tożsamości** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    d. Kliknij przycisk **Przeglądaj** można przekazać certyfikatu, zostały pobrane z witryny Azure portal.

    e. Kliknij przycisk **Zapisz zmiany**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/origami-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/origami-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/origami-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/origami-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-an-origami-test-user"></a>Tworzenie użytkownika testowego Origami

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Origami. 

1. Zaloguj się do konta Origami przy użyciu uprawnień administratora.

1. W menu u góry kliknij **administratora**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_origami_51.png)

1. Na **użytkowników i zabezpieczeń** okno dialogowe, kliknij przycisk **użytkowników**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_origami_54.png)

1. Kliknij przycisk **Dodaj nowego użytkownika**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_origami_55.png)

1. W oknie dialogowym Dodawanie nowego użytkownika wykonaj następujące czynności:
   
    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_origami_56.png)

    a. W **nazwa_użytkownika** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    b. W **hasło** pole tekstowe, wpisz hasło.

    c. W **Potwierdź hasło** polu tekstowym wpisz ponownie hasło.

    d. W **imię** polu tekstowym Wprowadź imię użytkownika, takich jak **Britta**.

    e. W **nazwisko** polu tekstowym Wprowadź nazwisko użytkownika, takich jak **Simon**.

    f. Kliknij pozycję **Zapisz**.
   
    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_origami_57.png)

1. Przypisz **ról użytkownika** i **dostępu klienta** dla użytkownika. 
   
    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Origami.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Origami, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Origami**.

    ![Konfigurowanie logowania jednokrotnego](./media/origami-tutorial/tutorial_origami_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Origami w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Origami.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/origami-tutorial/tutorial_general_01.png
[2]: ./media/origami-tutorial/tutorial_general_02.png
[3]: ./media/origami-tutorial/tutorial_general_03.png
[4]: ./media/origami-tutorial/tutorial_general_04.png

[100]: ./media/origami-tutorial/tutorial_general_100.png

[200]: ./media/origami-tutorial/tutorial_general_200.png
[201]: ./media/origami-tutorial/tutorial_general_201.png
[202]: ./media/origami-tutorial/tutorial_general_202.png
[203]: ./media/origami-tutorial/tutorial_general_203.png

