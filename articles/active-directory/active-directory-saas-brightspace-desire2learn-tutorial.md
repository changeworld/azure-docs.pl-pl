---
title: 'Samouczek: Integracji Azure Active Directory z Brightspace przez Desire2Learn | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Brightspace przez Desire2Learn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e2d3065b-1f6c-4c45-af78-0d5da3266999
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: f3a0df337e8e52bacd30456a6236135c3561b210
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34338588"
---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Samouczek: Integracji Azure Active Directory z Brightspace przez Desire2Learn

Z tego samouczka dowiesz się integrowanie Brightspace przez Desire2Learn z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Brightspace przez Desire2Learn zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Brightspace przez Desire2Learn
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Brightspace przez Desire2Learn (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Brightspace przez Desire2Learn, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Brightspace przez Desire2Learn logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Brightspace przez Desire2Learn z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-brightspace-by-desire2learn-from-the-gallery"></a>Dodawanie Brightspace przez Desire2Learn z galerii
Aby skonfigurować integrację usługi Azure AD Brightspace przez Desire2Learn, należy dodać Brightspace przez Desire2Learn z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Brightspace przez Desire2Learn z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Brightspace przez Desire2Learn**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_brightspacebydesire2learn_search.png)

5. W panelu wyników wybierz **Brightspace przez Desire2Learn**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_brightspacebydesire2learn_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Brightspace przez Desire2Learn w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Brightspace przez Desire2Learn jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Brightspace przez Desire2Learn musi się.

W Brightspace przez Desire2Learn, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Brightspace przez Desire2Learn, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie Brightspace przez użytkownika testowego Desire2Learn](#creating-a-brightspace-by-desire2learn-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Brightspace przez Desire2Learn połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i konfigurowanie rejestracji jednokrotnej w sieci Brightspace przez aplikację Desire2Learn.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Brightspace przez Desire2Learn, wykonaj następujące czynności:**

1. W portalu Azure na **Brightspace przez Desire2Learn** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_brightspacebydesire2learn_samlbase.png)

3. Na **Brightspace Desire2Learn domeny i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_brightspacebydesire2learn_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca:
    | |
    |--|
    | `https://<companyname>.tenants.brightspace.com/samlLogin`|
    | `https://<companyname>.desire2learn.com/shibboleth-sp`|

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<companyname>.desire2learn.com/d2l/lp/auth/login/samlLogin.d2l`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [Brightspace przez zespół pomocy technicznej Desire2Learn](https://www.d2l.com/contact/) uzyskać te wartości.
 


4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_brightspacebydesire2learn_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_general_400.png)

6. Aby skonfigurować logowanie jednokrotne w **Brightspace przez Desire2Learn** stronie, musisz wysłać pobrany **XML metadanych** do [Brightspace przez zespół pomocy technicznej Desire2Learn](https://www.d2l.com/contact/).

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-brightspace-desire2learn-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-brightspace-desire2learn-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-brightspace-desire2learn-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-brightspace-desire2learn-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-brightspace-by-desire2learn-test-user"></a>Tworzenie Brightspace przez Desire2Learn użytkownika testowego

Aby włączyć użytkowników usługi Azure AD zalogować się do Brightspace przez Desire2Learn, ich muszą mieć przydzielone do Brightspace przez Desire2Learn.  

W przypadku Brightspace przez Desire2Learn, konta użytkowników muszą zostać utworzone przez użytkownika [Brightspace przez zespół pomocy technicznej Desire2Learn](https://www.d2l.com/contact/).

>[!NOTE]
>Aby użyć innych Brightspace Desire2Learn narzędzia do tworzenia konta użytkownika lub interfejsów API dostarczonych przez Brightspace przez Desire2Learn do świadczenia usługi Azure Active Directory kont użytkowników. 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Brightspace przez Desire2Learn.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Brightspace przez Desire2Learn, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Brightspace przez Desire2Learn**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_brightspacebydesire2learn_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Kliknięcie Brightspace Desire2Learn kafelka w panelu dostępu, możesz należy pobrać automatycznie zalogowane do Twojej Brightspace przez aplikację Desire2Learn.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-brightspace-desire2learn-tutorial/tutorial_general_203.png

