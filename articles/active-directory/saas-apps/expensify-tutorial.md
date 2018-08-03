---
title: 'Samouczek: Integracja usługi Azure Active Directory z Expensify | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Expensify.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/2/2017
ms.author: jeedes
ms.openlocfilehash: d53877dbcc25edad14714633bfa11a0c3cbbf76e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433262"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>Samouczek: Integracja usługi Azure Active Directory z Expensify

W tym samouczku dowiesz się, jak zintegrować Expensify w usłudze Azure Active Directory (Azure AD).

Integrowanie Expensify z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Expensify.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Expensify (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Expensify, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Expensify logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Expensify z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-expensify-from-the-gallery"></a>Dodawanie Expensify z galerii
Aby skonfigurować integrację Expensify w usłudze Azure AD, należy dodać Expensify z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Expensify z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **Expensify**, wybierz opcję **Expensify** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Expensify na liście wyników](./media/expensify-tutorial/tutorial_expensify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Expensify w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Expensify do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Expensify musi można ustanowić.

W Expensify, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Expensify, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego Expensify](#create-an-expensify-test-user)**  — aby odpowiednikiem Britta Simon w Expensify połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Expensify.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Expensify, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Expensify** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/expensify-tutorial/tutorial_expensify_samlbase.png)

1. Na **Expensify domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Domena i adresy URL pojedynczego logowania jednokrotnego informacji expensify](./media/expensify-tutorial/tutorial_expensify_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL w postaci: `https://www.expensify.com/authentication/saml/login`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://www.<companyname>.expensify.com`

    > [!NOTE] 
    > Zastąp `<companyname>` sekcji adres URL identyfikatora z domeny Twojej firmy. Zobacz przykład `https://contoso.expensify.com` powyżej. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Expensify](mailto:help@expensify.com) aby zyskać tę wartość.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/expensify-tutorial/tutorial_expensify_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/expensify-tutorial/tutorial_general_400.png)

1. Aby włączyć logowanie Jednokrotne w Expensify, należy najpierw włączyć **kontroli domeny** w aplikacji. Kontrola domeny można włączyć w aplikacji za pomocą kroków wymienionych [tutaj](http://help.expensify.com/domain-control). Aby uzyskać dodatkową pomoc, pracować [zespołem pomocy technicznej klienta Expensify](mailto:help@expensify.com). Jeśli masz włączoną kontrolą domeny, wykonaj następujące kroki:
   
    ![Konfigurowanie logowania jednokrotnego](./media/expensify-tutorial/tutorial_expensify_51.png)
    
    a. Zaloguj się do aplikacji Expensify.
    
    b. Na pasku narzędzi u góry kliknij **administratora**.
    
    c. W okienku po lewej stronie kliknij **domeny**.
    
    d. Kliknij nazwę zweryfikowanej domeny.
    
    e. W okienku po lewej stronie kliknij **SAML**, a następnie wybierz pozycję **włączone**.
    
    f. Otwórz pobrany metadanych federacji z usługi Azure AD w programie Notatnik, skopiuj zawartość, a następnie wklej go do **metadanych dostawcy tożsamości** pola tekstowego.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/expensify-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/expensify-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/expensify-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/expensify-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-an-expensify-test-user"></a>Tworzenie użytkownika testowego Expensify

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Expensify. Praca z [zespołem pomocy technicznej klienta Expensify](mailto:help@expensify.com) Aby dodać użytkowników na platformie Expensify.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Expensify.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Expensify, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Expensify**.

    ![Link Expensify na liście aplikacji](./media/expensify-tutorial/tutorial_expensify_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Expensify w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Expensify.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/expensify-tutorial/tutorial_general_01.png
[2]: ./media/expensify-tutorial/tutorial_general_02.png
[3]: ./media/expensify-tutorial/tutorial_general_03.png
[4]: ./media/expensify-tutorial/tutorial_general_04.png

[100]: ./media/expensify-tutorial/tutorial_general_100.png

[200]: ./media/expensify-tutorial/tutorial_general_200.png
[201]: ./media/expensify-tutorial/tutorial_general_201.png
[202]: ./media/expensify-tutorial/tutorial_general_202.png
[203]: ./media/expensify-tutorial/tutorial_general_203.png

