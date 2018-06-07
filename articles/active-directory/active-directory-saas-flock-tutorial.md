---
title: 'Samouczek: Integracji Azure Active Directory z stada | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i stada.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7b2c3ac5-17f1-49a0-8961-c541b258d4b1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 30bdc71c9403a8de50773cee68c86f3f49bd359a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655014"
---
# <a name="tutorial-azure-active-directory-integration-with-flock"></a>Samouczek: Integracji Azure Active Directory z stada

Z tego samouczka dowiesz się integrowanie stada w usłudze Azure Active Directory (Azure AD).

Całkującej stada z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do stada.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do stada (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z stada, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Stada logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie stada z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-flock-from-the-gallery"></a>Dodawanie stada z galerii
Aby skonfigurować integrację stada do usługi Azure AD, należy dodać stada z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać stada z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **stada**, wybierz pozycję **stada** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Stada na liście wyników](./media/active-directory-saas-flock-tutorial/tutorial_flock_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z stada w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w stada jest dla użytkownika, w usłudze Azure AD. Innymi słowy łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w stada musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z stada, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego stada](#create-a-flock-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta stada połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji stada.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z stada, wykonaj następujące czynności:**

1. W portalu Azure na **stada** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-flock-tutorial/tutorial_flock_samlbase.png)

3. Na **stada domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny stada pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-flock-tutorial/tutorial_flock_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.flock.com/`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<subdomain>.flock.com/`

    > [!NOTE]
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta stada](mailto:support@flock.com) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-flock-tutorial/tutorial_flock_certificate.png)

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-flock-tutorial/tutorial_general_400.png)

6. Na **konfiguracji stada** , kliknij przycisk **skonfigurować stada** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja stada](./media/active-directory-saas-flock-tutorial/tutorial_flock_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy stada.

8. Wybierz **uwierzytelniania** karcie z panelu nawigacji po lewej stronie, a następnie wybierz **uwierzytelnianie SAML**.

    ![Konfiguracja stada](./media/active-directory-saas-flock-tutorial/configure1.png)

9. W **uwierzytelnianie SAML** sekcji, wykonaj następujące czynności:

    ![Konfiguracja stada](./media/active-directory-saas-flock-tutorial/configure2.png)

    a. W **SAML 2.0 Endpoint(HTTP)** pole tekstowe, Wklej **SAML pojedynczy znak na adres URL usługi** wartość, która została skopiowana z portalu Azure.

    b. W **wystawcy dostawcy tożsamości** pole tekstowe, Wklej **identyfikator jednostki SAML** wartość, która została skopiowana z portalu Azure.

    c. Otwórz pobrany **Certificate(Base64)** z portalu Azure w programie Notatnik, wkleić zawartości do **certyfikatu publicznego** pola tekstowego.

    d. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-flock-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-flock-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-flock-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-flock-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-flock-test-user"></a>Tworzenie użytkownika testowego stada

Aby włączyć użytkowników usługi Azure AD zalogować się do stada, muszą mieć przydzielone do stada. W przypadku stada Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy stada jako administrator.

2. Kliknij przycisk **zespołu zarządzania** z panelu nawigacji po lewej stronie.

    ![Dodawanie pracownika](./media/active-directory-saas-flock-tutorial/user1.png)

3. Kliknij przycisk **dodać element członkowski** karcie, a następnie wybierz **członków zespołu**.

    ![Dodawanie pracownika](./media/active-directory-saas-flock-tutorial/user2.png)

4. Wprowadź adres e-mail użytkownika, takich jak **Brittasimon@contoso.com** , a następnie wybierz **Dodaj użytkowników**.

    ![Dodawanie pracownika](./media/active-directory-saas-flock-tutorial/user3.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do stada.

![Przypisanie roli użytkownika][200]

**Aby przypisać Simona Britta stada, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **stada**.

    ![Łącze stada na liście aplikacji](./media/active-directory-saas-flock-tutorial/tutorial_flock_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka stada w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji stada.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-flock-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flock-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flock-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flock-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-flock-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flock-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flock-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-flock-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-flock-tutorial/tutorial_general_203.png
