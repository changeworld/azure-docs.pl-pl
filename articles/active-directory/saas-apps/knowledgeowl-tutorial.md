---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą KnowledgeOwl | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8c86ad6e4b11f21c648083fac35e15eec7658c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60262205"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą KnowledgeOwl

W tym samouczku dowiesz się, jak zintegrować KnowledgeOwl w usłudze Azure Active Directory (Azure AD).

Integrowanie KnowledgeOwl z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do KnowledgeOwl.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do KnowledgeOwl (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą KnowledgeOwl, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- KnowledgeOwl logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie KnowledgeOwl z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-knowledgeowl-from-the-gallery"></a>Dodawanie KnowledgeOwl z galerii
Aby skonfigurować integrację KnowledgeOwl w usłudze Azure AD, należy dodać KnowledgeOwl z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać KnowledgeOwl z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
1. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **KnowledgeOwl**, wybierz opcję **KnowledgeOwl** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![KnowledgeOwl na liście wyników](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą KnowledgeOwl w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w KnowledgeOwl do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w KnowledgeOwl musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą KnowledgeOwl, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego KnowledgeOwl](#create-a-knowledgeowl-test-user)**  — aby odpowiednikiem Britta Simon w KnowledgeOwl połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji KnowledgeOwl.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z KnowledgeOwl, wykonaj następujące czynności:**

1. W witrynie Azure portal na **KnowledgeOwl** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

1. Na **KnowledgeOwl domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![KnowledgeOwl domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, korzystając z następującego wzorca:

    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca:

    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![KnowledgeOwl domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca:
    
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy zaktualizować te wartości z rzeczywistego identyfikatora, adres URL odpowiedzi i URL logowania jednokrotnego, co zostało wyjaśnione w dalszej części tego samouczka.

1. Aplikacja KnowledgeOwl oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji.

    ![Konfigurowanie logowania jednokrotnego](./media/knowledgeowl-tutorial/attribute.png)

1. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, pokazany na wcześniejszej ilustracji, używając i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu | Przestrzeń nazw|
    | ------------------- | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.
    
    ![Konfigurowanie logowania jednokrotnego](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d.From **Namespace** listy, wprowadź wartość przestrzeni nazw wyświetlanego dla tego wiersza.
    
    e. Kliknij przycisk **OK**.

1. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Raw)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
1. Na **konfiguracji KnowledgeOwl** , kliknij przycisk **skonfigurować KnowledgeOwl** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy KnowledgeOwl, jako administrator.

1. Kliknij pozycję **ustawienia** , a następnie wybierz **zabezpieczeń**.

     ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

1. Przewiń do **integracja logowania jednokrotnego SAML** i wykonaj następujące czynności:
    
     ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

     a. Wybierz **włączenia funkcji logowania jednokrotnego SAML**.

     b. Kopiuj **identyfikator jednostki SP** wartość i wklej go w **identyfikator jednostki** w **KnowledgeOwl domena i adresy URL** sekcji w witrynie Azure portal.

     c. Kopiuj **adres URL logowania SP** wartość i wklej go w **adres URL logowania i adres URL odpowiedzi** pola tekstowe w **KnowledgeOwl domena i adresy URL** sekcji w witrynie Azure portal.

     d. W **entityID tożsamości** pola tekstowego, Wklej **identyfikator jednostki SAML** wartości, które zostały skopiowane z witryny Azure portal.

     e. W **adres URL logowania dostawcy tożsamości** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** wartości, które zostały skopiowane z witryny Azure portal.

     f. W **adres URL wylogowania dostawcy tożsamości** pola tekstowego, Wklej **adres URL wylogowania** wartości, które zostały skopiowane z witryny Azure portal

     g. Przekazywanie formularza pobranego certyfikatu witryny Azure portal, klikając **Przekaż certyfikat tożsamości**.

     h. Kliknij pozycję **atrybutów SAML mapy** do mapowania atrybutów, a następnie wykonaj następujące czynności:
    
     ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` do **identyfikator logowania jednokrotnego** textbox
    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` do **nazwy użytkownika/Poczta E-mail** pola tekstowego.
    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` do **imię** pola tekstowego.
    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` do **nazwisko** pola tekstowego.
    * Kliknij pozycję **Zapisz**

      i. Kliknij przycisk **Zapisz** w dolnej części strony.

      ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/knowledgeowl-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/knowledgeowl-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/knowledgeowl-tutorial/create_aaduser_03.png)

1. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/knowledgeowl-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
 
### <a name="create-a-knowledgeowl-test-user"></a>Tworzenie użytkownika testowego KnowledgeOwl

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w KnowledgeOwl. KnowledgeOwl obsługę just-in-time, który jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu KnowledgeOwl, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do KnowledgeOwl.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon KnowledgeOwl, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **KnowledgeOwl**.

    ![Link KnowledgeOwl na liście aplikacji](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka KnowledgeOwl w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji KnowledgeOwl.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/knowledgeowl-tutorial/tutorial_general_203.png

