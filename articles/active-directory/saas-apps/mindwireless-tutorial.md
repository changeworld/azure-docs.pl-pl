---
title: 'Samouczek: Integracja usługi Azure Active Directory z mindWireless | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i mindWireless.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bd00a339-27c9-4904-b66f-a95bf597ac3c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeedes
ms.openlocfilehash: 6c6fe0a720795c67a7062f5a5971c699472fca07
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434350"
---
# <a name="tutorial-azure-active-directory-integration-with-mindwireless"></a>Samouczek: Integracja usługi Azure Active Directory z mindWireless

W tym samouczku dowiesz się, jak zintegrować mindWireless w usłudze Azure Active Directory (Azure AD).

Integrowanie mindWireless z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do mindWireless.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do mindWireless (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą mindWireless, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- MindWireless logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie mindWireless z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-mindwireless-from-the-gallery"></a>Dodawanie mindWireless z galerii
Aby skonfigurować integrację mindWireless w usłudze Azure AD, należy dodać mindWireless z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać mindWireless z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

1. W polu wyszukiwania wpisz **mindWireless**, wybierz opcję **mindWireless** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![mindWireless na liście wyników](./media/mindwireless-tutorial/tutorial_mindwireless_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą mindWireless w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w mindWireless do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w mindWireless musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą mindWireless, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego mindWireless](#create-a-mindwireless-test-user)**  — aby mają odpowiednika w pozycji Britta simon w mindWireless połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji mindWireless.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z mindWireless, wykonaj następujące czynności:**

1. W witrynie Azure portal na **mindWireless** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/mindwireless-tutorial/tutorial_mindwireless_samlbase.png)

1. Na **mindWireless domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![mindWireless domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/mindwireless-tutorial/tutorial_mindwireless_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.mwsmart.com/`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<subdomain>.mwsmart.com/SAML/AssertionConsumerService.aspx`

    > [!NOTE] 
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adres URL odpowiedzi. Skontaktuj się z pomocą [zespołu pomocy technicznej mindWireless](mailto:sdulloor@mindwireless.com) do uzyskania tych wartości.

1. Aplikacja mindWireless oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML.

1. Poniższy zrzut ekranu przedstawia przykład dla niego. Nazwa oświadczeń zawsze być **identyfikator pracownika** i wartości, które firma Microsoft ma zamapowany na user.employeeid, który zawiera EmployeeID użytkownika. W tym miejscu mapowanie użytkownika z usługi Azure AD do mindWireless odbywa się z identyfikatorem EmployeeID, ale można go mapować na inną wartość również oparte na ustawieniach aplikacji. Możesz pracować z [zespołu pomocy technicznej mindWireless](mailto:sdulloor@mindwireless.com) najpierw na prawidłowy identyfikator użytkownika i zmapować tę wartość z **identyfikator pracownika** oświadczenia.

    ![Konfigurowanie logowania jednokrotnego](./media/mindwireless-tutorial/tutorial_attribute.png)

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, pokazany na wcześniejszej ilustracji, używając i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu | Wartość Namespace |
    | -------------- | --------------- | ----------------|
    | Identyfikator pracownika | user.employeeid | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/mindwireless-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/mindwireless-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. W **Namespace** polu tekstowym wpisz wartość przestrzeni nazw wyświetlanego dla tego wiersza.
    
    e. Kliknij przycisk **OK**.
    
1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/mindwireless-tutorial/tutorial_mindwireless_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/mindwireless-tutorial/tutorial_general_400.png)

1. Na **mindWireless konfiguracji** kliknij **skonfigurować mindWireless** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![mindWireless konfiguracji](./media/mindwireless-tutorial/tutorial_mindwireless_configure.png) 

1. Aby skonfigurować logowanie jednokrotne na **mindWireless** stronie, musisz wysłać pobrany **Certificate(Base64), SAML pojedynczego logowania jednokrotnego usługi adresu URL**, i **identyfikator jednostki SAML** do [zespołu pomocy technicznej mindWireless](mailto:sdulloor@mindwireless.com). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/mindwireless-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/mindwireless-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/mindwireless-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/mindwireless-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-a-mindwireless-test-user"></a>Tworzenie użytkownika testowego mindWireless

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w mindWireless. Praca z [zespołu pomocy technicznej mindWireless](mailto:sdulloor@mindwireless.com) Aby dodać użytkowników na platformie mindWireless. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do mindWireless.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon mindWireless, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **mindWireless**.

    ![Link mindWireless na liście aplikacji](./media/mindwireless-tutorial/tutorial_mindwireless_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka mindWireless w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji mindWireless.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mindwireless-tutorial/tutorial_general_01.png
[2]: ./media/mindwireless-tutorial/tutorial_general_02.png
[3]: ./media/mindwireless-tutorial/tutorial_general_03.png
[4]: ./media/mindwireless-tutorial/tutorial_general_04.png

[100]: ./media/mindwireless-tutorial/tutorial_general_100.png

[200]: ./media/mindwireless-tutorial/tutorial_general_200.png
[201]: ./media/mindwireless-tutorial/tutorial_general_201.png
[202]: ./media/mindwireless-tutorial/tutorial_general_202.png
[203]: ./media/mindwireless-tutorial/tutorial_general_203.png

