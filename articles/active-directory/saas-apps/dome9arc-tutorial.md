---
title: 'Samouczek: Integracja usługi Azure Active Directory z łuk Dome9 | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Dome9 łuku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2018
ms.author: jeedes
ms.openlocfilehash: 934520764749b5abce9aefe22b8eb9a5d8e490f2
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746495"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Samouczek: Integracja usługi Azure Active Directory z Dome9 łuk

W tym samouczku dowiesz się, jak zintegrować Dome9 łuku z usługą Azure Active Directory (Azure AD).

Integrowanie Dome9 łuku z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Dome9 łuku.
- Użytkowników, aby automatycznie uzyskać zalogowanych na łuk Dome9 (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z łuk Dome9, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Łuk Dome9 logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Dome9 łuku z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-dome9-arc-from-the-gallery"></a>Dodawanie Dome9 łuku z galerii

Aby skonfigurować integrację łuk Dome9 w usłudze Azure AD, należy dodać Dome9 łuku z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Dome9 łuku z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **łuk Dome9**, wybierz opcję **łuk Dome9** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Dome9 łuk na liście wyników](./media/dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą łuk Dome9 w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w łuk Dome9 do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Dome9 łuk musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą łuk Dome9, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego łuk Dome9](#create-a-dome9-arc-test-user)**  — aby odpowiednikiem Britta Simon w łuk Dome9, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji Włączanie usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Dome9 łuku.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z łuk Dome9, wykonaj następujące czynności:**

1. W witrynie Azure portal na **łuk Dome9** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

3. Na **Dome9 łuk domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Dome9 łuk domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL: `https://secure.dome9.com/`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Możesz wybrać wartość nazwy firmy w portalu administracyjnym dome9 zostało wyjaśnione w dalszej części tego samouczka.

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Dome9 łuk domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/dome9arc-tutorial/tutorial_dome9arc_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://secure.dome9.com/sso/saml/<yourcompanyname>`
 
    > [!NOTE] 
    > Te wartości są prawdziwe. Rzeczywisty adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta łuk Dome9](https://dome9.com/about/contact-us/) do uzyskania tych wartości. 

5. Aplikacja oprogramowania łuk Dome9 oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie logowania jednokrotnego attb](./media/dome9arc-tutorial/tutorial_dome9arc_attribute.png)

6. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu  | Wartość atrybutu | 
    | --------------- | --------------- | 
    | memberof | User.assignedroles |
    
    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego Dodaj attb](./media/dome9arc-tutorial/tutorial_dome9_04.png)

    ![Konfigurowanie logowania jednokrotnego attb edycji](./media/dome9arc-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. Kliknij przycisk **OK**.
    
    > [!NOTE]
    > Skontaktuj się z tym [łącze](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-enterprise-app-role-management) na temat sposobu konfigurowania i konfigurowanie ról dla aplikacji.

7. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

8. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/dome9arc-tutorial/tutorial_general_400.png)

9. Na **konfiguracji łuk Dome9** , kliknij przycisk **skonfigurować łuk Dome9** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfiguracja łuk Dome9](./media/dome9arc-tutorial/tutorial_dome9arc_configure.png) 

10. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy łuk Dome9, jako administrator.

11. Kliknij pozycję **ustawienia profilu** w prawym górnym rogu, a następnie kliknij przycisk **ustawienia konta**. 

    ![Konfiguracja łuk Dome9](./media/dome9arc-tutorial/configure1.png)

12. Przejdź do **logowania jednokrotnego** a następnie kliknij przycisk **Włącz**.

    ![Konfiguracja łuk Dome9](./media/dome9arc-tutorial/configure2.png)

13. W sekcji konfiguracji logowania jednokrotnego wykonaj następujące czynności:

    ![Konfiguracja łuk Dome9](./media/dome9arc-tutorial/configure3.png)

    a. Wprowadź nazwę firmy w **Accountid** pola tekstowego. Ta wartość jest do użycia w odpowiedzi na adres url wymienione w sekcji Azure adres URL portalu.

    b. W **wystawcy** pola tekstowego, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane witryny Azure Portal.

    c. W **adresu url punktu końcowego protokołu Idp** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, które zostały skopiowane witryny Azure Portal.

    d. Otwórz pobrany certyfikat zakodowany w formacie Base64 w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikat X.509** pola tekstowego.

    e. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/dome9arc-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/dome9arc-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/dome9arc-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/dome9arc-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-a-dome9-arc-test-user"></a>Tworzenie użytkownika testowego Dome9 łuk

Aby umożliwić użytkownikom usługi Azure AD zalogować się na łuk Dome9, musi być obsługiwana w aplikacji. Łuk Dome9 obsługę just-in-time, ale w tym działało poprawnie, użytkownik musi wybrać określonego **roli** i Przypisz te same użytkownikowi.

   >[!Note]
   >Dla **roli** tworzenia i inne szczegóły kontaktu [zespołem pomocy technicznej klienta łuk Dome9](https://dome9.com/about/contact-us/).

**Aby ręcznie zainicjować obsługę konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy łuk Dome9 jako administrator.

2. Kliknij pozycję **użytkownicy i role** a następnie kliknij przycisk **użytkowników**.

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user1.png)

3. Kliknij przycisk **Dodaj użytkownika**.

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user2.png)

4. W **Create User** sekcji, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/dome9arc-tutorial/user3.png)

    a. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak Brittasimon@contoso.com.

    b. W **imię** polu tekstowym wpisz imię użytkownika, takich jak Britta.

    c. W **nazwisko** pole tekstowe, wpisz nazwisko użytkownika, takich jak Simon.

    d. Wprowadź **użytkownika logowania jednokrotnego** jako **na**.

    e. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Dome9 łuku.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon łuk Dome9, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **łuk Dome9**.

    ![Link Dome9 łuk na liście aplikacji](./media/dome9arc-tutorial/tutorial_dome9arc_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka łuk Dome9 w panelu dostępu, możesz należy pobrać automatycznie zalogowanych do aplikacji Dome9 łuku.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/dome9arc-tutorial/tutorial_general_203.png

