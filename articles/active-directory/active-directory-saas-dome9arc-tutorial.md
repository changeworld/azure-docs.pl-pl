---
title: 'Samouczek: Integracji Azure Active Directory z łuk Dome9 | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i łuk Dome9.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: de164f6a439fd68b4746dc96c43ff5cb0740ffe8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Samouczek: Integracji Azure Active Directory z Dome9 łuk

Z tego samouczka dowiesz integrowanie łuk Dome9 z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD łuk Dome9 zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do łuk Dome9.
- Umożliwia użytkownikom automatycznie pobrać zalogowane na łuk Dome9 (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z łuk Dome9, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Łuk Dome9 logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie łuk Dome9 z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-dome9-arc-from-the-gallery"></a>Dodawanie łuk Dome9 z galerii
Aby skonfigurować integrację usługi Azure AD łuk Dome9, należy dodać łuk Dome9 z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać łuk Dome9 z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **łuk Dome9**, wybierz pozycję **łuk Dome9** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Łuk Dome9 na liście wyników](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z łuk Dome9 w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w łuk Dome9 jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w Dome9 łuk musi określone.

Łuk Dome9 przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z łuk Dome9, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego łuk Dome9](#create-a-dome9-arc-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta łuk Dome9, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji łuk Dome9.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z łuk Dome9, wykonaj następujące czynności:**

1. W portalu Azure na **łuk Dome9** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

3. Na **Dome9 łuk domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny łuk Dome9 pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL: `https://secure.dome9.com/`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Możesz wybrać wartość nazwę firmy w portalu administracyjnym dome9 znajduje się w dalszej części tego samouczka.

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny łuk Dome9 pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://secure.dome9.com/sso/saml/<yourcompanyname>`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta łuk Dome9](https://dome9.com/about/contact-us/) uzyskać te wartości. 

5. Dome9 łuk aplikacji oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie rejestracji jednokrotnej attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_attribute.png)

6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu  | Wartość atrybutu | 
    | --------------- | --------------- | 
    | memberof | User.assignedroles | 
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej Dodaj attb](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9_04.png)

    ![Konfigurowanie rejestracji jednokrotnej attb edycji](./media/active-directory-saas-dome9arc-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **OK**.

7. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

8. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-dome9arc-tutorial/tutorial_general_400.png)
    
9. Na **konfiguracji łuk Dome9** , kliknij przycisk **skonfigurować łuk Dome9** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Dome9 łuk konfiguracji](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_configure.png) 

10. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy łuk Dome9 jako administrator.

11. Polecenie **ustawienia profilu** w prawym górnym rogu, a następnie kliknij przycisk **ustawienia konta**. 

    ![Dome9 łuk konfiguracji](./media/active-directory-saas-dome9arc-tutorial/configure1.png)

12. Przejdź do **logowania jednokrotnego** , a następnie kliknij przycisk **włączyć**.

    ![Dome9 łuk konfiguracji](./media/active-directory-saas-dome9arc-tutorial/configure2.png)

13. W sekcji konfiguracji logowania jednokrotnego wykonaj następujące czynności:

    ![Dome9 łuk konfiguracji](./media/active-directory-saas-dome9arc-tutorial/configure3.png)

    a. Wprowadź nazwę firmy w **Identyfikatora** pola tekstowego. Ta wartość jest do użycia w odpowiedzi na adres url wymienione w sekcji adresu URL portalu Azure.

    b. W **wystawcy** pole tekstowe, Wklej wartość **identyfikator jednostki SAML**, która została skopiowana formularza portalu Azure.

    c. W **adres url punktu końcowego Idp** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, która została skopiowana formularza portalu Azure.

    d. Otwórz w Notatniku z pobranego certyfikatu szyfrowania Base64, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu X.509** pola tekstowego.

    e. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-dome9-arc-test-user"></a>Tworzenie użytkownika testowego Dome9 łuk

Aby włączyć użytkowników usługi Azure AD zalogować się na łuk Dome9, musi być przygotowana do aplikacji. Łuk Dome9 obsługę w czasie, ale w tym do poprawnego użytkownik musiał wybrać konkretnego **roli** i przypisać je do użytkownika.

   >[!Note] 
   >Dla **roli** tworzenia i inne szczegóły kontaktu [zespołem pomocy technicznej klienta łuk Dome9](https://dome9.com/about/contact-us/).

**Aby ręcznie zainicjować obsługę konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy łuk Dome9 jako administrator.

2. Polecenie **użytkownicy i role** , a następnie kliknij przycisk **użytkowników**.

    ![Dodawanie pracownika](./media/active-directory-saas-dome9arc-tutorial/user1.png)

3. Kliknij przycisk **Dodaj użytkownika**.

    ![Dodawanie pracownika](./media/active-directory-saas-dome9arc-tutorial/user2.png)

4. W **Tworzenie użytkownika** sekcji, wykonaj następujące czynności:
    
    ![Dodawanie pracownika](./media/active-directory-saas-dome9arc-tutorial/user3.png)

    a. W **E-mail** tekstowym, wpisz adres e-mail użytkownika, takich jak Brittasimon@contoso.com.

    b. W **imię** tekstowym, wpisz imię użytkownika, takich jak Britta.

    c. W **nazwisko** tekstowym, wpisz nazwisko użytkownika, takich jak Simona.

    d. Wprowadź **użytkownika logowania jednokrotnego** jako **na**.

    e. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu na łuk Dome9.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta łuk Dome9, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **łuk Dome9**.

    ![Łącze łuk Dome9 na liście aplikacji](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka łuk Dome9 w panelu dostępu należy należy pobrać automatycznie zalogowane łuk Dome9 aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_203.png

