---
title: 'Samouczek: Integracji Azure Active Directory z ScreenSteps | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 444bc2d0724f8e7efc453c9efa577908822a4df2
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Samouczek: Integracji Azure Active Directory z ScreenSteps

Z tego samouczka dowiesz się integrowanie ScreenSteps z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD ScreenSteps zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do ScreenSteps.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do ScreenSteps (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z ScreenSteps, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- ScreenSteps logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie ScreenSteps z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-screensteps-from-the-gallery"></a>Dodawanie ScreenSteps z galerii
Aby skonfigurować integrację usługi Azure AD ScreenSteps, należy dodać ScreenSteps z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać ScreenSteps z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **ScreenSteps**, wybierz pozycję **ScreenSteps** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![ScreenSteps na liście wyników](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z ScreenSteps w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w ScreenSteps jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w ScreenSteps musi się.

W ScreenSteps, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z ScreenSteps, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego ScreenSteps](#create-a-screensteps-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta ScreenSteps połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji ScreenSteps.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z ScreenSteps, wykonaj następujące czynności:**

1. W portalu Azure na **ScreenSteps** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_samlbase.png)

3. Na **ScreenSteps domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny ScreenSteps pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_url.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość rzeczywista logowania jednokrotnego adres URL, który znajduje się w dalszej części tego samouczka. 

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-screensteps-tutorial/tutorial_general_400.png)

6. Na **konfiguracji ScreenSteps** , kliknij przycisk **skonfigurować ScreenSteps** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja ScreenSteps](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_configure.png) 

7. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy ScreenSteps jako administrator.

8. Kliknij przycisk **ustawienia konta**.

    ![Zarządzanie kontami](./media/active-directory-saas-screensteps-tutorial/ic778523.png "Zarządzanie kontami")

9. Kliknij przycisk **logowanie jednokrotne**.

    ![Zdalne uwierzytelnianie](./media/active-directory-saas-screensteps-tutorial/ic778524.png "uwierzytelniania zdalnego")

10. Kliknij przycisk **Tworzenie końcowego rejestracji jednokrotnej**.

    ![Zdalne uwierzytelnianie](./media/active-directory-saas-screensteps-tutorial/ic778525.png "uwierzytelniania zdalnego")

11. W **utworzyć jeden znak na punkt końcowy** sekcji, wykonaj następujące czynności:

    ![Tworzenie punktu końcowego uwierzytelniania](./media/active-directory-saas-screensteps-tutorial/ic778526.png "Tworzenie punktu końcowego uwierzytelniania")
    
    a. W **tytuł** tekstowym, wpisz tytuł.
    
    b. Z **tryb** listy, wybierz **SAML**.
    
    c. Kliknij przycisk **Utwórz**.

12. **Edytuj** nowy punkt końcowy.

    ![Edytuj punktu końcowego](./media/active-directory-saas-screensteps-tutorial/ic778528.png "edycji punktu końcowego")

13. W **edytować jeden znak na punkt końcowy** sekcji, wykonaj następujące czynności:

    ![Uwierzytelniania zdalnego punktu końcowego](./media/active-directory-saas-screensteps-tutorial/ic778527.png "uwierzytelniania zdalnego punktu końcowego")

    a. Kliknij przycisk **Przekaż nowy plik certyfikatu SAML**, a następnie przekaż certyfikat, który został pobrany z portalu Azure.
    
    b. Wklej **SAML pojedynczy znak na adres URL usługi** wartość, która została skopiowana z portalu Azure do **zdalnego adresu URL logowania** pola tekstowego.
    
    c. Wklej **Sign-Out URL** wartość, która została skopiowana z portalu Azure do **adres URL wylogowania** pola tekstowego.
    
    d. Wybierz **grupy** do przypisywania użytkowników do po ich udostępnieniu.
    
    e. Kliknij przycisk **aktualizacji**.

    f. Kopiuj **adres URL klienta SAML** do Schowka i Wklej do **adres URL logowania** textbox w **ScreenSteps domeny i adres URL** sekcji.
    
    g. Wróć do **Edytuj Endpoint rejestracji jednokrotnej**.
    
    h. Kliknij przycisk **domyślnym kontem** przycisk, aby używać tego punktu końcowego dla wszystkich użytkowników, którzy logują ScreenSteps. Alternatywnie możesz kliknąć **dodać do lokacji** przycisk, aby używać tego punktu końcowego dla określonych witryn internetowych w **ScreenSteps**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-screensteps-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-screensteps-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-screensteps-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-screensteps-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-screensteps-test-user"></a>Tworzenie użytkownika testowego ScreenSteps

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w ScreenSteps. Praca z [zespołem pomocy technicznej klienta ScreenSteps](https://www.screensteps.com/contact) Aby dodać użytkowników do platformy ScreenSteps. Użytkownicy muszą utworzyć i aktywowana, aby użyć rejestracji jednokrotnej. 

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu ScreenSteps.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta ScreenSteps, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **ScreenSteps**.

    ![Łącze ScreenSteps na liście aplikacji](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka ScreenSteps w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji ScreenSteps.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_203.png

