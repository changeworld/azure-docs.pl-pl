---
title: 'Samouczek: Integracja usługi Azure Active Directory z LCVista | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i LCVista.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8db80d6e-3275-419f-aa39-6115a7bc9800
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: jeedes
ms.openlocfilehash: 1ec1783e6c9caabfbc5e03849b6d4c04b1f33d23
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448159"
---
# <a name="tutorial-azure-active-directory-integration-with-lcvista"></a>Samouczek: Integracja usługi Azure Active Directory z LCVista

W tym samouczku dowiesz się, jak zintegrować LCVista w usłudze Azure Active Directory (Azure AD).

Integrowanie LCVista z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do LCVista
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do LCVista (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą LCVista, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- LCVista logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie LCVista z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-lcvista-from-the-gallery"></a>Dodawanie LCVista z galerii
Aby skonfigurować integrację LCVista w usłudze Azure AD, należy dodać LCVista z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać LCVista z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **LCVista**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/lcvista-tutorial/tutorial_lcvista_search.png)

1. W panelu wyników wybierz **LCVista**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/lcvista-tutorial/tutorial_lcvista_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą LCVista w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w LCVista do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w LCVista musi można ustanowić.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w LCVista.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą LCVista, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego LCVista](#creating-a-lcvista-test-user)**  — aby odpowiednikiem Britta Simon w LCVista połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji LCVista.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z LCVista, wykonaj następujące czynności:**

1. W witrynie Azure portal na **LCVista** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Konfigurowanie logowania jednokrotnego](./media/lcvista-tutorial/tutorial_lcvista_samlbase.png)

1. Na **LCVista domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/lcvista-tutorial/tutorial_lcvista_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.lcvista.com/rainier/login`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.lcvista.com` 
     
    > [!NOTE] 
    > Te wartości nie są rzeczywiste. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adres URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta LCVista](https://lcvista.com/contact) do uzyskania tych wartości. 

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/lcvista-tutorial/tutorial_lcvista_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/lcvista-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji LCVista** , kliknij przycisk **skonfigurować LCVista** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML identyfikator jednostki** i **SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/lcvista-tutorial/tutorial_lcvista_configure.png) 

1.  Zaloguj się do aplikacji LCVista jako administrator.

1. W **konfiguracji SAML** sekcji wyboru **Włącz SAML logowania** i wprowadź szczegóły, zgodnie z poniższymi obrazu. 

    ![Konfigurowanie logowania jednokrotnego](./media/lcvista-tutorial/tutorial_lcvista_config.png)

    a. Wklej **adres URL wystawcy** skopiowanej z usługi Azure AD w **identyfikator jednostki** sekcji. 

    b. Wklej **pojedynczy znak na adres URL usługi** skopiowanej z usługi Azure AD w **adresu URL** sekcji.

    c. Z metadane (XML) który został pobrany z witryny Azure portal, skopiuj wartość **X509Certificate** i wklej go w **certyfikatu x509** sekcji.

    d. W **atrybut imię** pola tekstowego, Wklej wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. W **ostatniego atrybutu nazwy** pola tekstowego, Wklej wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. W **atrybut E-mail** pola tekstowego, Wklej wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. W **atrybut Username** pola tekstowego, Wklej wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

> [!TIP]
> Teraz mogą odczytywać zwięzłe wersji tych instrukcji wewnątrz [witryny Azure portal](https://portal.azure.com), podczas gdy konfigurujesz aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij pozycję **logowania jednokrotnego** karty i uzyskać dostęp do osadzonych dokumentacji za pośrednictwem  **Konfiguracja** sekcji u dołu. Możesz dowiedzieć się więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacja embedded usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/lcvista-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/lcvista-tutorial/create_aaduser_02.png) 

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/lcvista-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/lcvista-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="creating-a-lcvista-test-user"></a>Tworzenie użytkownika testowego LCVista

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w LCVista. Musisz skontaktować się z [zespołem pomocy technicznej klienta LCVista](https://lcvista.com/contact) Aby dodać użytkowników w aplikacji LCVista. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do LCVista.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon LCVista, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **LCVista**.

    ![Konfigurowanie logowania jednokrotnego](./media/lcvista-tutorial/tutorial_lcvista_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu. Kliknij Kafelek LCVista w panelu dostępu, nastąpi przekierowanie do podpisania organizacji na stronie. Po pomyślnym zalogowaniu można będzie mieć zalogowanych do aplikacji LCVista. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lcvista-tutorial/tutorial_general_01.png
[2]: ./media/lcvista-tutorial/tutorial_general_02.png
[3]: ./media/lcvista-tutorial/tutorial_general_03.png
[4]: ./media/lcvista-tutorial/tutorial_general_04.png

[100]: ./media/lcvista-tutorial/tutorial_general_100.png

[200]: ./media/lcvista-tutorial/tutorial_general_200.png
[201]: ./media/lcvista-tutorial/tutorial_general_201.png
[202]: ./media/lcvista-tutorial/tutorial_general_202.png
[203]: ./media/lcvista-tutorial/tutorial_general_203.png

