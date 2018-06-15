---
title: 'Samouczek: Integracji Azure Active Directory z Dealpath | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Dealpath.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 51ace608-5a4f-48c0-9446-d9f86ad2e890
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: jeedes
ms.openlocfilehash: 2df638c8c75e07b9df9ace9fc420f0d36920bf8f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341930"
---
# <a name="tutorial-azure-active-directory-integration-with-dealpath"></a>Samouczek: Integracji Azure Active Directory z Dealpath

Z tego samouczka dowiesz się integrowanie Dealpath z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Dealpath zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Dealpath.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Dealpath (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Dealpath, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Dealpath logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Dealpath z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-dealpath-from-the-gallery"></a>Dodawanie Dealpath z galerii
Aby skonfigurować integrację usługi Azure AD Dealpath, należy dodać Dealpath z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Dealpath z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Dealpath**, wybierz pozycję **Dealpath** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Dealpath na liście wyników](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Dealpath w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Dealpath jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Dealpath musi się.

W Dealpath, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Dealpath, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Dealpath](#create-a-dealpath-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Dealpath połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Dealpath.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Dealpath, wykonaj następujące czynności:**

1. W portalu Azure na **Dealpath** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_samlbase.png)

3. Na **Dealpath domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny Dealpath pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://app.dealpath.com/account/login`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://api.dealpath.com/saml/metadata/<ID>`

    > [!NOTE] 
    > Identyfikator nie jest prawdziwe. Zaktualizuj tę wartość z rzeczywistego identyfikatora. Skontaktuj się z [zespołem pomocy technicznej klienta Dealpath](mailto:kenter@dealpath.com) można uzyskać wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-dealpath-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Dealpath** , kliknij przycisk **skonfigurować Dealpath** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_configure.png) 

7. W oknie przeglądarki innej witryny sieci web, zaloguj się do Dealpath jako Administrator.

8. W górnym rogu kliknij **narzędzi administracyjnych** i przejdź do **integracji**, a następnie w **uwierzytelnianie SAML 2.0** kliknij sekcję **ustawienia aktualizacji**:

    ![Konfiguracja Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_admin.png)

9. W **skonfigurować uwierzytelnianie SAML 2.0** wykonaj następujące czynności:

    ![Konfiguracja Dealpath](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_saml.png) 

    a. W **adres URL logowania jednokrotnego SAML** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.

    b. W **wystawcy dostawcy tożsamości** pole tekstowe, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure.

    c. Skopiuj zawartość pobranego **certificate(Base64)** plików w programie Notatnik, a następnie wklej go do **certyfikatu publicznego** pola tekstowego.

    d. Kliknij przycisk **zaktualizować ustawienia**.


> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-dealpath-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-dealpath-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-dealpath-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-dealpath-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-dealpath-test-user"></a>Tworzenie użytkownika testowego Dealpath

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Dealpath. Praca z [zespołem pomocy technicznej klienta Dealpath](mailto:kenter@dealpath.com) Aby dodać użytkowników do platformy Dealpath. Użytkownicy muszą być tworzone i aktywowana, aby użyć rejestracji jednokrotnej

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Dealpath.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Dealpath, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Dealpath**.

    ![Łącze Dealpath na liście aplikacji](./media/active-directory-saas-dealpath-tutorial/tutorial_dealpath_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Dealpath w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Dealpath.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dealpath-tutorial/tutorial_general_203.png

