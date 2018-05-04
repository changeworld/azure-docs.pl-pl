---
title: 'Samouczek: Integracji Azure Active Directory z Fluxx Labs | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i laboratoriów Fluxx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: jeedes
ms.openlocfilehash: 0bba820c14c5eddc6db99923e3fb1de58c110f4c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Samouczek: Integracji Azure Active Directory z Fluxx Labs

Z tego samouczka dowiesz się integrowanie Fluxx laboratoriów w usłudze Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Fluxx Labs zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do laboratoriów Fluxx.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do laboratoriów Fluxx (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Fluxx Labs, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Laboratoria Fluxx logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Fluxx Labs z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-fluxx-labs-from-the-gallery"></a>Dodawanie Fluxx Labs z galerii
Aby skonfigurować integrację usługi Azure AD Fluxx Labs, należy dodać Fluxx Labs z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Fluxx Labs z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Fluxx Labs**, wybierz pozycję **Fluxx Labs** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Laboratoria Fluxx na liście wyników](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Fluxx laboratoriów w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w laboratoriach Fluxx jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w laboratoriach Fluxx musi się.

W laboratoriach Fluxx, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Fluxx Labs, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Fluxx Labs](#create-a-fluxx-labs-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Labs Fluxx, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Fluxx Labs.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Fluxx Labs, wykonaj następujące czynności:**

1. W portalu Azure na **Fluxx Labs** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

3. Na **Fluxx Labs domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny Labs Fluxx pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca:

    | Środowisko | Wzorzec URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io` |
    | Produkcja wstępna | `https://<subdomain>.preprod.fluxxlabs.com`|
        
    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca:

    | Środowisko | Wzorzec URL|
    |-------------|------------|
    | Produkcja | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Produkcja wstępna | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|
        
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator i adres URL odpowiedzi, należy zaktualizować te wartości. Skontaktuj się z [Fluxx Labs obsługuje zespołu](mailto:travis@fluxxlabs.com) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_400.png)

6. Na **Fluxx Labs konfiguracji** , kliknij przycisk **skonfigurować Labs Fluxx** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Fluxx Labs konfiguracji](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Fluxx Labs.

8. Wybierz **Admin** poniżej **ustawienia** sekcji.

    ![Fluxx Labs konfiguracji](./media/active-directory-saas-fluxxlabs-tutorial/config1.png)

9. Panel administracyjny, wybierz **dodatków plug-in** > **integracji** , a następnie wybierz **SAML SSO-(Disabled)**

    ![Fluxx Labs konfiguracji](./media/active-directory-saas-fluxxlabs-tutorial/config2.png)
    
10. W sekcji atrybutu wykonaj następujące czynności:
    
    ![Fluxx Labs konfiguracji](./media/active-directory-saas-fluxxlabs-tutorial/config3.png)

    a. Wybierz **logowania jednokrotnego SAML** wyboru.

    b. W **ścieżka żądania** pole tekstowe, typ **/auth/saml**.

    c. W **ścieżki wywołania zwrotnego** pole tekstowe, typ **/auth/saml/callback**.

    d. W **potwierdzenia konsumenta usługi Url(Single Sign-On URL)** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.

    e. W **odbiorców (identyfikator jednostki SP)** pole tekstowe, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure.

    f. Otwórz w Notatniku certyfikatu zakodowanego base-64, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikat dostawcy tożsamości** pola tekstowego.

    g. W **identyfikator nazwy formatu** pole tekstowe, wprowadź wartość `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    h. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Gdy zawartość zapisane, pole zostanie wyświetlona pusta zabezpieczeń, ale wartość została zapisana w konfiguracji.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
  
### <a name="create-a-fluxx-labs-test-user"></a>Tworzenie użytkownika testowego Fluxx Labs

Aby umożliwić użytkownikom usługi Azure AD do logowania się w laboratoriach Fluxx, muszą mieć przydzielone do laboratoriów Fluxx. W przypadku Fluxx Labs Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Fluxx Labs jako administrator.

2. Kliknij poniżej wyświetlana **ikona**.

    ![Fluxx Labs konfiguracji](./media/active-directory-saas-fluxxlabs-tutorial/config6.png)

3. Na pulpicie nawigacyjnym, kliknij poniżej wyświetlanych ikonę, aby otworzyć **nowych osób** karty.

    ![Fluxx Labs konfiguracji](./media/active-directory-saas-fluxxlabs-tutorial/config4.png)

4. Na **nowych osób** sekcji, wykonaj następujące czynności:
    
    ![Fluxx Labs konfiguracji](./media/active-directory-saas-fluxxlabs-tutorial/config5.png)

    a. Laboratoria Fluxx Użyj adresu e-mail jako identyfikator unikatowy dla logowania jednokrotnego logowania. Wypełnij **UID logowania jednokrotnego** pole z adres e-mail użytkownika zgodnego z adresem e-mail, używają jako Zaloguj się za pomocą logowania jednokrotnego.

    b. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do laboratoriów Fluxx.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Fluxx Labs, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Fluxx Labs**.

    ![Łącze Fluxx Labs na liście aplikacji](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Test rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Fluxx laboratoriów w panelu dostępu należy powinien pobrać automatycznie zalogowane do laboratoriów Fluxx aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_203.png
