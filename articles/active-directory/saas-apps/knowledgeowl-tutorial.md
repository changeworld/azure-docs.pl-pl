---
title: 'Samouczek: Integracja usługi Azure Active Directory z KnowledgeOwl | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: e4d86fc2e29c4d93567bedd045d57cd8d4e22cf5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39052438"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Samouczek: Integracja usługi Azure Active Directory z KnowledgeOwl

W tym samouczku dowiesz się, jak zintegrować KnowledgeOwl w usłudze Azure Active Directory (Azure AD).

Integrowanie KnowledgeOwl z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do KnowledgeOwl.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do KnowledgeOwl (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą KnowledgeOwl, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- KnowledgeOwl logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie KnowledgeOwl z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-knowledgeowl-from-the-gallery"></a>Dodawanie KnowledgeOwl z galerii
Aby skonfigurować integrację KnowledgeOwl w usłudze Azure AD, należy dodać KnowledgeOwl z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać KnowledgeOwl z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **KnowledgeOwl**, wybierz opcję **KnowledgeOwl** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![KnowledgeOwl na liście wyników](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą KnowledgeOwl w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w KnowledgeOwl do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w KnowledgeOwl musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą KnowledgeOwl, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego KnowledgeOwl](#create-a-knowledgeowl-test-user)**  — aby odpowiednikiem Britta Simon w KnowledgeOwl połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji KnowledgeOwl.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z KnowledgeOwl, wykonaj następujące czynności:**

1. W witrynie Azure portal na **KnowledgeOwl** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

3. Na **KnowledgeOwl domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![KnowledgeOwl domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. W **identyfikator jednostki** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![KnowledgeOwl domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca:
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
    > Te wartości są prawdziwe. Należy zaktualizować te wartości z rzeczywistego identyfikatora, adres URL odpowiedzi i URL logowania jednokrotnego, co zostało wyjaśnione w dalszej części tego samouczka.

5. Aplikacja KnowledgeOwl oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji.

    ![Konfigurowanie logowania jednokrotnego](./media/knowledgeowl-tutorial/attribute.png)

6. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, pokazany na wcześniejszej ilustracji, używając i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu | Przestrzeń nazw|
    | ------------------- | -------------------- | -----|
    | ssoid | User.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.
    
    ![Konfigurowanie logowania jednokrotnego](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.
    
    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d.From **Namespace** listy, wprowadź wartość przestrzeni nazw wyświetlanego dla tego wiersza.
    
    e. Kliknij przycisk **OK**.

7. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Raw)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

8. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
9. Na **konfiguracji KnowledgeOwl** , kliknij przycisk **skonfigurować KnowledgeOwl** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

10. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy KnowledgeOwl, jako administrator.

11. Kliknij pozycję **ustawienia** , a następnie wybierz **zabezpieczeń**.

    ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

12. Przewiń w dół maksymalnie **integracja logowania jednokrotnego SAML** i wykonaj następujące czynności:
    
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

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/knowledgeowl-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/knowledgeowl-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/knowledgeowl-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-knowledgeowl-test-user"></a>Tworzenie użytkownika testowego KnowledgeOwl

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w KnowledgeOwl. KnowledgeOwl obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu KnowledgeOwl, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do KnowledgeOwl.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon KnowledgeOwl, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **KnowledgeOwl**.

    ![Link KnowledgeOwl na liście aplikacji](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka KnowledgeOwl w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji KnowledgeOwl.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

