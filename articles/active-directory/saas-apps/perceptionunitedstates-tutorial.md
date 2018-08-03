---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą wrażenie Stany Zjednoczone (inne niż UltiPro) | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i wrażenie Stany Zjednoczone (inne niż UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 61fb9904e69f5269c345b733ef2396294c6c790a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448176"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą wrażenie Stany Zjednoczone (inne niż UltiPro)

W tym samouczku dowiesz się, jak zintegrować wrażenie Stany Zjednoczone (inne niż UltiPro) z usługą Azure Active Directory (Azure AD).

Integrowanie wrażenie Stany Zjednoczone (inne niż UltiPro) z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Stanów Zjednoczonych wrażenie (inne niż UltiPro).
- Aby umożliwić użytkownikom automatycznie pobrać zalogowanych do Stanów Zjednoczonych wrażenie (Non-UltiPro) (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą wrażenie Stany Zjednoczone (inne niż UltiPro), potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Wrażenie Stany Zjednoczone (inne niż UltiPro) logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie wrażenie Stany Zjednoczone (inne niż UltiPro) z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Dodawanie wrażenie Stany Zjednoczone (inne niż UltiPro) z galerii
Aby skonfigurować integrację ze Stanów Zjednoczonych wrażenie (inne niż UltiPro) w usłudze Azure AD, należy dodać wrażenie Stany Zjednoczone (inne niż UltiPro) z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać wrażenie Stany Zjednoczone (inne niż UltiPro) z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **wrażenie Stany Zjednoczone (inne niż UltiPro)**, wybierz opcję **wrażenie Stany Zjednoczone (inne niż UltiPro)** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

    ![Stany Zjednoczone wrażenie (inne niż UltiPro) na liście wyników](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą wrażenie Stanów Zjednoczonych (inne niż UltiPro) w oparciu o nazwie "Britta Simon" użytkownika testowego.

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Stanach Zjednoczonych wrażenie (inne niż UltiPro) dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Stanach Zjednoczonych wrażenie (inne niż UltiPro) musi zostać nawiązane.

W wrażenie Stanów Zjednoczonych (inne niż UltiPro), przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą wrażenie Stany Zjednoczone (inne niż UltiPro), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego wrażenie Stany Zjednoczone (inne niż UltiPro)](#create-a-perception-united-states-non-ultipro-test-user)**  — aby odpowiednikiem Britta Simon w wrażenie Stanów Zjednoczonych (inne niż UltiPro) połączoną usługę Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji wrażenie Stany Zjednoczone (inne niż UltiPro).

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą wrażenie Stany Zjednoczone (inne niż UltiPro), wykonaj następujące czynności:**

1. W witrynie Azure portal na **wrażenie Stany Zjednoczone (inne niż UltiPro)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

1. Na **wrażenie Stany Zjednoczone (inne niż UltiPro), domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Wrażenie Stanów Zjednoczonych (inne niż UltiPro) domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `https://perception.kanjoya.com/sp`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    > [!NOTE] 
    > Wartość nie jest prawdziwe. Wartość zostanie zaktualizowana o rzeczywistego adresu URL odpowiedzi, które wyjaśniono w dalszej części tego samouczka.
 
1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/perceptionunitedstates-tutorial/tutorial_general_400.png)

1. Na **konfiguracji wrażenie Stany Zjednoczone (inne niż UltiPro)** , kliknij przycisk **skonfigurować wrażenie Stany Zjednoczone (inne niż UltiPro)** otworzyć **Konfigurowanie logowania jednokrotnego** okna . Kopiuj **identyfikator jednostki SAML** z **krótki przewodnik po sekcji.**

    a. **Wrażenie Stany Zjednoczone (inne niż UltiPro)** aplikacja wymaga **identyfikator jednostki SAML** wartości, które zostały skopiowane do zakodowania jako identyfikator URI. Aby uzyskać wartość zakodowany jako identyfikator uri, użyj następującego linku:**http://www.url-encode-decode.com/**.

    b. Po otrzymaniu identyfikatora uri zakodowaną wartość połączyć ją z **adres URL odpowiedzi** wymienionych poniżej -

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. Wklej powyższą wartość w **adres URL odpowiedzi** polu tekstowym w **wrażenie Stany Zjednoczone (inne niż UltiPro), domena i adresy URL** sekcji.

    ![Wrażenie Stanów Zjednoczonych (inne niż UltiPro) konfiguracji](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

1. W innym oknie przeglądarki Zaloguj się do witryny firmy Perception Stany Zjednoczone (inne niż UltiPro) jako administrator.

1. Na głównym pasku narzędzi kliknij polecenie **ustawienia konta**.

    ![Wrażenie Stanów Zjednoczonych (inne niż UltiPro) użytkownika](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

1. Na **ustawienia konta** strony, wykonaj następujące czynności:

    ![Wrażenie Stanów Zjednoczonych (inne niż UltiPro) użytkownika](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. W **nazwa firmy** polu tekstowym wpisz nazwę **firmy**.
    
    b. W **nazwa konta** polu tekstowym wpisz nazwę **konta**.

    c. W **domyślne Odpowiedz do wiadomości E-mail** tekstu wpisz prawidłowe **E-mail**.

    d. Wybierz **logowania jednokrotnego dostawcy tożsamości** jako **SAML 2.0**.

1. Na **konfiguracji logowania jednokrotnego** strony, wykonaj następujące czynności:

    ![SSOConfig Stanów Zjednoczonych (inne niż UltiPro) wrażenie](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Wybierz **SAML NameID typu** jako **E-mail**.

    b. W **Nazwa konfiguracji logowania jednokrotnego** polu tekstowym wpisz nazwę swojej **konfiguracji**.
    
    c. W **nazwę dostawcy tożsamości** pola tekstowego, Wklej wartość **identyfikator jednostki SAML**, który skopiowano z witryny Azure portal. 

    d. W **pole tekstowe domeny SAML**, wprowadź domenę, takich jak **@contoso.com**.

    e. Kliknij pozycję **Przekaż ponownie** do przekazania **XML metadanych** pliku.

    f. Kliknij przycisk **aktualizacji**.


> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/perceptionunitedstates-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/perceptionunitedstates-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/perceptionunitedstates-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/perceptionunitedstates-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>Tworzenie użytkownika testowego wrażenie Stany Zjednoczone (inne niż UltiPro)

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Stanach Zjednoczonych wrażenie (inne niż UltiPro). Praca z [zespołem pomocy technicznej wrażenie Stany Zjednoczone (inne niż UltiPro)](http://www.ultimatesoftware.com/Contact/ContactUs) Aby dodać użytkowników na platformie wrażenie Stany Zjednoczone (inne niż UltiPro).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Stanów Zjednoczonych wrażenie (inne niż UltiPro).

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon do Stanów Zjednoczonych wrażenie (inne niż UltiPro), wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **wrażenie Stany Zjednoczone (inne niż UltiPro)**.

    ![Link wrażenie Stany Zjednoczone (inne niż UltiPro) na liście aplikacji](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka wrażenie Stany Zjednoczone (inne niż UltiPro) w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji wrażenie Stany Zjednoczone (inne niż UltiPro).
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/perceptionunitedstates-tutorial/tutorial_general_01.png
[2]: ./media/perceptionunitedstates-tutorial/tutorial_general_02.png
[3]: ./media/perceptionunitedstates-tutorial/tutorial_general_03.png
[4]: ./media/perceptionunitedstates-tutorial/tutorial_general_04.png

[100]: ./media/perceptionunitedstates-tutorial/tutorial_general_100.png

[200]: ./media/perceptionunitedstates-tutorial/tutorial_general_200.png
[201]: ./media/perceptionunitedstates-tutorial/tutorial_general_201.png
[202]: ./media/perceptionunitedstates-tutorial/tutorial_general_202.png
[203]: ./media/perceptionunitedstates-tutorial/tutorial_general_203.png

