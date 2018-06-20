---
title: 'Samouczek: Integracji Azure Active Directory z punktu widzenia użytkownika Stany Zjednoczone (Non-UltiPro) | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Stanów Zjednoczonych z punktu widzenia użytkownika (Non-UltiPro).
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
ms.openlocfilehash: e4f0fe0399b1230a27460df1718223736176ff01
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228224"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Samouczek: Integracji Azure Active Directory z punktu widzenia użytkownika Stany Zjednoczone (Non-UltiPro)

Z tego samouczka dowiesz się sposobu integracji z punktu widzenia użytkownika Stany Zjednoczone (Non-UltiPro) z usługi Azure Active Directory (Azure AD).

Integrowanie z punktu widzenia użytkownika Stany Zjednoczone (Non-UltiPro) z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Stanów Zjednoczonych z punktu widzenia użytkownika (Non-UltiPro).
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Stanów Zjednoczonych z punktu widzenia użytkownika (Non-UltiPro) (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z punktu widzenia użytkownika Stany Zjednoczone (inne niż UltiPro), potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Z punktu widzenia użytkownika Stany Zjednoczone (Non-UltiPro) logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie wrażenie Stany Zjednoczone (Non-UltiPro) z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Dodawanie wrażenie Stany Zjednoczone (Non-UltiPro) z galerii
Aby skonfigurować integrację z punktu widzenia użytkownika Stany Zjednoczone (Non-UltiPro) do usługi Azure AD, należy dodać wrażenie Stany Zjednoczone (Non-UltiPro) z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać wrażenie Stany Zjednoczone (Non-UltiPro) z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **wrażenie Stany Zjednoczone (Non-UltiPro)**, wybierz pozycję **wrażenie Stany Zjednoczone (Non-UltiPro)** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Z punktu widzenia użytkownika Stany Zjednoczone (Non-UltiPro) na liście wyników](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z punktu widzenia użytkownika Stanów Zjednoczonych (Non-UltiPro) w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Stanach Zjednoczonych z punktu widzenia użytkownika (Non-UltiPro) jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Stanach Zjednoczonych z punktu widzenia użytkownika (Non-UltiPro).

W wrażenie Stanów Zjednoczonych (inne niż UltiPro), przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z punktu widzenia użytkownika Stany Zjednoczone (inne niż UltiPro), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego z punktu widzenia użytkownika Stany Zjednoczone (Non-UltiPro)](#create-a-perception-united-states-non-ultipro-test-user)**  — aby odpowiednikiem Simona Britta w wrażenie Stanów Zjednoczonych (Non-UltiPro) połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji z punktu widzenia użytkownika Stany Zjednoczone (Non-UltiPro).

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z punktu widzenia użytkownika Stany Zjednoczone (inne niż UltiPro), wykonaj następujące czynności:**

1. W portalu Azure na **wrażenie Stany Zjednoczone (Non-UltiPro)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

3. Na **domeny wrażenie Stany Zjednoczone (Non-UltiPro) i adresy URL** sekcji, wykonaj następujące czynności:

    ![Z punktu widzenia użytkownika domeny Stanów Zjednoczonych (Non-UltiPro) i adres URL z jednym informacje logowania jednokrotnego](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL: `https://perception.kanjoya.com/sp`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    > [!NOTE] 
    > Wartość nie jest prawdziwe. Wartość zaktualizuje rzeczywiste odpowiedzi adres URL, który znajduje się w dalszej części tego samouczka.
 
4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/perceptionunitedstates-tutorial/tutorial_general_400.png)

6. Na **konfiguracji wrażenie Stany Zjednoczone (Non-UltiPro)** , kliknij przycisk **skonfigurować wrażenie Stany Zjednoczone (Non-UltiPro)** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML** z **sekcji krótkimi opisami.**

    a. **Wrażenie Stany Zjednoczone (Non-UltiPro)** aplikacja wymaga **identyfikator jednostki SAML** wartość, która została skopiowana, być zakodowane identyfikatorem uri. Aby uzyskać wartość identyfikatora uri zakodowane, użyj następującego łącza:**http://www.url-encode-decode.com/**.

    b. Po pierwsze identyfikator uri zakodowaną wartość połączyć ją z **adres URL odpowiedzi** wymienionych poniżej -

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. Wklej powyższych wartości w **adres URL odpowiedzi** textbox w **domeny wrażenie Stany Zjednoczone (Non-UltiPro) i adresy URL** sekcji.

    ![Konfiguracja Stanów Zjednoczonych (z systemem innym niż UltiPro) z punktu widzenia użytkownika](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

7. W innym oknie przeglądarki należy zalogować się jako administrator do witryny firmy wrażenie Stany Zjednoczone (Non-UltiPro).

8. Na głównym pasku narzędzi, kliknij przycisk **ustawienia konta**.

    ![Użytkownik Stanów Zjednoczonych (Non-UltiPro) z punktu widzenia użytkownika](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

9. Na **ustawienia konta** wykonaj następujące czynności:

    ![Użytkownik Stanów Zjednoczonych (Non-UltiPro) z punktu widzenia użytkownika](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. W **nazwa firmy** tekstowym, wpisz nazwę **firmy**.
    
    b. W **nazwa konta** tekstowym, wpisz nazwę **konta**.

    c. W **domyślne odpowiedzi na wiadomość E-mail** tekstu wpisz prawidłową **E-mail**.

    d. Wybierz **dostawcy tożsamości logowania jednokrotnego** jako **SAML 2.0**.

10. Na **konfiguracji logowania jednokrotnego** wykonaj następujące czynności:

    ![SSOConfig Stanów Zjednoczonych (z systemem innym niż UltiPro) z punktu widzenia użytkownika](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Wybierz **typu NameID SAML** jako **E-mail**.

    b. W **Nazwa konfiguracji logowania jednokrotnego** tekstowym, wpisz nazwę użytkownika **konfiguracji**.
    
    c. W **Nazwa dostawcy tożsamości** pole tekstowe, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure. 

    d. W **textbox domeny SAML**, wprowadź domenę, takich jak **@contoso.com**.

    e. Polecenie **Przekaż ponownie** przekazać **XML metadanych** pliku.

    f. Kliknij przycisk **aktualizacji**.


> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/perceptionunitedstates-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/perceptionunitedstates-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/perceptionunitedstates-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/perceptionunitedstates-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>Tworzenie użytkownika testowego z punktu widzenia użytkownika Stany Zjednoczone (Non-UltiPro)

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Stanach Zjednoczonych z punktu widzenia użytkownika (Non-UltiPro). Praca z [zespołem pomocy technicznej z punktu widzenia użytkownika Stany Zjednoczone (Non-UltiPro)](http://www.ultimatesoftware.com/Contact/ContactUs) Aby dodać użytkowników na platformie wrażenie Stany Zjednoczone (Non-UltiPro).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do Stanów Zjednoczonych z punktu widzenia użytkownika (Non-UltiPro).

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta do Stanów Zjednoczonych z punktu widzenia użytkownika (inne niż UltiPro), wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **wrażenie Stany Zjednoczone (Non-UltiPro)**.

    ![Link z punktu widzenia użytkownika Stany Zjednoczone (Non-UltiPro) na liście aplikacji](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka wrażenie Stany Zjednoczone (Non-UltiPro) w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji z punktu widzenia użytkownika Stany Zjednoczone (Non-UltiPro).
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

