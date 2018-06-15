---
title: 'Samouczek: Integracji Azure Active Directory z Yodeck | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: ba48c5d14775004e22dd67a2932c8969e6dfdd6d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354184"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Samouczek: Integracji Azure Active Directory z Yodeck

Z tego samouczka dowiesz się integrowanie Yodeck z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Yodeck zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Yodeck.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Yodeck (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Yodeck, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Yodeck logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Yodeck z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-yodeck-from-the-gallery"></a>Dodawanie Yodeck z galerii
Aby skonfigurować integrację usługi Azure AD Yodeck, należy dodać Yodeck z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Yodeck z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Yodeck**, wybierz pozycję **Yodeck** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Yodeck na liście wyników](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Yodeck w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Yodeck jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Yodeck musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Yodeck, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Yodeck](#create-a-yodeck-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Yodeck połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Yodeck.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Yodeck, wykonaj następujące czynności:**

1. W portalu Azure na **Yodeck** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_samlbase.png)

3. Na **Yodeck domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny Yodeck pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_url.png)

    W **identyfikator jednostki** tekstowym, wpisz adres URL: `https://app.yodeck.com/api/v1/account/metadata/`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny Yodeck pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL: `https://app.yodeck.com/login`

5. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk Kopiuj, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go do Notatnika.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_certificate.png)

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-yodeck-tutorial/tutorial_general_400.png)
    
7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Yodeck.

8. Polecenie **ustawienia użytkownika** formularza prawym górnym rogu strony i wybierz opcję **ustawienia konta**.

    ![Konfiguracja Yodeck](./media/active-directory-saas-yodeck-tutorial/configure1.png)

9. Wybierz **SAML** i wykonaj następujące czynności:

    ![Konfiguracja Yodeck](./media/active-directory-saas-yodeck-tutorial/configure2.png)

    a. Wybierz **importu z adresu URL**.

    b. W **adres URL** pole tekstowe, Wklej **adres Url metadanych Federacji aplikacji** wartość, która została skopiowana z portalu Azure i kliknij przycisk **importu**.
    
    c. Po zaimportowaniu **adres Url metadanych Federacji aplikacji**, pozostałe pola automatycznie wypełnić.

    d. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-yodeck-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-yodeck-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-yodeck-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-yodeck-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-yodeck-test-user"></a>Tworzenie użytkownika testowego Yodeck

Aby umożliwić użytkownikom usługi Azure AD zalogować się do Yodeck, musi być przygotowana do Yodeck.
W przypadku Yodeck Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Yodeck jako administrator.

2. Polecenie **ustawienia użytkownika** formularza prawym górnym rogu strony i wybierz opcję **użytkowników**.

    ![Dodawanie pracownika](./media/active-directory-saas-yodeck-tutorial/user1.png)

3. Polecenie **+ użytkownika** otworzyć **szczegóły użytkownika** kartę.

    ![Dodawanie pracownika](./media/active-directory-saas-yodeck-tutorial/user2.png)

4. Na **szczegóły użytkownika** okna dialogowego strony, należy wykonać następujące czynności:

    ![Dodawanie pracownika](./media/active-directory-saas-yodeck-tutorial/user3.png)

    a. W **imię** tekstowym, wpisz imię użytkownika, takich jak **Britta**.

    b. W **nazwisko** tekstowym, wpisz nazwisko użytkownika, takich jak **Simona**.

    c. W **E-mail** tekstowym, wpisz adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    d. Wybierz odpowiednie **uprawnień konta** opcji zgodnie z wymaganiami organizacji.
    
    e. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Yodeck.

![Przypisanie roli użytkownika][200]

**Aby przypisać Simona Britta Yodeck, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **Yodeck**.

    ![Łącze Yodeck na liście aplikacji](./media/active-directory-saas-yodeck-tutorial/tutorial_yodeck_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Yodeck w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Yodeck.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-yodeck-tutorial/tutorial_general_203.png

