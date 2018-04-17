---
title: 'Samouczek: Integracji Azure Active Directory z Elium | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: jeedes
ms.openlocfilehash: 2c41b2c297d8d89554950e8c77201ff5bedf743e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Samouczek: Integracji Azure Active Directory z Elium

Z tego samouczka dowiesz się integrowanie Elium z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Elium zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Elium.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Elium (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Elium, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Elium logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Elium z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-elium-from-the-gallery"></a>Dodawanie Elium z galerii
Aby skonfigurować integrację usługi Azure AD Elium, należy dodać Elium z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Elium z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Elium**, wybierz pozycję **Elium** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Elium na liście wyników](./media/active-directory-saas-elium-tutorial/tutorial_elium_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Elium w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Elium jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Elium musi się.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Elium, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Elium](#create-an-elium-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Elium połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Elium.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Elium, wykonaj następujące czynności:**

1. W portalu Azure na **Elium** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-elium-tutorial/tutorial_elium_samlbase.png)

3. Na **Elium domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny Elium pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-elium-tutorial/tutorial_elium_url.png)

    a. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<platform-domain>.elium.com/login/saml2/acs`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny Elium pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-elium-tutorial/tutorial_elium_url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: ` https://<platform-domain>.elium.com/login/saml2/login`
     
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Otrzymasz te wartości z **pliku metadanych SP** można pobrać `https://<platform-domain>.elium.com/login/saml2/metadata`, który znajduje się w dalszej części tego samouczka.

5. Aplikacja Elium oczekuje potwierdzenia języka SAML w określonym formacie, musisz dodać mapowania atrybutu niestandardowego do konfiguracji atrybuty tokenu SAML. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-elium-tutorial/tutorial_attribute.png)

6. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na powyższej ilustracji i wykonaj następujące czynności:
           
    | Nazwa atrybutu | Wartość atrybutu |   
    | ---------------| ----------------|
    | e-mail   |User.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | Firmy| user.companyname|
    
    > [!NOTE]
    > Są to domyślne oświadczeń. **Oświadczenie adresu e-mail jest wymagany tylko**. JIT również Inicjowanie obsługi tylko wiadomości e-mail oświadczenia jest obowiązkowe. Inne oświadczenia niestandardowe mogą się różnić od jednego klienta platformy do innej platformie klienta.

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-elium-tutorial/tutorial_attribute_04.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-elium-tutorial/tutorial_attribute_05.png)

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. Pozostaw puste przestrzeni nazw.
    
    e. Kliknij przycisk **OK**. 

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-elium-tutorial/tutorial_elium_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-elium-tutorial/tutorial_general_400.png)
    
7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Elium.

8. Polecenie **profilu użytkownika** w prawym górnym rogu i wybierz **administracji**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-elium-tutorial/user1.png)

9. Wybierz **zabezpieczeń** kartę.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-elium-tutorial/user2.png)

10. Przewiń w dół do **logowanie jednokrotne (SSO)** sekcji, a następnie wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-elium-tutorial/user3.png)

    a. Skopiuj wartość **Sprawdź uwierzytelnianie SAML2 działa w przypadku konta** i wklej go w **adres URL logowania** textbox w **Elium domeny i adres URL** części platformy Azure Portal.

    > [!NOTE]
    > Po skonfigurowaniu logowania jednokrotnego, aby zawsze dostęp do domyślnej strony logowania zdalnego z następującego adresu URL: `https://<platform_domain>/login/regular/login` 

    b. Wybierz **federacyjnego włączyć SAML2** wyboru.

    c. Wybierz **JIT inicjowania obsługi administracyjnej** wyboru.

    d. Otwórz **metadanych SP** , klikając **Pobierz** przycisku.

    e. Wyszukaj **identyfikator jednostki** w **metadanych SP** pliku, skopiuj **identyfikator jednostki** wartości, a następnie wklej je **identyfikator** textbox w  **Adresy URL i domeny Elium** sekcji w portalu Azure. 

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-elium-tutorial/user4.png)

    f. Wyszukaj **AssertionConsumerService** w **metadanych SP** pliku, skopiuj **lokalizacji** wartości, a następnie wklej je **adres URL odpowiedzi** pole tekstowe na **Elium domeny i adres URL** sekcji w portalu Azure.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-elium-tutorial/user5.png)

    g. Otwórz plik metadanych pobranych z portalu Azure do Notatnika, skopiuj zawartość i wklej ją do **metadanych IdP** pola tekstowego.

    h. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-elium-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-elium-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-elium-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-elium-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-an-elium-test-user"></a>Tworzenie użytkownika testowego Elium

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Elium. Elium obsługę w czasie, który jest domyślnie włączone. Nie ma elementu akcji można w tej sekcji. Nowy użytkownik został utworzony podczas próby dostępu Elium, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli trzeba ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Elium](mailto:support@elium.com).

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Elium.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Elium, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Elium**.

    ![Łącze Elium na liście aplikacji](./media/active-directory-saas-elium-tutorial/tutorial_elium_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Test rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Elium w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Elium.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-elium-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-elium-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-elium-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-elium-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-elium-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-elium-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-elium-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-elium-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-elium-tutorial/tutorial_general_203.png

