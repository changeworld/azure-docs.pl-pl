---
title: 'Samouczek: Integracji Azure Active Directory z usługi GitHub | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i usługi GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 9263be24f883d8a02735e757f3ac02b54960f61f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227398"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Samouczek: Integracji Azure Active Directory z usługi GitHub

Z tego samouczka dowiesz się integrowanie GitHub z usługi Azure Active Directory (Azure AD).

Integrowanie usługi GitHub z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do usługi GitHub.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do usługi GitHub (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługi GitHub, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- GitHub logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie GitHub z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-github-from-the-gallery"></a>Dodawanie GitHub z galerii
Aby skonfigurować integrację usługi Azure AD GitHub, musisz dodać GitHub z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać GitHub z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **GitHub**, wybierz pozycję **GitHub** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![GitHub z listy wyników](./media/github-tutorial/tutorial_github_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z usługi GitHub w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w serwisie GitHub jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązane użytkownika w usłudze GitHub musi określone.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z usługi GitHub, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego GitHub](#create-a-github-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta GitHub połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji GitHub.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z usługi GitHub, wykonaj następujące czynności:**

1. W portalu Azure na **GitHub** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/github-tutorial/tutorial_github_samlbase.png)

3. Na **GitHub domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i GitHub domeny pojedynczy informacje logowania jednokrotnego](./media/github-tutorial/tutorial_github_url.png)

    a. W **Zaloguj się na adres URL** tekstowym, wpisz adres URL, używając następującego wzorca: `https://github.com/orgs/<entity-id>/sso`

    b. W **identyfikator jednostki** tekstowym, wpisz adres URL, używając następującego wzorca: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Należy pamiętać, że nie są one rzeczywiste wartości. Należy zaktualizować te wartości przy użyciu rzeczywistego konta na adres URL i identyfikator. W tym miejscu zalecamy można używać unikatowej wartości ciągu w identyfikatorze. Przejdź do sekcji GitHub administratora dotyczące pobierania tych wartości.

4. Na **atrybuty użytkownika** zaznacz **identyfikator użytkownika** jako user.mail.

    ![Konfigurowanie rejestracji jednokrotnej](./media/github-tutorial/tutorial_github_attribute_new01.png)

5. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/github-tutorial/tutorial_github_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/github-tutorial/tutorial_general_400.png)

7. Na **konfiguracji GitHub** , kliknij przycisk **skonfigurować GitHub** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja usługi GitHub](./media/github-tutorial/tutorial_github_configure.png) 

8. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny GitHub organizacji jako administrator.

9. Przejdź do **ustawienia** i kliknij przycisk **zabezpieczeń**

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_03.png)

10. Sprawdź **uwierzytelnianie Włącz SAML** pole ujawniania pola konfiguracji logowania jednokrotnego. Następnie zaktualizuj pojedynczy adres URL logowania w konfiguracji usługi Azure AD za pomocą pojedynczego logowania jednokrotnego wartość adresu URL.

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_13.png)

11. Określ następujące pola:

    a. W **Zaloguj się na adres URL** pole tekstowe, Wklej **SAML pojedynczy znak na adres URL usługi** wartość, która została skopiowana z portalu Azure.

    b. W **wystawcy** pole tekstowe, Wklej **identyfikator jednostki SAML** wartość, która została skopiowana z portalu Azure.

    c. Otwórz certyfikat pobrany z portalu Azure w programie Notatnik wkleić zawartości do **certyfikatu publicznego** pola tekstowego.

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_051.png)

12. Polecenie **konfiguracji testu SAML** potwierdzić, że nie wystąpiły błędy sprawdzania poprawności lub błędy podczas rejestracji Jednokrotnej.

    ![Ustawienia](./media/github-tutorial/tutorial_github_config_github_06.png)

13. Kliknij pozycję **Zapisz**

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/github-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/github-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/github-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/github-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-github-test-user"></a>Tworzenie użytkownika testowego GitHub

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w witrynie GitHub. GitHub obsługę automatycznego użytkownika, który jest domyślnie włączone. Więcej informacji można znaleźć [tutaj](github-provisioning-tutorial.md) na temat konfigurowania użytkowników automatycznego inicjowania obsługi administracyjnej.

**Jeśli trzeba ręcznie utworzyć użytkownika, należy wykonać następujące kroki:**

1. Zaloguj się do witryny GitHub firmy jako administrator.

2. Kliknij przycisk **osób**.

    ![Osoby](./media/github-tutorial/tutorial_github_config_github_08.png "osób")

3. Kliknij przycisk **Członkowskie zaproszenia**.

    ![Zaprosić użytkowników](./media/github-tutorial/tutorial_github_config_github_09.png "zaprosić użytkowników")

4. Na **Członkowskie zaproszenia** okna dialogowego strony, należy wykonać następujące czynności:

    a. W **E-mail** tekstowym, wpisz adres e-mail konta Simona Britta.

    ![Zaproś inne osoby](./media/github-tutorial/tutorial_github_config_github_10.png "Zaproś inne osoby")

    b. Kliknij przycisk **wysłać zaproszenie**.

    ![Zaproś inne osoby](./media/github-tutorial/tutorial_github_config_github_11.png "Zaproś inne osoby")

    > [!NOTE]
    > Właścicielem konta usługi Azure Active Directory otrzymasz wiadomość e-mail, a następnie kliknij łącze, aby potwierdzić swoje konto, zanim staje się aktywny.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do usługi GitHub.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta GitHub, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji zaznacz **GitHub**.

    ![Łącze GitHub na liście aplikacji](./media/github-tutorial/tutorial_github_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka GitHub w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji GitHub.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/github-tutorial/tutorial_general_01.png
[2]: ./media/github-tutorial/tutorial_general_02.png
[3]: ./media/github-tutorial/tutorial_general_03.png
[4]: ./media/github-tutorial/tutorial_general_04.png

[100]: ./media/github-tutorial/tutorial_general_100.png

[200]: ./media/github-tutorial/tutorial_general_200.png
[201]: ./media/github-tutorial/tutorial_general_201.png
[202]: ./media/github-tutorial/tutorial_general_202.png
[203]: ./media/github-tutorial/tutorial_general_203.png

