---
title: 'Samouczek: Integracja usługi Azure Active Directory z ScreenSteps | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 105ec895635a882d562de48203222702a2c6bfed
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421750"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Samouczek: Integracja usługi Azure Active Directory z ScreenSteps

W tym samouczku dowiesz się, jak zintegrować ScreenSteps w usłudze Azure Active Directory (Azure AD).

Integrowanie ScreenSteps z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do ScreenSteps.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do ScreenSteps (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą ScreenSteps, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- ScreenSteps logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie ScreenSteps z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-screensteps-from-the-gallery"></a>Dodawanie ScreenSteps z galerii
Aby skonfigurować integrację ScreenSteps w usłudze Azure AD, należy dodać ScreenSteps z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać ScreenSteps z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **ScreenSteps**, wybierz opcję **ScreenSteps** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![ScreenSteps na liście wyników](./media/screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą ScreenSteps w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w ScreenSteps do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w ScreenSteps musi można ustanowić.

W ScreenSteps, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą ScreenSteps, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego ScreenSteps](#create-a-screensteps-test-user)**  — aby odpowiednikiem Britta Simon w ScreenSteps połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji ScreenSteps.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z ScreenSteps, wykonaj następujące czynności:**

1. W witrynie Azure portal na **ScreenSteps** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/screensteps-tutorial/tutorial_screensteps_samlbase.png)

1. Na **ScreenSteps domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![ScreenSteps domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/screensteps-tutorial/tutorial_screensteps_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy użyciu rzeczywistego logowania jednokrotnego adresu URL, który zostało wyjaśnione w dalszej części tego samouczka. 

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/screensteps-tutorial/tutorial_screensteps_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/screensteps-tutorial/tutorial_general_400.png)

1. Na **konfiguracji ScreenSteps** , kliknij przycisk **skonfigurować ScreenSteps** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja ScreenSteps](./media/screensteps-tutorial/tutorial_screensteps_configure.png) 

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy ScreenSteps, jako administrator.

1. Kliknij przycisk **ustawienia konta**.

    ![Zarządzanie kontami](./media/screensteps-tutorial/ic778523.png "Zarządzanie kontami")

1. Kliknij przycisk **logowanie jednokrotne**.

    ![Zdalne uwierzytelnianie](./media/screensteps-tutorial/ic778524.png "uwierzytelniania zdalnego")

1. Kliknij przycisk **utworzyć pojedynczy punkt końcowy logowania**.

    ![Zdalne uwierzytelnianie](./media/screensteps-tutorial/ic778525.png "uwierzytelniania zdalnego")

1. W **utworzyć pojedynczy punkt końcowy logowania** sekcji, wykonaj następujące czynności:

    ![Tworzenie punktu końcowego uwierzytelniania](./media/screensteps-tutorial/ic778526.png "utworzenie punktu końcowego uwierzytelniania")
    
    a. W **tytuł** pole tekstowe, wpisz tytuł.
    
    b. Z **tryb** listy wybierz **SAML**.
    
    c. Kliknij pozycję **Utwórz**.

1. **Edytuj** nowy punkt końcowy.

    ![Edytuj punkt końcowy](./media/screensteps-tutorial/ic778528.png "Edytuj punkt końcowy")

1. W **edytować pojedynczy punkt końcowy logowania** sekcji, wykonaj następujące czynności:

    ![Punkt końcowy uwierzytelniania zdalnego](./media/screensteps-tutorial/ic778527.png "uwierzytelniania zdalnego punktu końcowego")

    a. Kliknij przycisk **Przekaż nowy plik certyfikatu SAML**, a następnie przekaż certyfikat, który został pobrany z witryny Azure portal.
    
    b. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartości, które zostały skopiowane z witryny Azure portal do **zdalnego adresu URL logowania** pola tekstowego.
    
    c. Wklej **adres URL wylogowania** wartości, które zostały skopiowane z witryny Azure portal do **adres URL wylogowania** pola tekstowego.
    
    d. Wybierz **grupy** do przypisywania użytkowników do po ich udostępnieniu.
    
    e. Kliknij przycisk **aktualizacji**.

    f. Kopiuj **adres URL klienta SAML** do Schowka i Wklej do **adres URL logowania** polu tekstowym w **ScreenSteps domena i adresy URL** sekcji.
    
    g. Wróć do **edytować pojedynczy punkt końcowy logowania**.
    
    h. Kliknij przycisk **Ustaw jako domyślny dla konta** przycisk, aby użyć tego punktu końcowego dla wszystkich użytkowników logujących się na ScreenSteps. Alternatywnie możesz kliknąć pozycję **dodać do witryny** przycisk, aby użyć tego punktu końcowego dla określonych witryn internetowych w **ScreenSteps**.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/screensteps-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/screensteps-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/screensteps-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/screensteps-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-screensteps-test-user"></a>Tworzenie użytkownika testowego ScreenSteps

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w ScreenSteps. Praca z [zespołem pomocy technicznej klienta ScreenSteps](https://www.screensteps.com/contact) Aby dodać użytkowników na platformie ScreenSteps. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do ScreenSteps.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon ScreenSteps, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **ScreenSteps**.

    ![Link ScreenSteps na liście aplikacji](./media/screensteps-tutorial/tutorial_screensteps_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka ScreenSteps w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji ScreenSteps.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/screensteps-tutorial/tutorial_general_01.png
[2]: ./media/screensteps-tutorial/tutorial_general_02.png
[3]: ./media/screensteps-tutorial/tutorial_general_03.png
[4]: ./media/screensteps-tutorial/tutorial_general_04.png

[100]: ./media/screensteps-tutorial/tutorial_general_100.png

[200]: ./media/screensteps-tutorial/tutorial_general_200.png
[201]: ./media/screensteps-tutorial/tutorial_general_201.png
[202]: ./media/screensteps-tutorial/tutorial_general_202.png
[203]: ./media/screensteps-tutorial/tutorial_general_203.png

