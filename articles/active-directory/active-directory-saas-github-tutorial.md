---
title: 'Samouczek: Integracji Azure Active Directory z usługi GitHub | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i usługi GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4395bd95-05de-4deb-87a5-dc3bc8ac4d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: ef9d0bb112738e9abea5894fb0f321368b7fc26e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Samouczek: Integracji Azure Active Directory z usługi GitHub

Z tego samouczka dowiesz się integrowanie GitHub z usługi Azure Active Directory (Azure AD).

Integrowanie usługi GitHub z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do usługi GitHub
- Umożliwia użytkownikom automatycznie pobrać zalogowane do usługi GitHub (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu zarządzania Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługi GitHub, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- GitHub jednokrotnego włączone subskrypcji


> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.


Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie GitHub z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne


## <a name="adding-github-from-the-gallery"></a>Dodawanie GitHub z galerii
Aby skonfigurować integrację usługi Azure AD GitHub, musisz dodać GitHub z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać GitHub z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu zarządzania Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Kliknij przycisk **Dodaj** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **witryną GitHub.com**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search02.png)

5. W panelu wyników wybierz **GitHub**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search_result02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z usługi GitHub w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w serwisie GitHub jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązane użytkownika w usłudze GitHub musi określone.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w witrynie GitHub.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z usługi GitHub, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego GitHub](#creating-a-GitHub-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta GitHub połączonego z jej reprezentacji usługi Azure AD.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu zarządzania Azure i skonfigurować logowanie jednokrotne w aplikacji GitHub.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z usługi GitHub, wykonaj następujące czynności:**

1. W portalu zarządzania Azure na **GitHub** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okna dialogowego, jako **tryb** wybierz **na języku SAML logowania jednokrotnego** Włącz funkcji logowania jednokrotnego.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-github-tutorial/tutorial_github_01.png)

3. Na **GitHub domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-github-tutorial/tutorial_github_saml011.png)

    a. W **adres URL logowania** tekstowym, wpisz wartość, jak: `https://github.com/orgs/<entity-id>/sso`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://github.com/orgs/<entity-id>`

    > [!NOTE] 
    > Należy pamiętać, że nie są one rzeczywiste wartości. Należy zaktualizować te wartości z rzeczywistego uwagi na URL i identyfikator. W tym miejscu zalecamy można używać unikatowej wartości ciągu w identyfikatorze. Przejdź do sekcji GitHub administratora dotyczące pobierania tych wartości. 

4. Na **atrybuty użytkownika** zaznacz **identyfikator użytkownika** jako user.mail.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-github-tutorial/tutorial_github_attribute_new01.png)
    
5. Na **certyfikat podpisywania SAML** kliknij **Utwórz nowy certyfikat**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-github-tutorial/tutorial_github_03.png)

6. Na **Tworzenie nowego świadectwa** okna dialogowego, kliknij ikonę kalendarza i wybierz **Data wygaśnięcia**. Następnie kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-github-tutorial/tutorial_general_300.png)

7. Na **certyfikat podpisywania SAML** wybierz opcję **uaktywnić nowego świadectwa** i kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-github-tutorial/tutorial_github_04.png)

8. W oknie podręcznym **certyfikat przerzucania** okna, kliknij przycisk **OK**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-github-tutorial/tutorial_general_400.png)

9. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-github-tutorial/tutorial_github_05.png) 

10. Na **konfiguracji GitHub** , kliknij przycisk **skonfigurować GitHub** otworzyć **Konfigurowanie logowania jednokrotnego** okna.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-github-tutorial/tutorial_github_06.png) 

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-github-tutorial/tutorial_github_07.png)

11. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny GitHub organizacji jako administrator.

12. Przejdź do **ustawienia** i kliknij przycisk **zabezpieczeń**

    ![Ustawienia](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_03.png)

13. Sprawdź **uwierzytelnianie Włącz SAML** pole ujawniania pola konfiguracji logowania jednokrotnego. Następnie zaktualizuj pojedynczy adres URL logowania w konfiguracji usługi Azure AD za pomocą pojedynczego logowania jednokrotnego wartość adresu URL.

    ![Ustawienia](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_13.png)

14. Określ następujące pola:

    a. **Zaloguj się na adres URL**: wprowadź **SAML rejestracji jednokrotnej adres URL usługi** z **skonfigurować GitHub** sekcję na temat usługi Azure AD

    b. **Wystawca**: wprowadź **identyfikator jednostki SAML** z **skonfigurować GitHub** sekcję na temat usługi Azure AD

    c. **Certyfikat publiczny**: Otwórz pobranego certyfikatu z usługi Azure AD w programie Notatnik i skopiuj zawartość, w tym "Certyfikat BEGIN" i "END CERTIFICATE"

    ![Ustawienia](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_051.png)

15. Polecenie **konfiguracji testu SAML** potwierdzić, że nie wystąpiły błędy sprawdzania poprawności lub błędy podczas rejestracji Jednokrotnej.

    ![Ustawienia](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_06.png)

16. Kliknij pozycję **Zapisz**

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu zarządzania Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu zarządzania Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_01.png) 

2. Przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy** do wyświetlenia na liście Użytkownicy.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_02.png) 

3. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**. 


### <a name="creating-a-github-test-user"></a>Tworzenie użytkownika testowego GitHub

Aby włączyć użytkowników usługi Azure AD zalogować się do usługi GitHub, muszą mieć przydzielone w witrynie GitHub.  
W przypadku GitHub Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkowników, wykonaj następujące czynności:**

1. Zaloguj się do witryny GitHub firmy jako administrator.

2. Kliknij przycisk **osób**.

    ![Osoby](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_08.png "osób")

3. Kliknij przycisk **Członkowskie zaproszenia**.

    ![Zaprosić użytkowników](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_09.png "zaprosić użytkowników")

4. Na **Członkowskie zaproszenia** okna dialogowego strony, należy wykonać następujące czynności:

    a. W **E-mail** tekstowym, wpisz adres e-mail konta Simona Britta.

    ![Zaproś inne osoby](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_10.png "Zaproś inne osoby")
    
    b. Kliknij przycisk **wysłać zaproszenie**.

    ![Zaproś inne osoby](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_11.png "Zaproś inne osoby")

    > [!NOTE]
    > Właścicielem konta usługi Azure Active Directory otrzymasz wiadomość e-mail, a następnie kliknij łącze, aby potwierdzić swoje konto, zanim staje się aktywny.


### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do użycia usługi Azure logowania jednokrotnego za udostępnienie jej do usługi GitHub.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta GitHub, wykonaj następujące czynności:**

1. Otwórz widok aplikacji w portalu zarządzania Azure, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **witryną GitHub.com**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-github-tutorial/tutorial_github_search_result021.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    


### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka GitHub w panelu dostępu użytkownik powinien pobrać zalogowane do aplikacji GitHub. Można będzie można logować się jako organizacji konta muszą jednak następnie zaloguj się przy użyciu konta osobistego.


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-github-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-github-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-github-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-github-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-github-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-github-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-github-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-github-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-github-tutorial/tutorial_general_203.png
