---
title: 'Samouczek: Integracji Azure Active Directory z KnowledgeOwl | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i KnowledgeOwl.
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
ms.openlocfilehash: 95e8199ab57bcc5ba617403f1551da21a7625885
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221414"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Samouczek: Integracji Azure Active Directory z KnowledgeOwl

Z tego samouczka dowiesz się integrowanie KnowledgeOwl z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD KnowledgeOwl zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do KnowledgeOwl.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do KnowledgeOwl (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z KnowledgeOwl, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- KnowledgeOwl logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie KnowledgeOwl z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-knowledgeowl-from-the-gallery"></a>Dodawanie KnowledgeOwl z galerii
Aby skonfigurować integrację usługi Azure AD KnowledgeOwl, należy dodać KnowledgeOwl z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać KnowledgeOwl z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **KnowledgeOwl**, wybierz pozycję **KnowledgeOwl** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![KnowledgeOwl na liście wyników](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z KnowledgeOwl w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w KnowledgeOwl jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w KnowledgeOwl musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z KnowledgeOwl, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego KnowledgeOwl](#create-a-knowledgeowl-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta KnowledgeOwl połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji KnowledgeOwl.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z KnowledgeOwl, wykonaj następujące czynności:**

1. W portalu Azure na **KnowledgeOwl** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

3. Na **KnowledgeOwl domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny KnowledgeOwl pojedynczy informacje logowania jednokrotnego](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. W **identyfikator jednostki** tekstowym, wpisz adres URL, używając następującego wzorca:
    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny KnowledgeOwl pojedynczy informacje logowania jednokrotnego](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca:
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
    > Wartości te nie są prawdziwe. Musisz zaktualizować te wartości z rzeczywistego identyfikatora, adres URL odpowiedzi służący i URL logowania jednokrotnego, który znajduje się w dalszej części tego samouczka.

5. Aplikacja KnowledgeOwl oczekuje potwierdzenia języka SAML w określonym formacie, musisz dodać mapowania atrybutu niestandardowego do konfiguracji atrybuty tokenu SAML. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji.

    ![Konfigurowanie rejestracji jednokrotnej](./media/knowledgeowl-tutorial/attribute.png)

6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na powyższej ilustracji i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu | Przestrzeń nazw|
    | ------------------- | -------------------- | -----|
    | ssoid | User.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.
    
    ![Konfigurowanie rejestracji jednokrotnej](./media/knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej](./media/knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.
    
    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d.From **Namespace** listy, wprowadź wartość przestrzeni nazw wyświetlany dla danego wiersza.
    
    e. Kliknij przycisk **OK**.

7. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Raw)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

8. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/knowledgeowl-tutorial/tutorial_general_400.png)
    
9. Na **konfiguracji KnowledgeOwl** , kliknij przycisk **skonfigurować KnowledgeOwl** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

10. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy KnowledgeOwl jako administrator.

11. Polecenie **ustawienia** , a następnie wybierz **zabezpieczeń**.

    ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/configure1.png)

12. Przewiń w dół maksymalnie **integracji logowania jednokrotnego SAML** i wykonaj następujące czynności:
    
    ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/configure2.png)

    a. Wybierz **włączenia funkcji logowania jednokrotnego SAML**.

    b. Kopiuj **SP identyfikator jednostki** i wklej go do **identyfikator jednostki** w **KnowledgeOwl domeny i adres URL** sekcji w portalu Azure.

    c. Kopia **adres URL logowania SP** i wklej go do **adres URL logowania i adres URL odpowiedzi** pól tekstowych w **KnowledgeOwl domeny i adres URL** sekcji w portalu Azure.

    d. W **IdP entityID** pole tekstowe, Wklej **identyfikator jednostki SAML** wartość, która została skopiowana z portalu Azure.

    e. W **adres URL logowania IdP** pole tekstowe, Wklej **SAML pojedynczy znak na adres URL usługi** wartość, która została skopiowana z portalu Azure.

    f. W **adresu URL wylogowania IdP** pole tekstowe, Wklej **Sign-Out URL** wartość, która została skopiowana z portalu Azure

    g. Przekazywanie formularza pobranego certyfikatu portalu Azure, klikając **Przekaż certyfikat IdP**.

    h. Polecenie **mapy SAML atrybuty** Mapowanie atrybutów i wykonaj następujące czynności:
    
    ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/configure3.png)

    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` do **identyfikator logowania jednokrotnego** pole tekstowe
    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` do **nazwy użytkownika/Poczta E-mail** pola tekstowego.
    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` do **imię** pola tekstowego.
    * Wprowadź `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` do **nazwisko** pola tekstowego.
    * Kliknij pozycję **Zapisz**

    i. Kliknij przycisk **Zapisz** w dolnej części strony.

    ![Konfiguracja KnowledgeOwl](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/knowledgeowl-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/knowledgeowl-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/knowledgeowl-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/knowledgeowl-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-knowledgeowl-test-user"></a>Tworzenie użytkownika testowego KnowledgeOwl

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w KnowledgeOwl. KnowledgeOwl obsługę w czasie, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu KnowledgeOwl, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu KnowledgeOwl.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta KnowledgeOwl, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **KnowledgeOwl**.

    ![Łącze KnowledgeOwl na liście aplikacji](./media/knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka KnowledgeOwl w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji KnowledgeOwl.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
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

